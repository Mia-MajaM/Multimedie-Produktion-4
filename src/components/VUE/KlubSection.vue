<script setup>
import { ref } from "vue";
import KlubComp2 from "./KlubComp2.vue";

const medlemHeight = ref("350px");
const showMedlemComp = ref(false);
const showKlipComp = ref(false);

const toggleAccordion = () => {
  medlemHeight.value =
    medlemHeight.value === "-webkit-fit-content" ? "350px" : "-webkit-fit-content";
};
</script>
<template>
  <div class="full-width flex column align medlem-cont">
    <div class="full-width flex row align top red-sunset">
      <div class="content-cont flex align">
        <h2>Klubber</h2>
      </div>
    </div>
    <div
      class="medlem full-width flex column"
      :style="{ height: medlemHeight }"
    >
      <KlubComp2 />
      <div
        class="full-width flex align accordion red-sunset"
        @click="toggleAccordion"
        role="button"
        tabindex="0"
      >
        <div class="flex column align acc-txt">
          <p>Se Flere Klubber</p>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            class="arrow"
            viewBox="0 0 18 10"
          >
            <path
              fill="#000"
              d="M9.63574 9.63574c-.48828.48826-1.28125.48826-1.76953 0l-7.499998-7.5c-.488282-.48828-.488282-1.281249 0-1.76953.488281-.488281 1.281248-.488281 1.769528 0L8.75293 6.9834 15.3701.370117c.4883-.488282 1.2813-.488282 1.7695 0 .4883.488281.4883 1.281253 0 1.769533l-7.49995 7.5-.00391-.00391Z"
            ></path>
          </svg>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss">
@import "../../css/style.scss";
.medlem-cont {
  position: relative;
  .top {
    position: sticky;
    top: 0;
    z-index: 11;
  }
  .content-cont {
    align-items: center;
    margin: 0 auto;
    div {
      margin: 0 auto;
    }
    h2 {
      color: $pure-white;
      font-weight: 900;
      font-size: $font-h1;
      padding: 1% 0 1% 0;
      letter-spacing: 0.8pt;
      margin-right: auto;
    }
  }
  .medlem {
    background: $champagne;
    min-height: 290px;
    overflow-y: hidden;
    position: relative;
    padding-bottom: 100px;
  }
}
.accordion {
  position: absolute;
  bottom: 0;
  cursor: pointer;
  z-index: 10;
  min-height: 100px;
  .acc-txt {
    color: $eerie-black;
    background-color: $pure-white;
    padding: 1%;
    border-radius: $box-border-element;
    .arrow {
      height: 25px;
      width: 25px;
    }
  }
}

@media (max-width: 1250px) {
  h2 {
    font-size: $font-h2;
  }
}
@media (max-width: 850px) {
  h2 {
    font-size: $font-h3;
  }
}
</style>
