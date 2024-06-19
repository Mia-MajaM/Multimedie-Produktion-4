<script setup>
import { ref } from "vue";

const klubData = ref([]);
const expand = ref(false);

const baseEndpoint = `https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/klub?acf_format=standard`;

function fetchSkansen() {
  fetch(baseEndpoint)
    .then((res) => res.json())
    .then((data) => {
      klubData.value = data;
      console.log(data);
    })
    .catch((err) => console.error(err));
}
fetchSkansen();

function toggleExpand() {
  expand.value = !expand.value;
}
</script>

<template>
  <section class="flex content-cont">
    <article v-for="(klub, index) in klubData" :key="klub.id" v-if="expand || index < 3">
      <a
        :href="klub.acf.link"
        target="blank"
        :title="'Link til ' + klub.title.rendered"
        class="flex"
        role="link"
      >
        <div class="flex align">
          <img :src="klub.acf.logo" :alt="klub.title.rendered" class="image" />
        </div>
        <p class="p-hero">{{ klub.title.rendered }}</p>
        <p class="readMore">Læs mere</p>
      </a>
    </article>
  </section>
  <div class="arrow-expand flex column align" @click="toggleExpand" :class="{ rotated: expand }" role="button">
    <p>Se alle klubber</p>
    <svg
      width="18"
      height="10"
      viewBox="0 0 18 10"
      fill="none"
      xmlns="http://www.w3.org/2000/svg"
      class="arrow"
    >
      <path
        d="M9.63574 9.63574C9.14746 10.124 8.35449 10.124 7.86621 9.63574L0.366212 2.13574C-0.12207 1.64746 -0.12207 0.854491 0.366212 0.36621C0.854493 -0.122071 1.64746 -0.122071 2.13574 0.36621L8.75293 6.9834L15.3701 0.370117C15.8584 -0.118165 16.6514 -0.118165 17.1396 0.370117C17.6279 0.858398 17.6279 1.65137 17.1396 2.13965L9.63965 9.63965L9.63574 9.63574Z"
        fill="black"
      />
    </svg>
  </div>
</template>


<style scoped lang="scss">
@import "../../css/style.scss";
section {
  justify-content: center;
  flex-wrap: wrap;
  article {
    margin: 3%;
    width: 300px;
    height: 300px;
    a {
      background-color: $pure-white;
      box-shadow: $box-shadow;
      flex-direction: column;
      text-align: center;
      color: $eerie-black;
      position: relative;
      div {
        height: 200px;
        .image {
          margin: auto;
          padding: 5%;
          width: 150px;
          object-fit: cover;
        }
      }
      .p-hero {
        background-color: $cornell-red;
        color: $pure-white;
        padding: 3%;
      }
      .readMore {
        max-height: 0;
        opacity: 0;
        overflow: hidden;
        transition: all 0.5s ease;
      }
      &:hover .readMore {
        max-height: 100px;
        opacity: 1;
        padding: 5% 0%;
        transform: scale(1.05);
      }
    }
  }
  .arrow-expand {
    cursor: pointer;
    text-align: center;
    margin: 20px;
    .arrow {
      margin-top: 10px;
      color: $cornell-red;
      transition: $transition;
      transform-origin: center;
    }
    .rotated .arrow {
      transform: rotate(180deg);
    }
  }
}
</style>
