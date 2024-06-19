<script setup>
import { ref, onMounted } from 'vue';

const heroData = ref([]);

const baseEndpoint = `https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/hero-billede?acf_format=standard`;

// Fetch data from the API
onMounted(() => {
  fetch(baseEndpoint)
    .then((res) => res.json())
    .then((data) => {
      heroData.value = data;
      updateHeroesWithImages(); // Update hero images after fetching hero data
    })
    .catch((err) => console.error(err));
});

// Define a function to get hero image by ID
const getHeroImageById = (id) => {
  const hero = heroData.value.find(item => item.id === id);
  return hero ? hero.acf.heroimg : ''; // Return the image URL if found, otherwise return an empty string
};

// Define a function to update heroes object with images based on the URL
const updateHeroesWithImages = () => {
  const documentName = window.location.pathname.split("/").pop().split(".")[0];
  const filteredHeroes = heroes.value.filter(hero => {
    switch (documentName) {
      case "ophold":
        return hero.href.includes("/ophold");
      case "event-arrangement":
        return hero.href.includes("/event-arrangement");
      case "cafeteria":
        return hero.href.includes("/cafeteria");
      case "erhverv":
        return hero.href.includes("/erhverv");
      case "svoemmehal":
        return hero.href.includes("/svoemmehal");
      case "fitness":
        return hero.href.includes("/fitness");
      default:
        return false;
    }
  });
  
  // Update images for filtered heroes
  filteredHeroes.forEach(hero => {
    switch (documentName) {
      case "ophold":
        hero.image = getHeroImageById(400);
        break;
      case "event-arrangement":
        hero.image = getHeroImageById(396);
        break;
      case "cafeteria":
        hero.image = getHeroImageById(394);
        break;
      case "erhverv":
        hero.image = getHeroImageById(403);
        break;
      case "svoemmehal":
        hero.image = getHeroImageById(392);
        break;
      case "fitness":
        hero.image = getHeroImageById(390);
        break;
      default:
        break;
    }
  });

  // Set the filtered heroes as the updated heroes
  heroes.value = filteredHeroes;
};

let heroes = ref([
    
    { h1: 'Danhostel Nørresundby', phero: 'Overvejer du at besøge Skansen i Nørresundby, men mangler du et sted at overnatte? Så bare rolig. Danhostel Nørresundby har 29 værelser med plads til 2-4 gæster på hvert værelse.', AColor: '$pure-white', Abg: 'blue-sunrise', href: '/ophold#information', AbutTxt: 'priser og læs mere', image: "", height: "600px" },
    { h1: 'Arrangementer i Skansen', phero: 'Fra actionfyldte aktiviteter og adrenalinfyldte udfordringer til afslappende og luksuriøse oplevelser tilbyder vi et bredt udvalg af skræddersyede pakker, der passer til jeres ønsker og behov. Vores dedikerede team af arrangører vil sørge for, at hvert øjeblik er planlagt til perfektion og lever op til jeres forventninger.', AColor: '$pure-white', Abg: 'red-sunset', href: '/event-arrangement#eventkalender', AbutTxt: 'Skansen Fremtidige Events', image: "", height: "650px" },
    { h1: 'Skansens Café', phero: 'Skansen Café er vores café i forlængelse af Svømmehallen, og her tilbyder vi et varieret udvalg af lækre sandwich, burger og lette retter til at slukke den lille sult. Vi har et udvalg af sunde snacks og forskellige is.', AColor: '$pure-white', Abg: 'bg-bgA', href: '/cafeteria#menukort', AbutTxt: 'Skansen Menukort', image: "asd", height: "600px" },
    { h1: 'Skansens Erhverv', phero: 'Drømmer du om at tage en svømmetur i vores imponerende 50 meter bassin, eller ønsker du at fejre en uforglemmelig børnefødselsdag i vores sjove vandland? Så er du kommet til det rette sted!', AColor: '$pure-white', Abg: 'blue-sunrise', href: '/om-skansen/erhverv#firmaaftaler', AbutTxt: 'Skansen Erhvervsaftaler', image: "asd", height: "800px" },
    { h1: 'Svømmehal', phero: 'Drømmer du om at tage en svømmetur i vores imponerende 50 meter bassin, eller ønsker du at fejre en uforglemmelig børnefødselsdag i vores sjove vandland? Så er du kommet til det rette sted!', AColor: '$pure-white', Abg: 'blue-sunrise', href: '/svoemmehal#priser', AbutTxt: 'Skansen Svømmehal Priser', image: "asd", height: "800px" },
  { h1: 'Fitnesscenter', phero: 'Vores fokus er fitness, sundhed og velvære. Hos Skansen i Nørresundby finder du et topmoderne fitnesscenter, ledet af dygtige Stine Rex.', AColor: '$pure-white', Abg: 'red-sunset', href: '/fitness#medlemskab', AbutTxt: 'Skansen Fitness Medlemskaber & Priser', image: "asd", height: "1000px" },

  ]);

// -----------------------------------------------------
updateHeroesWithImages();
// -------------------------
</script>


<template>
    <div>
      <div v-for="(hero, index) in heroes" :key="index" class="hero full-width flex column" :style="{ height: hero.height }">
        <div class="hero-background" :style="{ backgroundImage: `url(${hero.image})` }"></div>
        <div class="content-cont hero-txt-cont">
          <div class="hero-txt flex column align">
            <section class="flex column">
              <h1>{{ hero.h1 }}</h1>
              <p>{{ hero.phero }}</p>
            </section>
            <a :href="hero.href" :class="'flex row align ' + hero.Abg" aria-label="Button" :style="{ color: hero.AColor }" :title="hero.h1">
              <p><span>Gå til </span>{{ hero.AbutTxt }}</p>
              <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 320 512" class="arrow">
                <path d="M278.6 233.4c12.5 12.5 12.5 32.8 0 45.3l-160 160c-12.5 12.5-32.8 12.5-45.3 0s-12.5-32.8 0-45.3L210.7 256 73.4 118.6c-12.5-12.5-12.5-32.8 0-45.3s32.8-12.5 45.3 0l160 160z"/>
              </svg>
            </a>
          </div>
        </div>
      </div>
    </div>
  </template>
  
    

<style lang="scss" scoped>
    @import "../../css/style.scss";
    
    .hero {
        position: relative;
        transition: $transition;
        
        .hero-background {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-position: center;
            background-repeat: no-repeat;
            background-size: cover;
        }
    
        .hero-txt-cont {
            position: relative; 
            z-index: 1; 
    
            .hero-txt {
                margin-top: 15%;
                margin-right: auto;
                max-width: 700px;
                border-radius: 25px;
                background-color: $pure-white;
                box-shadow: $box-shadow;
    
                section {
                    padding: 35px;
                    row-gap: 15px;
                }
    
                a {
                    border-radius: 0 0 25px 25px;
                    width: 100%;
                    padding: 30px;
                    cursor: pointer;
                    background-color: $cornell-red;
                    border: transparent;
                    color: $pure-white;
                    outline: double 25px transparent;
                    transition: $transition;
    
                    span {
                        width: 1px;
                        opacity: -100%;
                        transition: $transition;
                        position: relative;
                        right: 200px;
                    }
    
                    p {
                        margin-left: auto;
                    }
    
                    .arrow {
                        width: 15px;
                        fill: white;
                        margin-left: auto;
                    }
    
                    &:hover {
                        border: solid 2px $pure-white;
                        outline: solid 5px $pure-white;
    
                        p {
                            span {
                                width: auto;
                                opacity: 100%;
                                right: 0;
                            }
                        }
                    }
                }
            }
        }
    }

    @media (max-width: 1550px) {
        .hero {
            max-height: 800px;}
        }
    @media (max-width: 1150px) {
        .hero {
            max-height: 700px;}
        }
    @media (max-width: 750px) {
        .hero {
            max-height: 600px;}
        }
    @media (max-width: 650px) {
        .hero {
            max-height: 550px;}
        }
    @media (max-width: 450px) {
        .hero {
            max-height: 450px;
            .preview {
                width: 100%;
                height: auto;
                padding: 25px; 
    
                .img-container {
                    flex-wrap: wrap;
                    max-width: none;
                    width: 100%;
                }
                .arrow-cont {
                    display: none;
                }
                a {
                    img {
                        position: relative;
                    }
                }
            }
        }
    }
    </style>
    