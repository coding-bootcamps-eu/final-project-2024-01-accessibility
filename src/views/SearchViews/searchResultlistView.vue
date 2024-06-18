<template>
  <BackgroundGradient />

  <blurEffect v-if="resultlistPopup && !hideTutorials"></blurEffect>
  <!--  INFO POPUP für die resultlistview -->
  <div class="resultlist-popup-container" v-if="resultlistPopup && !hideTutorials">
    <svg
      class="resultlist-svg-popup"
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
            M 81.59 52.001 C 90.049 47.893 91.451 43.504 85.741 34.014 C 82.204 26.959 91.442 24.521 90.009 9.919 L 85.998 9.964 L 91.029 0.985 L 95.964 9.964 L 91.913 9.969 C 93.453 22.828 92.012 23.629 90.13 28.206 C 88.716 35.985 98.195 36.76 98.459 48.265 V 105.669 C 98.964 112.982 98.935 112.982 95.977 112.953 C 92.033 112.982 96.023 120.001 91.979 119.921 H 7.04 C 3.167 119.949 6.953 113.025 2.972 112.967 C 0.072 113.025 0.043 112.996 0.429 104.458 V 56.044 C 0.513 52.012 0.513 52.036 4.45 51.989 Z"
          />
        </clipPath>
      </defs>
      <rect width="200px" height="200px" clip-path="url(#clipPolygon)" style="fill: var(--black)" />
    </svg>
    <popupButtonTutorial
      class="resultlist-popup-button"
      :buttonLabel="'Sehr schön!'"
      @click="resultlistPopup = false"
    />
    <p class="resultlist-popup-text">
      Wenn du auf die Erde klickst, kommst du zur Karte, die alle Orte anzeigt, die du auch hier in
      der Liste siehst. Alternativ klicke direkt auf einen Eintrag der dich interessiert und
      <br />erhalte detaillierte Informationen.
    </p>
  </div>

  <div class="header-buttons">
    <RouterLink :to="backLink"> <BackArrow /></RouterLink>
    <RouterLink :to="{ name: 'home' }"> <headerLogo /></RouterLink>
    <RouterLink :to="{ name: 'searchresultmap' }"><EarthMap /></RouterLink>
  </div>

  <HeadLine Headline="Ergebnisliste" class="headline-text"></HeadLine>

  <div class="search-list-container" v-for="poi of store.temporaryData.currentPois" :key="poi.id">
    <button class="searchlist-button" v-if="store.renderFilteredPois(poi)">
      <img class="categorie-icon" :src="this.store.getIconOfCategory(poi)" alt="Kategorie Icon" />
      <favoriteStarSvg
        :svgWidth="'30px'"
        :svgheight="'30px'"
        class="favorite-star-resultlist"
        :poiId="poi.id"
      ></favoriteStarSvg>
      <router-link :to="'/infopoi/' + poi.id">
        <div class="searchlist-poiname">{{ poi.poiName }}</div>
        <div class="detailcategories-container">
          <div
            class="searchlist-detailcategories"
            v-for="detailcategorie of poi.detailCategories"
            :key="'detail-' + poi.id + '-' + detailcategorie"
          >
            ✅ {{ detailcategorie }}
          </div>
          <div class="address-container">
            <img
              class="location-list-icon"
              src="@/assets/icons/location-indicator.png"
              alt="location-logo"
            />
            <span class="address-text"
              >&nbsp; {{ poi.city }}, {{ poi.street }} {{ poi.number }}</span
            >
          </div>
        </div>
        <div class="searchlist-distance">
          Entfernung: <b>{{ poi.currentSearchDistance }}</b> Meter
        </div>
      </router-link>
    </button>
  </div>
</template>

<script>
import { storeData } from '@/stores/store.js'
import EarthMap from '@/components/EarthMap.vue'
import BackArrow from '@/components/BackArrow.vue'
import headerLogo from '@/components/headerLogo.vue'
import HeadLine from '@/components/HeadLine.vue'
import BackgroundGradient from '@/components/BackgroundGradient.vue'
import popupButtonTutorial from '@/components/popupButtonTutorial.vue'
import blurEffect from '@/components/blurEffect.vue'
import favoriteStarSvg from '@/components/favoriteStar.vue'

export default {
  components: {
    headerLogo,
    EarthMap,
    BackArrow,
    HeadLine,
    BackgroundGradient,
    popupButtonTutorial,
    blurEffect,
    favoriteStarSvg
  },
  data() {
    return {
      store: storeData(),
      resultlistPopup: true,
      hideTutorials: localStorage.getItem('hideTutorials') === 'true'
    }
  },
  computed: {
    backLink() {
      if (this.store.temporaryData.choosenCategory.trim() === 'Alle') {
        return { name: 'searchcategorie' }
      } else {
        return { name: 'searchoptionalcategorie' }
      }
    }
  },

  mounted() {
    this.store.checkForFilterOptions()
  }
}
</script>

<style scoped>
.searchlist-button {
  position: relative;
  width: 100%;
  min-height: 100px;
  background-color: var(--white);
  color: var(--red);
  margin-bottom: 10px;
  border-radius: 16px;
  border: 1px solid var(--black);
  align-self: center;
  justify-content: space-around;
}
.searchlist-button:hover {
  background-color: rgb(252, 220, 180);
  cursor: pointer;
}

.searchlist-poiname {
  font-size: 20px;
  font-weight: bold;
  color: var(--red);
  align-self: flex-start;
}
.searchlist-detailcategories {
  text-align: start;
  font-size: 14px;
  width: 70%;
}
.detailcategories-container {
  display: flex;
  flex-direction: column;
  min-height: 50px;
  justify-content: flex-start;
  min-width: 370px;
  padding-bottom: 5px;
}
.searchlist-distance {
  text-align: end;
  margin-right: 5px;
  font-size: 14px;
}
.header-buttons {
  justify-content: space-between;
  padding: 10px 20px;
  background-color: transparent;
}
.headline-text {
  width: 100%;
  margin-bottom: 3rem;
}
.search-list-container {
  display: flex;
  flex-direction: column;
  text-align: center;
  width: 100%;
  margin-left: auto;
  margin-right: auto;
}
.categorie-icon {
  position: absolute;
  top: 10px;
  right: 10px;
  width: 30px;
  border-radius: 5px;
}
.resultlist-popup-container {
  position: relative;
  align-self: flex-end;
  width: 300px;
  margin-right: 28px;
  z-index: 101;
}
.resultlist-svg-popup {
  position: absolute;
  width: 300px;
  height: 400px;
  top: 15px;
}
.resultlist-popup-button {
  position: absolute;
  top: 365px;
  left: 88px;
}
.resultlist-popup-text {
  position: absolute;
  top: 220px;
  left: 20px;
  color: var(--white);
}
.favorite-star-resultlist {
  position: absolute;
  top: 44px;
  right: 8px;
}
.location-list-icon {
  width: 10px;
}
.address-container {
  align-self: start;
  margin-top: 4px;
  margin-left: 4px;
  text-align: left;
}

@media screen and (min-width: 600px) {
  .searchlist-button {
    width: 70%;
  }
  .resultlist-popup-container {
    margin-right: 28px;
  }
  .resultlist-svg-popup {
    top: 40px;
  }
  .resultlist-popup-button {
    top: 390px;
  }
  .resultlist-popup-text {
    top: 245px;
  }
}
@media screen and (min-width: 768px) {
  .searchlist-button {
    width: 50%;
  }
}
</style>
