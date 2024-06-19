<script setup>
import { ref } from "vue";

const svommetillaegData = ref([]);
const baseEndpoint = `https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/svommetillaeg?acf_format=standard`;

function fetchSkansen() {
  fetch(baseEndpoint)
    .then((res) => res.json())
    .then((data) => {
      svommetillaegData.value = data;
      console.log(data);
    })
    .catch((err) => console.error(err));
}
fetchSkansen();
</script>

<template>
  <div class="content-cont">
    <h3>Andre priser og tillæg</h3>
    <section class="flex wrap align">
      <article v-for="tillaeg in svommetillaegData" :key="tillaeg.id">
        <div class="titleCont">
          <p class="p-hero">{{ tillaeg.title.rendered }}</p>
        </div>
        <div class="container">
          <div class="flex" v-if="tillaeg.acf.tillaegstype_1">
            <p>{{ tillaeg.acf.tillaegstype_1 }}</p>
            <p>{{ tillaeg.acf.pris_1 }} kr.</p>
          </div>
          <div class="flex" v-if="tillaeg.acf.tillaegstype_2">
            <p>{{ tillaeg.acf.tillaegstype_2 }}</p>
            <p>{{ tillaeg.acf.pris_2 }} kr.</p>
          </div>
          <div class="flex" v-if="tillaeg.acf.tillaegstype_3">
            <p>{{ tillaeg.acf.tillaegstype_3 }}</p>
            <p>{{ tillaeg.acf.pris_3 }} kr.</p>
          </div>
          <div class="flex" v-if="tillaeg.acf.tillaegstype_4">
            <p>{{ tillaeg.acf.tillaegstype_4 }}</p>
            <p>{{ tillaeg.acf.pris_4 }} kr.</p>
          </div>
        </div>
      </article>
    </section>
  </div>
</template>

<style scoped lang="scss">
@import "../../css/style.scss";
section {
  margin: auto;
  article {
    margin: 3%;
    .titleCont {
      background-color: $champagne;
      padding: 15px 25px;
      box-shadow: $box-shadow;
      width: -webkit-fit-content;
      position: relative;
      z-index: 1;
      left: 25px;
      top: 25px;
    }
    .container {
      background-color: $denim;
      color: $pure-white;
      width: 300px;
      height: 300px;
      padding: 30px 15px;
      border-radius: $box-border-element;
      div {
        padding: 15px;
        justify-content: space-between;
        align-items: center;
        p {
          max-width: 175px;
        }
      }
    }
  }
}
</style>
