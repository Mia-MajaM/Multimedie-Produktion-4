<script setup>
import { ref, computed } from "vue";
import MedlemComp from "./MedlemComp.vue";
import KlipComp from "./KlipComp.vue";

const medlemHeight = ref("350px");
const showMedlemComp = ref(false);
const showKlipComp = ref(false);

// Change height of the .medlem class
const toggleAccordion = computed(() => {
  medlemHeight.value =
  medlemHeight.value === "-webkit-fit-content" ? "350px" : "-webkit-fit-content";
});

// Change the accordion text depending on the height of .medlem class
const accTxt = computed(() => {
  return medlemHeight.value === "-webkit-fit-content"
    ? "Se færre medlemskaber"
    : "Se flere medlemskaber";
});
// Change the rotation styling on the .arrow class depending on the height of .medlem class
const arrowRotation = computed(() => {
  return medlemHeight.value === "-webkit-fit-content" ? "180deg" : "0deg";
});
</script>

<template>
  <div class="full-width flex column align medlem-cont">
    <div class="full-width flex row align top red-sunset">
      <div class="content-cont flex align">
        <h2>Medlemskaber</h2>
        <a class="flex button-ekstern" target="_blank" href="https://shop.compuapp.dk/norresun" aria-label="Button" title="Gå til Skansens ">
          <p>Køb Medlemskab</p>
          <svg xmlns="http://www.w3.org/2000/svg" fill="white" class="arrow" viewBox="0 0 18 10">
            <path fill="#ffff" d="M9.63574 9.63574c-.48828.48826-1.28125.48826-1.76953 0l-7.499998-7.5c-.488282-.48828-.488282-1.281249 0-1.76953.488281-.488281 1.281248-.488281 1.769528 0L8.75293 6.9834 15.3701.370117c.4883-.488282 1.2813-.488282 1.7695 0 .4883.488281.4883 1.281253 0 1.769533l-7.49995 7.5-.00391-.00391Z"/>
          </svg>
        </a>
      </div>
    </div>
    <div class="medlem full-width flex column" :style="{ height: medlemHeight }">
      <MedlemComp />
      <KlipComp />
      <div class="full-width flex align accordion red-sunset" @click="toggleAccordion">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" class="vector" viewBox="0 0 713 100">
          <path fill="#fff" d="M591 105H126L4 0h708L591 105Z"></path>
        </svg>
        <div class="flex column align acc-txt">
          <p v-text="accTxt"></p>
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" class="arrow" viewBox="0 0 18 10" :style="{ transform: `rotate(${arrowRotation})` }">
            <path fill="#000" d="M9.63574 9.63574c-.48828.48826-1.28125.48826-1.76953 0l-7.499998-7.5c-.488282-.48828-.488282-1.281249 0-1.76953.488281-.488281 1.281248-.488281 1.769528 0L8.75293 6.9834 15.3701.370117c.4883-.488282 1.2813-.488282 1.7695 0 .4883.488281.4883 1.281253 0 1.769533l-7.49995 7.5-.00391-.00391Z"></path>
          </svg>
        </div>
      </div>
    </div>
  </div>
</template>


<style lang="scss" scoped>
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
  box-shadow: $box-shadow-top;
  position: absolute;
  bottom: 0;
  cursor: pointer;
  z-index: 10;
  .acc-txt {
    color: $eerie-black;
    position: absolute;
    top: 1%;
    padding: 5px;
    .arrow {
      margin-top: 4px;      
      width: 20px;
    }
  }
  .vector {
    margin: 0;
    padding: 0;
    max-width: 350px;
    height: 50px;
  }
}

.button-ekstern {
    border: transparent;
    border-radius: $box-border-element;
    transition: $transition;
    background-color: $cornell-red;
    color: $pure-white;
    font-size: 1.5rem;
    align-items: center;
    cursor: pointer;
    padding: 10px 20px;
    max-height: 70px;
    height: 55px;
    outline: double 20px transparent;

    &:hover {
      filter: saturate(110%);
      box-shadow: $box-shadow;
      outline: solid 4px $pure-white;
      .arrow {
        rotate: 270deg;
      }
    }

    &:active {
      background-color: $eerie-black;
    }
  .arrow {
    transition: $transition;
    rotate: -90deg;
    width: 20px;
    height: 20px;
    margin-left: 5px;
  } 
}
@media (max-width: 1250px) {
  .vector {
    width: 50%;
  }
  h2 {
    font-size: $font-h2 !important;
  }
}
@media (max-width: 850px) {
  .vector {
    width: 75%;
  }
  h2 {
    font-size: $font-h3 !important;
  }
}
@media (max-width: 550px) {
  .medlem-cont{
    .top {
      .content-cont {
        padding: 10px 0 20px 0;
      flex-direction: column;
      h2{
        margin: 0 auto;
        text-align: center;
      }
      }
    }

  }   
}
</style>
