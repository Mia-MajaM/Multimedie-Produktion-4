<script setup>
import { ref, computed, onMounted } from "vue";

// Definere emits for farveskift
const emits = defineEmits(["colorChange"]);

// Definere ref til at holde dataen
// Får den til at gøre klar til at holde et array af fjere data objekter
const aabningstidData = ref([]);

// Definere computed enheder for ugedagene, så disse ikke skal skrives en for en
const weekdays = computed(() => [
  { label: "Mandag", key: "mandag" },
  { label: "Tirsdag", key: "tirsdag" },
  { label: "Onsdag", key: "onsdag" },
  { label: "Torsdag", key: "torsdag" },
  { label: "Fredag", key: "fredag" },
  { label: "Lørdag", key: "lordag" },
  { label: "Søndag", key: "sondag" },
]);

// Laver en funktion til at fetche åbningstiderne fra flere endpoints
const fetchOpeningHours = (endpoints) => {
  const fetches = Array.isArray(endpoints)
    ? endpoints.map((endpoint) => fetch(endpoint).then((res) => res.json()))
    : [fetch(endpoints).then((res) => res.json())];

  Promise.all(fetches)
    .then((results) => {
      aabningstidData.value = results.flat();
      // console.log(results);
    })
    .catch((err) => console.error(err));
};

// Definere de forskellige endpoints, baseret på hvilken url siden skal have
const baseEndpoints = {
  fitness:
    "https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/abningstid/243?acf_format=standard",
  svoemmehal:
    "https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/abningstid/244?acf_format=standard",
  "wellness-relax":
    "https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/abningstid/244?acf_format=standard",
  "om-skansen": [
    "https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/abningstid/243?acf_format=standard",
    "https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/abningstid/244?acf_format=standard",
    "https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/abningstid/245?acf_format=standard",
  ],
  "": [
    "https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/abningstid/243?acf_format=standard",
    "https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/abningstid/244?acf_format=standard",
    "https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/abningstid/245?acf_format=standard",
  ],
};

// Henter den nuværende url for siden, og finder ud af hvilken url der befinder sig efter /
const urlOne = new URL(window.location.href);
const documentName = urlOne.pathname.split("/").pop().split(".")[0];

// Henter åbningstiderne hvis dokumentet mathcer en af de definerede endpoints
onMounted(() => {
  if (baseEndpoints[documentName]) {
    fetchOpeningHours(baseEndpoints[documentName]);
  }
});

// Definere en funktion der ændre baggrundsfarven baseret på sectionens title
const backgroundColor = (sectionTitle) => {
  if (sectionTitle === "Fitness") {
    return "#ce3d2a"; // Rød baggrund for Fitness
  } else if (sectionTitle === "Svømmehal") {
    return "#0073c3"; // Blå baggrund for Svømmehal
  } else if (sectionTitle === "Reception") {
    return "#fcedd8"; // Champagne farve for Reception
  }
};

// Definere en funktion der styre paragraph farven baseret på sectionens title
const paragraphColor = (sectionTitle) => {
  if (sectionTitle === "Reception") {
    return "#000000"; // Sort farve for Reception
  } else {
    return "#ffffff"; // Hvis farve for andre sectioner
  }
};
</script>

<template>
  <div>
    <!-- Sikre at åbningstiderne er hentet inden rendering -->
    <div
      v-if="aabningstidData.length"
      class="flex row full-width"
      aria-live="polite"
      role="region"
      aria-label="Opening Hours"
    >
      <!-- Bruger en v-for til at gå igennem hver post -->
      <!-- Laver inline style, for at styre baggrundsfarven -->
      <article
        v-for="(data, index) in aabningstidData"
        :key="index"
        class="flex column opening-hours-section"
        :style="{
          backgroundColor: backgroundColor(data.title.rendered),
          color: paragraphColor(data.title.rendered),
          borderBottomColor: paragraphColor(data.title.rendered),
        }"
      >
        <h2 id="opening-hours">Åbningstider i {{ data.title.rendered }}</h2>
        <!-- Looper gennem ugedage og indsætter åbningstiderne -->
        <div v-for="(day, dayIndex) in weekdays" :key="dayIndex" class="flex">
          <p class="day">{{ day.label }}:</p>
          <p>{{ data.acf[day.key] }}</p>
        </div>
        <aside>
          <p class="p-small">{{ data.acf.ekstra_info }}</p>
          <p class="p-small">{{ data.acf.ekstra_info_2 }}</p>
        </aside>
      </article>
    </div>

    <!-- Hvis der kun findes en post, som der gør på fitness og svømmehal url, skal der kun køres gennem 1 -->
    <article
      v-else-if="aabningstidData.length === 1"
      class="flex column opening-hours-section"
      :style="{
        backgroundColor: backgroundColor(aabningstidData[0].title.rendered),
        color: paragraphColor(aabningstidData[0].title.rendered),
        borderBottomColor: paragraphColor(aabningstidData[0].title.rendered),
      }"
    >
      <h2 id="opening-hours">
        Åbningstider i {{ aabningstidData[0].title.rendered }}
      </h2>
      <div v-for="(day, index) in weekdays" :key="index" class="flex">
        <p class="day">{{ day.label }}:</p>
        <p>{{ aabningstidData[0].acf[day.key] }}</p>
      </div>
      <aside class="flex column align">
        <p class="p-small">{{ aabningstidData[0].acf.ekstra_info }}</p>
        <p class="p-small">{{ aabningstidData[0].acf.ekstra_info_2 }}</p>
      </aside>
    </article>
  </div>
</template>

<style scoped lang="scss">
@import "../../css/style.scss";

.full-width {
  gap: 25px;
  .flex {
    column-gap: 25px;
    .day {
      margin-right: auto;
    }
  }
}
article {
  border-radius: $box-border-element;
  max-width: 375px;
  min-width: 300px;
  width: 100%;
  padding: 4%;
  row-gap: 35px;

  div {
    border-bottom: 1px solid;
  }
  aside {
    width: 100%;
    padding: 3%;
  }
}
.opening-hours-section {
  min-height: 650px;
}

.day {
  margin-right: 100px;
}

@media (max-width: 1250px) {
  article {
    min-width: 220px;
  }
}
@media (max-width: 850px) {
  .full-width {
    flex-wrap: wrap;
    justify-content: center;
  }
  article {
    padding: 6%;
  }
}
</style>
