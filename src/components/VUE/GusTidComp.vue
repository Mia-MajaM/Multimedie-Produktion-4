<script setup>
import { ref } from "vue";

const gustidData = ref([]);

const baseEndpoint = `https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/saunagustid?acf_format=standard`;

function fetchSkansen() {
  fetch(baseEndpoint)
    .then((res) => res.json())
    .then((data) => {
      gustidData.value = data;
      // console.log(data);
    })
    .catch((err) => console.error(err));
}
fetchSkansen();
</script>

<template>
  <section class="flex content-cont">
    <article v-for="gustid in gustidData" :key="gustid.id" aria-labelledby="gustidTitle">
      <div class="topSection">
        <h3 v-html="gustid.title.rendered" id="gustidTitle"></h3>
      </div>
      <div class="middleSection flex">
        <div class="saunagus flex column">
          <p>Saunagus i</p>
          <h3>{{ gustid.acf.sted }}</h3>
        </div>
        <div class="tider flex column">
          <p v-html="gustid.acf.saunagustider"></p>
          <p class="p-small">{{ gustid.acf.ekstra_info }}</p>
        </div>
      </div>
      <div class="bottomSection">
        <!-- Use proper headings to improve accessibility -->
        <p v-if="gustid.acf.sted === 'Panorama'">
          <strong>Kelo:</strong> {{ gustid.acf.anden_sauna }}
        </p>
        <p v-else-if="gustid.acf.sted === 'Kelo'">
          <strong>Panorama:</strong> {{ gustid.acf.anden_sauna }}
        </p>
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
    border: 1px solid $denim;
    width: 300px;
    margin: 3%;
    text-align: center;

    .topSection {
      background-color: $denim;
      color: $pure-white;
      padding-bottom: 3%;
      box-shadow: $box-shadow;
      position: relative;
      z-index: 1;
    }
    .middleSection {
      position: relative;
      z-index: 0;
      height: 200px;
      .saunagus {
        width: 70%;
        background-color: $denim;
        color: $pure-white;
        justify-content: center;
      }
      .tider {
        margin: 3% auto 0% auto;
        justify-content: space-between;
      }
    }
    .bottomSection {
      border-top: 1px solid $denim;
      padding: 5%;
    }
  }
}
</style>
