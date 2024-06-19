<script setup>
import { ref, computed } from "vue";

const eventData = ref([]);

const baseEndpoint = `https://runepjetursson.com/mmd4/skansen/wp-json/wp/v2/event?acf_format=standard&per_page=100`;

// Laver en async function til at fetch event data
async function fetchSkansen() {
  try {
    // fetcher data fra endpoint, ved at pause udførelsen af functionen til der er modtaget et response fra api-kaldet
    const response = await fetch(baseEndpoint);
    const data = await response.json();

    // Henter datoen for dagen før, for at kunne inkludere events der foregår på dagens dato
    // getDate tager dagens dato og minusser med 1, for at få dagen før, hvilket gemmes i yesterday
    const now = new Date();
    const yesterday = new Date(now);
    yesterday.setDate(yesterday.getDate() - 1);

    // Udregner indenfor hvilke måneder der skal vises events
    // getMonth tager værdien for den nuværende måned og ligger 2 til, dette gemmes i threeMonths
    const threeMonths = new Date(now);
    threeMonths.setMonth(threeMonths.getMonth() + 2);

    // fladgør dataen, så alt er samlet i et array i stedet for flere
    eventData.value = data.flatMap((event) =>
      // Hent alle datoer fra event
      eventDates(event)
        // Lav et nyt objekt for hver dato med event informationer
        .map((date) => ({ ...event, date }))
        // filtrere events so der kun includeres dem indenfor den specifikke periode
        .filter(({ date }) => {
          const eventDate = new Date(date);
          // perioden angives fra dagen før til tre måneder frem
          return eventDate >= yesterday && eventDate <= threeMonths;
        })
    );
  } catch (error) {
    console.error(error);
  }
}

fetchSkansen();

// Formatere datoerne til dansk format
function formatDate(date) {
  return new Date(date).toLocaleDateString("da-DK", {
    day: "2-digit",
    month: "short",
  });
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

// Til at gruppere de forskellige events i deres måneder, benyttes der computed property
const groupedEventsByMonth = computed(() => {
  return (
    eventData.value
      // Sortere eventsne efter dato
      .sort((a, b) => new Date(a.date) - new Date(b.date))
      // gruppere events efter måned og år
      .reduce((groupedEvents, event) => {
        // sikre det er i dansk format
        const monthYear = new Date(event.date).toLocaleDateString("da-DK", {
          year: "numeric",
          month: "long",
        });
        // tjekker om de grupperede events er indenfor den nurværende måned og år
        if (!groupedEvents[monthYear]) groupedEvents[monthYear] = [];
        // tilføjer eventet til det array det tilhører
        groupedEvents[monthYear].push(event);
        return groupedEvents;
      }, {})
  );
});
</script>

<template>
  <section
    class="content-cont"
    aria-live="polite"
    role="region"
    aria-label="Event-List"
  >
    <!-- Loop through each event array in the grouped array -->
    <div v-for="(events, month) in groupedEventsByMonth" :key="month">
      <!-- Display the month for the grouped array -->
      <h2>{{ month }}</h2>
      <hr />
      <!-- Loop through each event in the month -->
      <div class="flex wrap">
        <article v-for="(event, index) in events" :key="index">
          <h3 v-html="event.title.rendered"></h3>
          <div class="dateHighlight">
            <p>{{ formatDate(event.date) }}</p>
            <p class="p-small">
              {{ event.acf.ugedag }}
            </p>
            <p class="p-small">
              {{ event.acf.tidspunkt }}
            </p>
          </div>
          <p v-html="event.acf.beskrivelse"></p>

          <p class="p-small">
            Ved at klikke på nedenstående knap, åbner du vores booking side for
            events. <br />
            For at finde eventet: Gå til '{{
              event.acf.eventshoppens_kategori
            }}'
          </p>
          <div class="button">
            <slot />
          </div>
        </article>
      </div>
    </div>
  </section>
</template>

<style scoped lang="scss">
@import "../../css/style.scss";

section {
  margin: 3%;
  h2 {
    text-transform: capitalize;
    margin-top: 5%;
  }
  hr {
    border: 1px solid $eerie-black;
    width: 400px;
    margin: 1% 0%;
    @media (max-width: 930px) {
      width: 300px;
    }
  }
  div {
    .flex {
      justify-content: space-between;
      gap: 25px;
    }
    article {
      max-width: 45%;
      @media (max-width: 930px) {
        max-width: 100%;
      }
      h3 {
        padding: 3% 0%;
      }
      .dateHighlight {
        background-color: $champagne;
        border-radius: 5px;
        padding: 1% 2%;
        width: -webkit-fit-content;
        min-width: 40%;
        p {
          margin: 0;
        }
      }
      p {
        margin: 3% 0%;
      }
      .p-small {
        margin-bottom: 5%;
      }
      .button {
        width: 170px;
      }
    }
  }
}
</style>
