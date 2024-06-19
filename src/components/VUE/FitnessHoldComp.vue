<script setup>
import { ref, onMounted, onUnmounted, computed } from "vue";

const FitnessHoldData = ref([]);
const FitnessHoldCategoriesData = ref([]);

// fetcher alle fitnesshold
const baseEndpoint = `https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/
fitnesshold?acf_format=standard&per_page=100`;

async function fetchAllPosts(url) {
  let allPosts = [];
  let page = 1;
  let totalPages = 1;

  while (page <= totalPages) {
    const response = await fetch(`${url}&page=${page}`);
    const data = await response.json();
    allPosts = [...allPosts, ...data];
    totalPages = parseInt(response.headers.get("X-WP-TotalPages"));
    page++;
  }

  return allPosts;
}

function fetchSkansen() {
  fetchAllPosts(baseEndpoint)
    .then((data) => {
      FitnessHoldData.value = data;
      // console.log(data);
    })
    .catch((err) => console.error(err));
}

fetchSkansen();

// Fetcher alle categorier under fitnesshold, ved at benytte parent category id'et
const baseEndpointCategories = `https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/categories?acf_format=standard&parent=13`;

function fetchSubcategories() {
  fetch(baseEndpointCategories)
    .then((res) => res.json())
    .then((categories) => {
      FitnessHoldCategoriesData.value = categories;
      // console.log(categories);
    })
    .catch((err) => console.error(err));
}

fetchSubcategories();

// Laver variable til at kunne toggle overlayet
const isBigContainerVisible = ref(false);
// Laver en variable til at holde det valgte hold
const selectedHold = ref(null);

// laver en toggle der sætter værdien for containeren
const toggleBigContainer = (hold) => {
  selectedHold.value = hold;
  isBigContainerVisible.value = !isBigContainerVisible.value;
};

// Bruger computed til at kategorisere holdene efter deres category
const categorizedFitnessHoldData = computed(() => {
  const categorizedData = {};

  // Kører gennem dataen for hvert fitnesshold og kategoriserer dem efter kategori
  FitnessHoldData.value.forEach((hold) => {
    const categoryId = hold.categories[0];
    const categoryName = getCategoryName(categoryId);
    if (!categorizedData[categoryName]) {
      categorizedData[categoryName] = [];
    }
    categorizedData[categoryName].push(hold);
  });

  // Sortere kategorierne alfabetisk
  const sortedCategories = Object.keys(categorizedData).sort();
  const sortedCategorizedData = {};

  // Sortere hver kategori alfabetisk og sorter fitness holdene i hvert category
  sortedCategories.forEach((category) => {
    categorizedData[category].sort((a, b) => {
      if (a.title.rendered < b.title.rendered) return -1;
      if (a.title.rendered > b.title.rendered) return 1;
      return 0;
    });
    sortedCategorizedData[category] = categorizedData[category];
  });

  return sortedCategorizedData;
});

// Henter categorierne baseret på navnet tilknyttet categories id
function getCategoryName(categoryId) {
  const category = FitnessHoldCategoriesData.value.find(
    (cat) => cat.id === categoryId
  );
  return category ? category.name : "Andre";
}

// Bruger computed til at hente billedet på den første post i hvert categori, til brug i categorivælgeren
const categoryImages = computed(() => {
  const images = {};
  for (const category in categorizedFitnessHoldData.value) {
    const firstPost = categorizedFitnessHoldData.value[category][0];
    if (firstPost && firstPost.acf && firstPost.acf.holdbillede) {
      images[category] = firstPost.acf.holdbillede;
    }
  }
  return images;
});

// Funktion der scroller til den kategori der er klikket på
const scrollToCategory = (category) => {
  const categoryElement = document.getElementById(category);
  if (categoryElement) {
    categoryElement.scrollIntoView({ behavior: "smooth" });
  }
};

// Funktion der lukker overlayet hvis escape trykkes
const closeOverlayOnEscapeKey = (event) => {
  if (event.key === "Escape") {
    toggleBigContainer(null);
  }
};

// Eventlistener der lytter efter om escape trykkes på, mens overlayet er åbent
onMounted(() => {
  window.addEventListener("keydown", closeOverlayOnEscapeKey);
});

// Fjerner eventlistener hvis overlayet ikke er unmounted
onUnmounted(() => {
  window.removeEventListener("keydown", closeOverlayOnEscapeKey);
});
</script>

<template>
  <!-- Valg af categori -->
  <section class="categoryCont flex align wrap">
    <!-- Laver en article for hvert categori, med tilhørende billede hentet fra holdene -->
    <article
      v-for="(hold, category) in categorizedFitnessHoldData"
      :key="category"
      @click="scrollToCategory(category)"
      role="button"
      tabindex="0"
    >
      <img
        :src="categoryImages[category]"
        alt="Category Image"
        class="categoryImage"
      />
      <div class="categoryName flex column">
        <p v-html="category"></p>
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          class="arrow"
          viewBox="0 0 18 10"
          aria-hidden="true"
        >
          <path
            fill="#000"
            d="M9.636 9.636a1.252 1.252 0 0 1-1.77 0l-7.5-7.5a1.252 1.252 0 0 1 1.77-1.77l6.617 6.617L15.37.37a1.252 1.252 0 0 1 1.77 1.77l-7.5 7.5-.004-.004Z"
          />
        </svg>
      </div>
    </article>
  </section>

  <!-- Sectionen for hvert hold. Div med v-for bruges til at sortere efter categori -->
  <!-- id=category bruges til scroll functionen -->
  <div
    v-for="(holdbeskrivelse, category) in categorizedFitnessHoldData"
    :key="category"
    class="pageCont"
    :id="category"
  >
    <section>
      <div class="categoryNameCont">
        <h2 v-html="category"></h2>
      </div>
      <div class="flex wrap flexContainer">
        <div
          v-for="hold in holdbeskrivelse"
          :key="hold.id"
          class="container"
          @click="toggleBigContainer(hold)"
          role="button"
          tabindex="0"
        >
          <div class="nameCont">
            <p class="p-hero" v-html="hold.title.rendered"></p>
          </div>
          <div class="imgCont">
            <img
              :src="hold.acf.holdbillede"
              :alt="hold.title.rendered"
              class="image"
            />
            <div class="description">
              <p class="p-small">{{ hold.acf.kort_intro }}</p>
              <h1 class="dots">. . .</h1>
            </div>
          </div>
        </div>
      </div>
    </section>
    <hr />
  </div>

  <!-- Overlay sektion -->
  <!-- Bruger v-if til at sikre, den kun er vist hvis værdien er true, som den bliver når man klikker på holdet -->
  <div
    class="overlay"
    v-if="isBigContainerVisible"
    aria-modal="true"
    aria-labelledby="overlayTitle"
    role="dialog"
  >
    <div class="bigContainer">
      <div class="bigNameCont flex align">
        <!-- selectedHold bruges så informationen der vises matcher det hold man har klikket på -->
        <p
          class="p-hero"
          v-html="selectedHold?.title.rendered"
          id="overlayTitle"
        ></p>
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 384 512"
          @click="toggleBigContainer(null)"
          role="button"
          tabindex="0"
          aria-label="Close"
        >
          <path
            d="M342.6 150.6c12.5-12.5 12.5-32.8 0-45.3s-32.8-12.5-45.3 0L192 210.7 86.6 105.4c-12.5-12.5-32.8-12.5-45.3 0s-12.5 32.8 0 45.3L146.7 256 41.4 361.4c-12.5 12.5-12.5 32.8 0 45.3s32.8 12.5 45.3 0L192 301.3 297.4 406.6c12.5 12.5 32.8 12.5 45.3 0s12.5-32.8 0-45.3L237.3 256 342.6 150.6z"
          />
        </svg>
      </div>
      <div class="imgDescripCont flex align wrap">
        <img
          :src="selectedHold?.acf.holdbillede"
          :alt="selectedHold?.title.rendered"
          class="image"
        />
        <p v-html="selectedHold?.acf.intro_beskrivelse" class="p-small"></p>
      </div>
      <div class="flex wrap descriptionCont">
        <p
          class="description p-small"
          v-html="selectedHold?.acf.resten_af_beskrivelsen"
        ></p>
        <aside class="flex column">
          <p class="p-hero">Sådan deltager du:</p>
          <ul>
            <li v-if="selectedHold?.acf.adgang_1">
              {{ selectedHold?.acf.adgang_1 }}
            </li>
            <li v-if="selectedHold?.acf.adgang_2">
              {{ selectedHold?.acf.adgang_2 }}
            </li>
            <li v-if="selectedHold?.acf.adgang_3">
              {{ selectedHold?.acf.adgang_3 }}
            </li>
            <li v-if="selectedHold?.acf.adgang_4">
              {{ selectedHold?.acf.adgang_4 }}
            </li>
          </ul>
        </aside>
      </div>
      <div class="buttonCont flex">
        <slot />
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
@import "../../css/style.scss";
.categoryCont {
  background-color: $champagne;
  padding: 2%;
  gap: 10px;
  article {
    border-radius: $box-border-section;
    border: 1px solid $pure-white;
    width: 15%;
    position: relative;
    overflow: hidden;
    cursor: pointer;
    .categoryImage {
      border-radius: $box-border-section;
      filter: brightness(0.5);
      transition: filter 0.3s ease;
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
    .categoryName {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      width: 100%;
      z-index: 2;
      align-items: center;
      background-color: #00000050;
      color: $pure-white;
      padding: 5px;
      transition: top 0.3s ease;
      svg {
        display: none;
        height: 15px;
        width: 15px;
        path {
          fill: $pure-white;
        }
      }
    }

    &:hover {
      .categoryImage {
        filter: brightness(1);
      }
      .categoryName {
        background-color: $cornell-red;
        top: 82%;
        svg {
          display: flex;
        }
      }
    }
  }
  @media (max-width: 1025px) {
    article {
      width: 140px;
    }
  }
}

.pageCont {
  padding-top: 3%;
  section {
    max-width: 80%;
    padding-bottom: 20px;
    margin: auto;
    background-color: $pure-white;
    border-radius: $box-border-section;
    .categoryNameCont {
      background-color: $champagne;
      width: 40%;
      padding: 15px;
      padding-left: 20px;
      border-radius: $box-border-section 0px $box-border-section 0px;
      box-shadow: $box-shadow;
    }
    @media (max-width: 431px) {
      .categoryNameCont {
        width: 100%;
        border-radius: $box-border-section $box-border-section 0px 0px;
      }
    }
    .container {
      background-color: $champagne;
      margin: 20px 20px 0px 20px;
      padding: 5px;
      width: 250px;
      border-radius: $box-border-element;
      position: relative;
      cursor: pointer;
      .nameCont {
        background-color: $cornell-red;
        border-radius: $box-border-element;
        text-align: center;
        color: $pure-white;
        p {
          padding: 20px;
        }
      }
      .imgCont {
        position: relative;
        color: white;
        height: 150px;
        padding-bottom: 5px;
        .image {
          width: 100%;
          height: 100%;
          margin-top: 5px;
          object-fit: cover;
          border-radius: $box-border-element;
        }

        .description {
          display: none;

          .p-small {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            padding: 20px;
            padding-bottom: 5px;
            z-index: 1;
          }
          .dots {
            position: absolute;
            right: 5px;
            bottom: -15px;
            text-align: right;
            z-index: 2;
          }
        }
      }

      @media (max-width: 1025px) {
        .imgCont {
          height: 125px;
        }
      }
    }
    @media (max-width: 700px) {
      .flexContainer {
        justify-content: center;
        .container {
          width: 65%;
        }
      }
    }
    .container:hover {
      background-color: $denim;
      .imgCont {
        .image {
          filter: brightness(0.3);
        }
        .description {
          display: block;
        }
      }
    }
  }
  hr {
    border: 1px solid $pure-white;
    width: 60%;
    margin: 3% auto 0% auto;
  }
}

.overlay {
  position: fixed;
  height: 100%;
  width: 100%;
  padding-top: 100px;
  z-index: 5;
  top: 0;
  background-color: rgba(0, 0, 0, 0.5);
  .bigContainer {
    background-color: $champagne;
    border-radius: $box-border-element;
    width: 600px;
    max-height: 600px;
    margin: auto;
    padding-top: 5px;
    .bigNameCont {
      margin: 0px 5px;
      background-color: $cornell-red;
      border-radius: $box-border-element;
      color: $pure-white;
      justify-content: space-between;
      p {
        padding: 20px;
      }
      svg {
        fill: $pure-white;
        width: 25px;
        height: 25px;
        margin: 10px 20px;
        cursor: pointer;
      }
    }
    .imgDescripCont {
      position: relative;
      box-shadow: $box-shadow;
      .image {
        width: 240px;
        height: 145px;
        margin: 5px 0px 5px 15px;
        object-fit: cover;
        border-radius: $box-border-element;
      }
      p {
        width: 280px;
        margin: 20px auto;
      }
    }
    .descriptionCont {
      justify-content: space-between;
      .description {
        margin: 20px;
        width: 300px;
      }
    }
    aside {
      background-color: $denim;
      color: $pure-white;
      width: 250px;
      padding-top: 20px;
      align-items: center;
      ul {
        list-style: disc;
        li {
          margin: 10px;
          font-size: $font-p-small;
        }
      }
    }
    .buttonCont {
      justify-content: space-evenly;
      padding: 20px;
    }
  }
  @media (max-width: 431px) {
    .bigContainer {
      width: 80%;
      max-height: 100%;
      aside {
        width: 100%;
      }
    }
  }
}
</style>
