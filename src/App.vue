<script>
import axios from "axios";

export default {
  name: "App",

  data() {
    return {
      loading: true,
      surahs: [],
      currentSurah: [],
    };
  },

  mounted() {
    // Get the list of all surahs
    axios.get("https://api.alquran.cloud/v1/surah").then((res) => {
      this.surahs = res.data.data;
    });
    this.querySurah(1);
  },

  methods: {
    querySurah(surahNumber) {
      this.loading = true;
      // Get an initial surah to load
      axios
        .get("http://api.alquran.cloud/v1/surah/" + surahNumber)
        .then((res) => {
          this.currentSurah = res.data.data;
          this.loading = false;
        });
    },

    selectSurah(e) {
      this.querySurah(e.target.value);
    },
  },
};
</script>

<template>
  <div class="min-h-screen bg-slate-800">
    <h1 class="text-6xl text-teal-600 font-extrabold text-center">Iqra</h1>
    <div class="container py-6">
      <div class="bg-gray-900 text-gray-200 my-6 shadow-2xl rounded">
        <!-- Header -->
        <header class="bg-teal-700 p-3 rounded-t">
          <div class="md:flex md:text-right text-center md:items-center my-4">
            <!-- Surah Selection -->
            <div class="flex-1 flex-grow-0 px-4 my-3 md:my-auto">
              <select @change="selectSurah" class="iqra-input">
                <option disabled selected>Select a Surah</option>
                <option
                  v-for="surah in surahs"
                  :key="surah.number"
                  :value="surah.number">
                  {{ surah.englishName }}
                </option>
              </select>
            </div>

            <!-- Surah Meta -->
            <div class="flex-1 px-4 text-center">
              <h3 class="text-lg md:font-xl font-semibold mb-1">
                {{ currentSurah.name }}
                ({{ currentSurah.englishName }})
              </h3>
              <p>
                {{ currentSurah.englishNameTranslation }} |
                {{ currentSurah.revelationType }} | Ayahs-
                {{ currentSurah.numberOfAyahs }}
              </p>
            </div>
          </div>

          <!-- TODO: Implement the audio -->
        </header>

        <article class="p-6 lg:p-8">
          <!-- loader -->
          <div v-if="loading" id="loader" class="my-10 text-center">
            <span class="loader"></span>
            <p>Loading...</p>
          </div>

          <!-- Ayahs -->
          <ul
            v-else
            class="rtl flex flex-wrap whitespace-pre-wrap space-y-6 gap-2">
            <li
              class="flex items-center gap-2"
              v-if="currentSurah.hasOwnProperty('ayahs')"
              v-for="ayah in currentSurah.ayahs"
              :key="ayah.numberInSurah">
              <span class="ayah-number flex-shrink-0">{{
                ayah.numberInSurah
              }}</span>

              <p class="ayah">
                {{ ayah.text }}
              </p>
            </li>
          </ul>
        </article>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  @apply max-w-2xl mx-auto;
}

.iqra-input {
  @apply bg-gray-800 rounded px-4 py-2;
}

.ayah-number {
  @apply border border-teal-600 rounded-full w-8 h-8 text-center flex justify-center items-center text-sm font-bold;
}
.ayah {
  @apply text-lg md:text-2xl font-semibold leading-loose;
}

.rtl {
  direction: rtl;
}

/* Loader */
.loader {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  display: inline-block;
  position: relative;
  border: 3px solid;
  border-color: teal teal transparent;
  box-sizing: border-box;
  animation: rotation 1s linear infinite;
}
.loader::after {
  content: "";
  box-sizing: border-box;
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  margin: auto;
  border: 3px solid;
  border-color: transparent teal;
  width: 24px;
  height: 24px;
  border-radius: 50%;
  animation: rotationBack 0.5s linear infinite;
  transform-origin: center center;
}

@keyframes rotation {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

@keyframes rotationBack {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(-360deg);
  }
}
</style>
