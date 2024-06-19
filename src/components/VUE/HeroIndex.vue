<script setup>
import { ref, onMounted } from 'vue';

// FETCH -----------------------------
const heroData = ref([]);
const baseEndpoint = `https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/hero-billede?acf_format=standard`;

function fetchSkansen() {
  fetch(baseEndpoint)
    .then((res) => res.json())
    .then((data) => {
        heroData.value = data.slice(1, 7);
      console.log(data);
      updateHeroesImages();
    })
    .catch((err) => console.error(err));
}
fetchSkansen();

function updateHeroesImages() {
  heroes.value.forEach((hero, index) => {
    heroes.value[index].image = heroData.value[index].acf.heroimg;
  });
}
// -----------------------------------

let heroes = ref([
    
    { h1: 'Danhostel Nørresundby', phero: 'Overvejer du at besøge Skansen i Nørresundby, men mangler du et sted at overnatte? Så bare rolig. Danhostel Nørresundby har 29 værelser med plads til 2-4 gæster på hvert værelse.', AColor: '$pure-white', Abg: 'blue-sunrise', href: '/ophold', AbutTxt: 'Priser og læs mere', image: "/src/assets/img/ophold/Overnatning.png" },
    { h1: 'Om Skansen og Kontakt', phero: 'Sommeren 1968 stod svømmebassinet klar, og blev døbt Skansebadet. i 1969 kunne man tage det første spadestik til den første idrætshal i Nørresundby. Den 15. Maj 1971 blev hallen indviet og døbt “Solsidehallen”.', AColor: '$pure-white', Abg: 'bg-bgA', href: '/om-skansen', AbutTxt: 'Om Skansen og Kontakt', image: "/src/assets/img/ophold/Overnatning.png" },
    { h1: 'Event & Arrangementer', phero: 'Fra actionfyldte aktiviteter og adrenalinfyldte udfordringer til afslappende og luksuriøse oplevelser tilbyder vi et bredt udvalg af skræddersyede pakker, der passer til jeres ønsker og behov. Vores dedikerede team af arrangører vil sørge for, at hvert øjeblik er planlagt til perfektion og lever op til jeres forventninger.', AColor: '$pure-white', Abg: 'bg-bgA', href: '/event-arrangement', AbutTxt: 'Event & Arrangementer', image: "/src/assets/img/event/event.png" },
    { h1: 'Skansens Café', phero: 'Skansen Café er vores café i forlængelse af Svømmehallen, og her tilbyder vi et varieret udvalg af lækre sandwich, burger og lette retter til at slukke den lille sult. Vi har et udvalg af sunde snacks og forskellige is.', AColor: '$pure-white', Abg: 'bg-bgA', href: '/cafeteria', AbutTxt: 'Skansens Café', image: "/src/assets/img/cafeteria/SkansensCafe.png" },
    { h1: 'Svømmehal', phero: 'Drømmer du om at tage en svømmetur i vores imponerende 50 meter bassin, eller ønsker du at fejre en uforglemmelig børnefødselsdag i vores sjove vandland? Så er du kommet til det rette sted!', AColor: '$pure-white', Abg: 'blue-sunrise', href: '/svoemmehal', AbutTxt: 'Svømmehal', image: "/src/assets/img/svommehal/Svomning_hero_figure.png" },
  { h1: 'Fitnesscenter', phero: 'Vores fokus er fitness, sundhed og velvære. Hos Skansen i Nørresundby finder du et topmoderne fitnesscenter, ledet af dygtige Stine Rex.', AColor: '$pure-white', Abg: 'red-sunset', href: '/fitness', AbutTxt: 'Fitness', image: "/src/assets/img/fitness/Haandbold_hero_figure.png" },
]);

// -----------------------------------------------------
let currentHero = ref(0);
let intervalId;

function nextHero() {
    clearInterval(intervalId);
    currentHero.value = (currentHero.value + 1) % heroes.value.length;
    startSlideshow(); 
}

function prevHero() {
    clearInterval(intervalId); 
    currentHero.value = (currentHero.value - 1 + heroes.value.length) % heroes.value.length;
    startSlideshow(); 
}

function startSlideshow() {
    intervalId = setInterval(() => {
        nextHero();
    }, 5000);
}

function pauseSlideshow() {
    clearInterval(intervalId);
}

function resumeSlideshow() {
    startSlideshow();
}

onMounted(() => {
    startSlideshow();
});
// -------------------------
</script>


<template>
    <div class="hero full-width flex column" @mouseover="pauseSlideshow" @mouseleave="resumeSlideshow">
        <div class="hero-background" :style="{ backgroundImage: `url(${heroes[currentHero].image})` }"></div>
        <div class="content-cont hero-txt-cont">
            <div class="hero-txt flex column align">
                <section class="flex column">
                    <h1>{{ heroes[currentHero].h1 }}</h1>
                    <p>{{ heroes[currentHero].phero }}</p>
                </section>
                <a :href="heroes[currentHero].href" :class="'flex row align ' + heroes[currentHero].Abg" aria-label="Button" :style="{ color: heroes[currentHero].AColor }" :title="heroes[currentHero].h1">
                    <p><span>Gå til </span>{{ heroes[currentHero].AbutTxt }}</p>
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 320 512" class="arrow">
                        <path d="M278.6 233.4c12.5 12.5 12.5 32.8 0 45.3l-160 160c-12.5 12.5-32.8 12.5-45.3 0s-12.5-32.8 0-45.3L210.7 256 73.4 118.6c-12.5-12.5-12.5-32.8 0-45.3s32.8-12.5 45.3 0l160 160z"/>
                    </svg>
                </a>
            </div>
        </div>
        <div class="full-width preview flex row align">
            <a class="arrow-cont left flex align" @click="prevHero"> 
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 320 512" class="arrow">
                    <path d="M278.6 233.4c12.5 12.5 12.5 32.8 0 45.3l-160 160c-12.5 12.5-32.8 12.5-45.3 0s-12.5-32.8 0-45.3L210.7 256 73.4 118.6c-12.5-12.5-12.5-32.8 0-45.3s32.8-12.5 45.3 0l160 160z"/>
                </svg>
            </a>
            <div class="flex row align img-container">
                <a v-for="(hero, index) in heroes" :key="index" @click="currentHero = index" class="flex align" >
                    <img :src="hero.image" alt="Baggrundsbillede for gældende side" title="Skift Slideshow" :style="{ filter: currentHero === index ? 'saturate(100%)' : 'saturate(10%)' }">
                </a>
            </div>
            <a class="arrow-cont right flex align" @click="nextHero"> 
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 320 512" class="arrow">
                    <path d="M278.6 233.4c12.5 12.5 12.5 32.8 0 45.3l-160 160c-12.5 12.5-32.8 12.5-45.3 0s-12.5-32.8 0-45.3L210.7 256 73.4 118.6c-12.5-12.5-12.5-32.8 0-45.3s32.8-12.5 45.3 0l160 160z"/>
                </svg>
            </a>
        </div>
    </div>
    </template>
    

<style lang="scss" scoped>
    @import "../../css/style.scss";
    
    .hero {
        position: relative;
        height: 700px;
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
            opacity: 0;
            animation: fade-in 1s forwards;
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
    
        .preview {
            background-color: $champagne;
            margin-top: auto;
            bottom: 0;
            flex-wrap: wrap;
            height: -webkit-fit-content;
    
            .arrow-cont {
                width: 75px;
                height: 100px;
                background-color: $eerie-black;
                cursor: pointer;
                transition: all 0.2s;
    
                &:hover {
                    background-color: $denim;
                }
    
                .arrow {
                    fill: $pure-white;
                    width: 25px;
                    height: 25px;
                    z-index: 2;
                }
            }
    
            .left {
                .arrow {
                    transform: rotate(180deg);
                }
            }
    
            a {
                position: relative;
                width: 150px;
                height: 100px;
                max-width: 150px;
                img {
                    background-color: $champagne;
                    position: absolute;
                    height: inherit;
                    width: inherit;
                    object-position: center;
                    object-fit: cover;
                    z-index: 1;
                    max-width: 140px;
                    transition: $transition;
                    cursor: pointer;
                    filter: saturate(10%);
                }
    
                p {
                    visibility: hidden;
                    z-index: 2;
                    background-color: rgba(0, 0, 0, 0.467);
                    width: 80%;
                    padding: 10px;
                    font-size: $font-p-small;
                    color: $pure-white;
                    cursor: pointer;
                }
    
                &:hover {
                    img {
                        border: solid 4px $pure-white;
                        border-radius: $box-border-element;
                    }
    
                    p {
                        visibility: visible;
                    }
                }
            }
            .img-container{
                flex-wrap: wrap;
                max-width: 70vw;
            }
        }
    }
    
    @keyframes fade-in {
        0% {
            opacity: 0;
        }
        100% {
            opacity: 1;
        }
    }
    
    @media (max-width: 550px) {
        .hero {
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
    