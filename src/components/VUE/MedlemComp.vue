<script setup>
import { ref } from "vue";

const medlemskabData = ref([]);

const baseEndpoint = `https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/medlemskab?acf_format=standard`;

function fetchSkansen() {
  fetch(baseEndpoint)
    .then((res) => res.json())
    .then((data) => {
      medlemskabData.value = data;
      // console.log(data);
    })
    .catch((err) => console.error(err));
}
fetchSkansen();
</script>

<template>
  <section class="flex content-cont">
    <article v-for="medlemskab in medlemskabData" :key="medlemskab.id">
      <div class="medlemNavn">
        <h3>{{ medlemskab.title.rendered }}</h3>
      </div>
      <p>{{ medlemskab.acf.betalingsmetode.join(", ") }}</p>
      <div class="prisDiv">
        <p>{{ medlemskab.acf.pris_pr_maned }} kr./mdr</p>
      </div>
      <div class="flex">
        <ul>
          <li v-if="medlemskab.acf.giver_adgang_til_1">
            {{ medlemskab.acf.giver_adgang_til_1 }}
          </li>
          <li v-if="medlemskab.acf.giver_adgang_til_2">
            {{ medlemskab.acf.giver_adgang_til_2 }}
          </li>
          <li v-if="medlemskab.acf.giver_adgang_til_3">
            {{ medlemskab.acf.giver_adgang_til_3 }}
          </li>
          <li v-if="medlemskab.acf.giver_adgang_til_4">
            {{ medlemskab.acf.giver_adgang_til_4 }}
          </li>
          <li v-if="medlemskab.acf.giver_adgang_til_5">
            {{ medlemskab.acf.giver_adgang_til_5 }}
          </li>
        </ul>
      </div>
      <p class="extraText" v-if="medlemskab.acf.ekstra_bemaerkninger">{{ medlemskab.acf.ekstra_bemaerkninger }}</p>
    </article>
  </section>
</template>


<style scoped lang="scss">
@import "../../css/style.scss";
section {
  flex-wrap: wrap;
  justify-content: center;
  article {
    background-color: $pure-white;
    border: 1px solid $cornell-red;
    width: 300px;
    height: 450px;
    margin: 3%;
    text-align: center;
    .medlemNavn {
      border-bottom: 1px solid $cornell-red;
      padding: 3%;
    }
    p {
      padding: 3% 0%;
    }
    .prisDiv {
      background-color: $cornell-red;
      color: $pure-white;
      p {
        font-size: $font-h3;
        font-family: "Palanquin";
        font-weight: $font-weight-bold;
      }
    }
    .flex {
      margin: 3% 12%;
    }
    ul {
      list-style: disc;
      li {
        padding-top: 3%;
        text-align: left;
      }
    }
    .extraText {
      font-weight: $font-weight-bold;
    }
  }
}
</style>
