<template>
  <BackgroundGradient />

  <blurEffect v-if="searchPoiPopup && !hideTutorials"></blurEffect>

  <header>
    <div class="header-buttons">
      <RouterLink :to="{ name: 'home' }"> <BackArrow /></RouterLink>
      <RouterLink :to="{ name: 'home' }"> <headerLogo /></RouterLink>
    </div>
  </header>
  <!--  INFO POPUP für den standort bestimmungsbutton -->
  <div class="searchpoi-popup-container" v-if="searchPoiPopup && !hideTutorials">
    <svg
      class="searchpoi-svg-popup"
      viewBox="0 0 100 100"
      width="200px"
      height="200px"
      style="filter: url(#f1)"
    >
      <defs>
        <filter id="f1" x="-10%" y="-10%" width="140%" height="140%">
          <feGaussianBlur in="SourceAlpha" stdDeviation="0.5" result="blur" />
          <feColorMatrix
            in="blur"
            type="matrix"
            values="0 0 0 0 0.5 0 0 0 0 0.5 0 0 0 0 0.5 0 0 0 1 0"
            result="coloredBlur"
          />
          <feOffset in="coloredBlur" dx="3" dy="3" result="offsetBlur" />
          <feMerge>
            <feMergeNode in="offsetBlur" />
            <feMergeNode in="SourceGraphic" />
          </feMerge>
        </filter>
        <clipPath id="clipPolygon">
          <path
            d="
            M 82.004 38.891 C 91.22 34.704 91.451 23.504 85.741 14.014 C 82.204 6.959 78.02 8.645 72.502 14.77 L 75.513 14.77 L 70.963 19.321 L 67.081 14.904 L 69.825 14.77 C 71.341 12.106 73.071 10.66 77.691 7.499 C 74.058 7.935 73.288 7.678 71.066 7.123 L 69.869 8.96 L 67.433 3.661 L 73.117 3.404 L 72.092 5.199 C 80.583 6.302 85.822 4.371 90.724 9.675 C 95.418 15.967 98.195 16.76 98.459 28.265 V 85.669 C 98.964 92.982 98.935 92.982 95.977 92.953 C 92.033 92.982 96.023 100.001 91.979 99.921 H 7.04 C 3.167 99.949 6.953 93.025 2.972 92.967 C 0.072 93.025 0.043 92.996 0.429 84.458 V 42.999 C 0.567 39.019 0.567 38.927 5.287 39.019 Z"
          />
        </clipPath>
      </defs>
      <rect width="200px" height="200px" clip-path="url(#clipPolygon)" style="fill: var(--black)" />
    </svg>
    <popupButtonTutorial
      class="searchpoi-popup-button"
      :buttonLabel="'Geht klar!'"
      @click="searchPoiPopup = false"
    />
    <p class="searchpoi-popup-text">
      Lege fest, in welchem Radius du Orte auf der Karte angezeigt bekommen möchtest und bitte
      stimme der Standortbestimmung zu, damit wir Dir dann auch interessante Orte in deiner Nähe
      zeigen können.
    </p>
  </div>
  <!--  INFO POPUP für den Suchradius schiebebalken 
  
  M 82.004 38.891 C 91.22 34.704 91.451 23.504 85.741 14.014 C 82.204 6.959 78.02 8.645 72.502 14.77 L 75.513 14.77 L 70.963 19.321 L 67.081 14.904 L 69.825 14.77 C 71.341 12.106 73.071 10.66 76.728 8.382 C 80.583 6.302 85.822 4.371 90.724 9.675 C 95.418 15.967 98.195 16.76 98.459 28.265 V 85.669 C 98.964 92.982 98.935 92.982 95.977 92.953 C 92.033 92.982 96.023 100.001 91.979 99.921 H 7.04 C 3.167 99.949 6.953 93.025 2.972 92.967 C 0.072 93.025 0.043 92.996 0.429 84.458 V 42.999 C 0.567 39.019 0.567 38.927 5.287 39.019 Z
  -->

  <HeadLine Headline="Wo möchtest du suchen?" class="headline"></HeadLine>
  <h3 class="search-radius-input-header">SUCHRADIUS</h3>

  <input
    type="range"
    min="50"
    max="3000"
    step="50"
    value="1000"
    name="rangeInput"
    id="idRangeInput"
  />

  <label for="rangeInput" class="search-radius-label"
    >Reichweite {{ store.temporaryData.searchDistance }} m</label
  >

  <NavButton
    Navigation="Bestimme meinen Standort"
    @click="store.getOwnPosition"
    class="router-link"
  ></NavButton>
  <br />

  <div class="location-info-container">
    <h3 class="current-location-info">Sie befinden sich hier:</h3>
    <p class="current-location-info"><b>Stadt:</b> {{ store.temporaryData.city }}</p>
    <p class="current-location-info"><b>Stadteil:</b> {{ store.temporaryData.district }}</p>
    <p class="current-location-info"><b>Straße:</b> {{ store.temporaryData.street }}</p>
    <p class="current-location-info"><b>Postleitzahl:</b> {{ store.temporaryData.zipCode }}</p>

    <p v-show="store.temporaryData.houseNumber" class="current-location-info">
      <b>Hausnummer:</b> {{ store.temporaryData.houseNumber }}
    </p>

    <p v-show="!store.temporaryData.houseNumber" class="current-location-info">
      <b>Hausnummer:</b> nicht vorhanden (Daten nicht genau zuweisbar)
    </p>
  </div>

  <p v-show="!store.temporaryData.street" class="searchpoiview-info">
    Leider funktioniert der Ortungsservice nicht immer, <br />
    bitte dann erneut den Standort bestimmen lassen, falls Ihre Adresse nach 10 sec. nicht angezeigt
    wird
  </p>

  <RouterLink :to="{ name: 'searchcategorie' }" class="router-link"
    ><NavButton :disabled="!store.temporaryData.street" Navigation="Weiter">Weiter</NavButton>
  </RouterLink>
</template>

<script>
import { storeData } from '@/stores/store.js'
import headerLogo from '@/components/headerLogo.vue'
import BackArrow from '@/components/BackArrow.vue'
import NavButton from '@/components/NavButton.vue'
import HeadLine from '@/components/HeadLine.vue'

import BackgroundGradient from '@/components/BackgroundGradient.vue'

import popupButtonTutorial from '@/components/popupButtonTutorial.vue'
import blurEffect from '@/components/blurEffect.vue'

export default {
  components: {
    headerLogo,
    BackArrow,
    NavButton,
    HeadLine,
    BackgroundGradient,
    popupButtonTutorial,
    blurEffect
  },
  data() {
    return {
      store: storeData(),
      searchPoiPopup: true,
      hideTutorials: localStorage.getItem('hideTutorials') === 'true'
    }
  },

  mounted() {
    const rangeinputField = document.querySelector('#idRangeInput')
    rangeinputField.addEventListener('input', this.saveInputValue)
  },
  methods: {
    saveInputValue(e) {
      this.store.temporaryData.searchDistance = e.target.value
    },
    searchPoiClosePopup() {
      this.searchPoiPopup = false
    }
  }
}
</script>

<style scoped>
button {
  margin-top: 30px;
}
label {
  margin-left: 20px;
}

.header-buttons {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 20px;
  background-color: transparent;
}

.headline {
  width: 100%;
}
.router-link {
  align-self: center;
}
#idRangeInput {
  width: 40%;
  margin-left: 30%;
}
.search-radius-label {
  margin-left: 30%;
}
.search-radius-input-header {
  text-align: center;
}
.current-location-info {
  margin-left: 15px;
}
.location-info-container {
  margin-left: auto;
  margin-right: auto;
  background-color: var(--white);
  color: var(--black);
  border-radius: 16px;
  width: 100%;
}
.searchpoiview-info {
  text-align: center;
  font-size: 20px;
  font-weight: bold;
}
.searchpoi-popup-container {
  position: relative;
  align-self: center;
  width: 300px;
  z-index: 101;
}
.searchpoi-svg-popup {
  position: absolute;
  width: 300px;
  height: 300px;
  top: 195px;
  left: 20px;
}
.searchpoi-popup-button {
  position: absolute;
  top: 415px;
  left: 173px;
}
.searchpoi-popup-text {
  position: absolute;
  top: 305px;
  left: 50px;
  color: var(--white);
}
@media screen and (min-width: 600px) {
  .location-info-container {
    width: 70%;
  }
  .searchpoi-popup-container {
    top: -38px;
  }
}
</style>
