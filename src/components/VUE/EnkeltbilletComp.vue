<script setup>
import { ref } from "vue";

const enkeltbilletData = ref([]);

const baseEndpoint = `https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/enkeltbillet?acf_format=standard`;

function fetchSkansen() {
  fetch(baseEndpoint)
    .then((res) => res.json())
    .then((data) => {
      enkeltbilletData.value = data;
      // console.log(data);
    })
    .catch((err) => console.error(err));
}
fetchSkansen();
</script>

<template>
  <section class="flex content-cont" aria-live="polite" role="region" aria-label="Ticket Information">
    <article v-for="enkeltbillet in enkeltbilletData" :key="enkeltbillet.id">
      <div class="topSection flex column">
        <h3>{{ enkeltbillet.title.rendered }}</h3>
        <p>{{ enkeltbillet.acf.omrade }}</p>
      </div>
      <div class="middleSection flex align">
        <p class="p-small">{{ enkeltbillet.acf.tidsrum }}</p>
      </div>
      <div class="priceSection flex">
        <div>
          <p>Voksne: {{ enkeltbillet.acf.pris_voksne }} kr.</p>
          <p>Pensionist: {{ enkeltbillet.acf.pris_pensionist }} kr.</p>
          <!-- Check if there is data for the children's age group -->
          <p v-if="enkeltbillet.acf.aldersgruppe_born">
            Børn {{ enkeltbillet.acf.aldersgruppe_born }} år: {{ enkeltbillet.acf.pris_born }} kr.
          </p>
        </div>
      </div>
    </article>
  </section>
</template>


<style scoped lang="scss">
@import "../../css/style.scss";
section {
  flex-wrap: wrap;
  justify-content: center;
  article {
    background-color: $champagne;
    border: 1px solid $denim;
    width: 300px;
    min-height: 290px;
    margin: 3%;
    border-radius: $box-border-element;
    .topSection {
      text-align: center;
      background-color: $pure-white;
      padding-bottom: 15px;
      row-gap: 10px;
      border-radius: $box-border-element;
      p{
        margin-bottom: 5px;
      }
      &::after {
        content: "";
        width: 80%;
        align-self: center;
        height: 1px;
        background: $denim;
  
      }
    }
    .middleSection {
      background-color: $pure-white;
      padding-bottom: 15px;
      text-align: center;
      
      min-height: 53px;
    }
    .priceSection {
      justify-content: center;
      padding: 25px 0 0 0;
    }
  }
}
</style>
