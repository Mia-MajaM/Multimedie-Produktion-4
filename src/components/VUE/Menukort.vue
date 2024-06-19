<script setup>
import { ref } from "vue";

const menukortData = ref([]);

const baseEndpoint = `https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/menukort?acf_format=standard`;

function fetchSkansen() {
  fetch(baseEndpoint)
    .then((res) => res.json())
    .then((data) => {
      menukortData.value = data;
      // console.log(data);
    })
    .catch((err) => console.error(err));
}
fetchSkansen();
</script>

<template>
  <section aria-labelledby="menuHeading">
    <h2 id="menuHeading">Menukort</h2>
    <div class="flex wrap">
      <article v-for="madgruppe in menukortData" :key="madgruppe.id">
        <h3>{{ madgruppe.title.rendered }}</h3>
        <hr />
        <p class="p-small">{{ madgruppe.acf.ekstra_info }}</p>
        <div class="flex price">
          <p v-if="madgruppe.acf.madbeskrivelse_1" aria-label="Menu description">
            {{ madgruppe.acf.madbeskrivelse_1 }}
          </p>
          <p v-if="madgruppe.acf.pris_1" aria-label="Price">
            {{ madgruppe.acf.pris_1 }},-
          </p>
        </div>
        <div class="flex price">
          <p v-if="madgruppe.acf.madbeskrivelse_2" aria-label="Menu description">
            {{ madgruppe.acf.madbeskrivelse_2 }}
          </p>
          <p v-if="madgruppe.acf.pris_2" aria-label="Price">
            {{ madgruppe.acf.pris_2 }},-
          </p>
        </div>
        <div class="flex price">
          <p v-if="madgruppe.acf.madbeskrivelse_3" aria-label="Menu description">
            {{ madgruppe.acf.madbeskrivelse_3 }}
          </p>
          <p v-if="madgruppe.acf.pris_3" aria-label="Price">
            {{ madgruppe.acf.pris_3 }},-
          </p>
        </div>
        <div class="flex price">
          <p v-if="madgruppe.acf.madbeskrivelse_4" aria-label="Menu description">
            {{ madgruppe.acf.madbeskrivelse_4 }}
          </p>
          <p v-if="madgruppe.acf.pris_4" aria-label="Price">
            {{ madgruppe.acf.pris_4 }},-
          </p>
        </div>
        <div class="flex price">
          <p v-if="madgruppe.acf.madbeskrivelse_5" aria-label="Menu description">
            {{ madgruppe.acf.madbeskrivelse_5 }}
          </p>
          <p v-if="madgruppe.acf.pris_5" aria-label="Price">
            {{ madgruppe.acf.pris_5 }},-
          </p>
        </div>
      </article>
      <p class="ekstra_tekst" aria-label="Allergen information">* Kontakt personale i forhold til allergener</p>
    </div>
  </section>
</template>


<style scoped lang="scss">
@import "../../css/style.scss";

section {
  background-color: $pure-white;
  padding: 3% 0% 0% 10%;

  div {
    gap: 10%;

    article {
      width: 300px;

      h3 {
        margin-top: 5%;
      }

      hr {
        border: 1px solid $eerie-black;
        margin: 1% 0%;
      }

      .price {
        justify-content: space-between;
        margin: 5% 0%;
      }
    }

    .ekstra_tekst {
      text-align: right;
      margin: 0% 10% 3% 0%;
      width: 100%;
    }
  }
}
</style>
