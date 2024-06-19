<script setup>
import { ref } from "vue";

const eventData = ref([]);
const eventNavn = ref("");
const tidspunkt = ref("");
const NexteventName = ref("");
const NextEventDate = ref("");

const baseEndpoint = `https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/event?acf_format=standard`;

// Laver en async function til at fetch event data
async function fetchSkansen() {
  try {
    // fetcher data fra endpoint, ved at pause udførelsen af functionen til der er modtaget et response fra api-kaldet
    const res = await fetch(baseEndpoint);
    const data = await res.json();
    // Opdatere eventData med den hentede data
    eventData.value = data;
    // Kører funktionen updateTodaysEvent ved burg af data
    updateTodaysEvent(data);
    // console.log(data);
  } catch (err) {
    console.error(err);
  }
}

// Laver en funktion til at finde og indsætte dagen event eller det næste event
function updateTodaysEvent(data) {
  // Henter dagens dato
  const today = new Date();

  // Laver variabler der gør det muligt at opdatere og holde eventsne
  let eventFound = false;
  let nextEventDate = null;
  let nextEventName = null;

  // Laver en for, der går igennem hvert event
  for (const event of data) {
    // Finder datoerne for hvert event
    const eventDatesArray = eventDates(event).map((date) => new Date(date));

    // tjekker om i dags dato matcher nogle af de fundne datoer
    if (
      eventDatesArray.some(
        (date) => date.toDateString() === today.toDateString()
      )
    ) {
      // Hvis dagens dato bliver fundet i events, skal eventNavn og tidspunkt variablerne opdateres med dataen fra wordpress
      eventNavn.value = event.title.rendered;
      tidspunkt.value = event.acf.tidspunkt;
      // Ændre eventFound til true, so den ikke behøver finde den igen
      eventFound = true;
      // Går ud af loopet når der er fundet data
      break;
    } else {
      // hvis den ikke finder data der matcher dagens dato, skal den finde den næste dato
      const closestDate = eventDatesArray.find((date) => date > today);
      // Vi tjekker her om closetsDate eksistere og har en value
      // samtidig tjekker vi om der er et opkommende event
      // og om closestDate er tidligere end nextEventDate, hvis den er så et closesDate det næste event
      if (closestDate && (!nextEventDate || closestDate < nextEventDate)) {
        // udfra det opdatere vi variablerne med data for det næste event
        nextEventDate = closestDate;
        nextEventName = event.title.rendered;
      }
    }
  }
  // Hvis der ikke er fundet et event for i dag
  if (!eventFound) {
    // Så opdateres navnet til 'Ingen events i dag' og tidspunket laves til en tom string
    eventNavn.value = "Ingen events i dag";
    tidspunkt.value = "";
  }

  // Hvis der er fundet et nextEventDate og nextEventName
  if (nextEventDate && nextEventName) {
    // så skal NextEventDate formatteres i dansk format og variablerne opdateres
    NextEventDate.value = nextEventDate.toLocaleDateString("da-DK", {
      day: "numeric",
      month: "short",
    });
    NexteventName.value = nextEventName;
  }
}

// kalder funktionen
fetchSkansen();

// Henter i dags dato
function getCurrentDate() {
  const day = { day: "numeric" };
  const today = new Date();
  return today.toLocaleDateString("da-DK", day);
}

// Henter den nuværende måned
function getCurrentMonth() {
  const month = { month: "short" };
  const today = new Date();
  return today.toLocaleDateString("da-DK", month);
}

// Eftersom der er flere datoer tilknyttet fjer event, skal disse adskilles
function eventDates(event) {
  // Laver en tom array til at holde datoerne
  const dates = [];
  // Looper igennem hver dato, der er angivet 6 ACF felter i wordpress til dette
  for (let i = 1; i <= 6; i++) {
    // de forskellige felter i acf hedder dato_1, dato_2 etc. Vi navngiver dem her dateField
    const dateField = `dato_${i}`;
    // Da ikke alle acf felter er udfyldt, tjekker vi her om det enkelte objekt har et dateField tilknyttet
    if (event.acf[dateField]) {
      // Hvis dette felt eksistere, bliver dens dato pushed til arrayet for datoerne
      dates.push(event.acf[dateField]);
    }
  }
  return dates;
}

const currentDate = ref(getCurrentDate());
const currentMonth = ref(getCurrentMonth());
</script>

<template>
  <section class="blue-sunrise flex" aria-live="polite" role="region" aria-label="Event Calendar">
    <h2>Eventkalender</h2>
    <div class="dateHighlight">
      <p class="p-small">I dag</p>
      <p class="date">{{ currentDate }}</p>
      <p class="date">{{ currentMonth }}</p>
    </div>
    <div>
      <p v-html="eventNavn"></p>
      <hr />
      <!-- If there is no time for today's event and thus no event, display date and name for the next event -->
      <p>{{ tidspunkt }}</p>
      <div v-if="tidspunkt == ''">
        <p>
          Næste event:
          {{ NextEventDate }} <p v-html="NexteventName"></p>
        </p>
      </div>
    </div>
    <!-- Use slot for the button to insert the necessary values -->
    <slot />
  </section>
</template>


<style scoped lang="scss">
@import "../../css/style.scss";
section {
  padding: 0% 15%;
  color: white;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 3%;

  .dateHighlight {
    background-color: $pure-white;
    color: $eerie-black;
    padding: 0.5% 2%;
    box-shadow: $box-shadow;
    text-align: center;
    .date {
      text-transform: uppercase;
      font-weight: $font-weight-bold;
    }
  }
  div {
    hr {
      border: 1px solid $pure-white;
      width: 400px;
      margin: 1% 0%;
    }
  }
}
</style>
