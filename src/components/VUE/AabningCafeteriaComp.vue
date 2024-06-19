<script setup>
import { ref } from "vue";

const aabningstidData = ref(null);

const baseEndpoint = `https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/abningstid/300?acf_format=standard`;

function fetchSkansen() {
  fetch(baseEndpoint)
    .then((res) => res.json())
    .then((data) => {
      aabningstidData.value = data;
      console.log(data);
    })
    .catch((err) => console.error(err));
}
fetchSkansen();
</script>

<template>
  <article v-if="aabningstidData" aria-labelledby="opening-hours-heading">
    <h2 id="opening-hours-heading">Åbningstider</h2>
    <p class="p-small" v-if="aabningstidData.acf.ekstra_info">{{ aabningstidData.acf.ekstra_info }}</p>
    <div>
      <p class="dag">Mandag & Onsdag</p>
      <p>{{ aabningstidData.acf.mandag_onsdag_formiddag }}</p>
      <p>{{ aabningstidData.acf.mandag_onsdag_eftermiddag }}</p>
    </div>
    <div>
      <p class="dag">Tirsdag & Torsdag</p>
      <p>{{ aabningstidData.acf.tirsdag_torsdag_formiddag }}</p>
      <p>{{ aabningstidData.acf.tirsdag_torsdag_eftermiddag }}</p>
    </div>
    <div>
      <p class="dag">Fredag</p>
      <p>{{ aabningstidData.acf.fredag_formiddag }}</p>
      <p v-if="aabningstidData.acf.fredag_eftermiddag">
        {{ aabningstidData.acf.fredag_eftermiddag }}
      </p>
    </div>
    <div>
      <p class="dag">Lørdag, Søndag & Helligdage</p>
      <p>{{ aabningstidData.acf.lordag_sondag_formiddag }}</p>
      <p v-if="aabningstidData.acf.lordag_sondag_eftermiddag">
        {{ aabningstidData.acf.lordag_sondag_eftermiddag }}
      </p>
    </div>
  </article>
</template>


<style scoped lang="scss">
@import "../../css/style.scss";

article {
  border-radius: $box-border-element;
  max-width: 375px;
  min-width: 320px;
  margin: 3% 5%;
  h2 {
    margin-bottom: 5%;
  }
  div {
    margin-top: 7%;
    .dag {
      text-decoration: underline;
    }
    p {
      margin-top: 3%;
    }
  }
}
</style>
