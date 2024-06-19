<script setup>
import { ref} from "vue";

const klubData = ref([]);

const baseEndpoint = `https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/klub?acf_format=standard`;

function fetchSkansen() {
  fetch(baseEndpoint)
    .then((res) => res.json())
    .then((data) => {
      klubData.value = data;
    })
    .catch((err) => console.error(err));
}

fetchSkansen();
</script>

<template>
  <div class="flex content-cont wrap align">
    <article v-for="klub in klubData" :key="klub.id" class="article">
      <a :href="klub.acf.link" target="_blank" class="flex" role="link">
        <div class="flex align">
          <img :src="klub.acf.logo" :alt="klub.title.rendered" class="image" />
        </div>
        <p class="p-hero">{{ klub.title.rendered }}</p>
        <p class="readMore">Læs mere</p>
      </a>
    </article>
  </div>
</template>

  
<style scoped lang="scss">
@import "../../css/style.scss";

  .article {
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
  
</style>
