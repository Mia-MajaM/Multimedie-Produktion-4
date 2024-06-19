<script setup>
import { ref } from "vue";

const flerturskortData = ref([]);

const baseEndpoint = `https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/flerturskort?acf_format=standard`;

function fetchSkansen() {
  fetch(baseEndpoint)
    .then((res) => res.json())
    .then((data) => {
      flerturskortData.value = data;
      // console.log(data);
    })
    .catch((err) => console.error(err));
}
fetchSkansen();
</script>

<template>
  <section class="flex content-cont" aria-labelledby="flerturskortTitle">
    <article v-for="flerturskort in flerturskortData" :key="flerturskort.id">
      <div class="topSection flex column">
        <h3>{{ flerturskort.title.rendered }}</h3>
        <p>{{ flerturskort.acf.omrade }}</p>
      </div>
      <div class="middleSection flex align">
        <p class="p-small">{{ flerturskort.acf.tidsrum }}</p>
      </div>
      <div class="priceSection flex">
        <div>
          <h4 class="p-hero">10 tursbånd</h4>
          <!-- Since there are numbers in the slug from WordPress, the object needs to be wrapped in [] -->
          <p>Voksne: {{ flerturskort.acf["10_turs_pris_for_voksne"] }} kr.</p>
          <p>
            Pensionist:
            {{ flerturskort.acf["10_turs_pris_for_pensionister"] }} kr.
          </p>
          <!-- Use v-if to check if there is data for the age group field; if not, children should not be displayed -->
          <p v-if="flerturskort.acf.aldersgruppe_born">
            Børn {{ flerturskort.acf.aldersgruppe_born }} år:
            {{ flerturskort.acf["10_turs_pris_for_born"] }} kr.
          </p>

          <h4 class="p-hero">30 tursbånd</h4>
          <p>Voksne: {{ flerturskort.acf["30_turs_pris_for_voksne"] }} kr.</p>
          <p>
            Pensionist:
            {{ flerturskort.acf["30_turs:_pris_for_pensionister"] }} kr.
          </p>
          <p v-if="flerturskort.acf.aldersgruppe_born">
            Børn {{ flerturskort.acf.aldersgruppe_born }} år: 
            {{ flerturskort.acf["30_turs_pris_for_born"] }} kr.
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
    min-height: 475px;
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
      text-align: center;
      height: 60px;
      padding: 3%;
    }
    .priceSection {
      justify-content: center;
      padding-bottom: 10%;
      .p-hero {
        margin: 15% 0% 5% 0%;
      }
    }
  }
}
</style>
