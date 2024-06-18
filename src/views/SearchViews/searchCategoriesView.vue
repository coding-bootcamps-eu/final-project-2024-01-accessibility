<template>
  <BackgroundGradient />

  <blurEffect v-if="searchcategoryPopup && !hideTutorials"></blurEffect>
  <!--  Popup zur Empfehlung der kategoriewahl  -->
  <div class="category-popup-container" v-if="searchcategoryPopup && !hideTutorials">
    <svg
      class="category-svg-popup"
      viewBox="0 0 100 100"
      width="200px"
      height="200px"
      style="filter: url(#f1)"
    >
      <defs>
        <filter id="f1" x="-20%" y="-20%" width="140%" height="140%">
          <feGaussianBlur in="SourceAlpha" stdDeviation="1.5" result="blur" />
          <feColorMatrix
            in="blur"
            type="matrix"
            values="0 0 0 0 0.5 0 0 0 0 0.5 0 0 0 0 0.5 0 0 0 1 0"
            result="coloredBlur"
          />
          <feOffset in="coloredBlur" dx="7" dy="7" result="offsetBlur" />
          <feMerge>
            <feMergeNode in="offsetBlur" />
            <feMergeNode in="SourceGraphic" />
          </feMerge>
        </filter>
        <clipPath id="clipPolygon">
          <path
            d="
          M 26.937 15.811 L 35.813 12.224 L 33.13 20.417 L 95.397 67.558 C 96.752 68.574 97.712 69.986 97.825 73.262 V 94.761 C 97.729 98.062 95.831 99.799 92.833 99.835 H 9.272 C 0.029 99.83 0.272 94.812 0.181 88.457 V 4.849 C 0.23 1.034 6.232 -0.971 8.842 1.857 Z"
          />
        </clipPath>
      </defs>
      <rect width="200px" height="200px" clip-path="url(#clipPolygon)" style="fill: var(--black)" />
    </svg>
    <popupButtonTutorial
      class="category-popup-button"
      :buttonLabel="'Verstanden!'"
      @click="categoryClosePopup"
    />
    <p class="category-popup-text">
      Wenn <br />
      Sie erstmal<br />
      die App erstmal<br />
      nur testen möchten, <br />
      sollten Sie "Alle" <br />
      Kategorien auswählen
    </p>
  </div>

  <div class="header-buttons">
    <RouterLink :to="{ name: 'searchpoi' }"> <BackArrow /></RouterLink>
    <RouterLink :to="{ name: 'home' }"> <headerLogo /></RouterLink>
  </div>

  <HeadLine class="headline-searchcategorie" :Headline="'Kategorien'" />
  <CategorieButton
    class="category-button button-search-categorie"
    v-for="categorie of store.localData.categories"
    :key="categorie.id"
    :Kategorie="categorie.categoryName"
    @click="saveButtonValue(categorie)"
    :id="categorie.id"
    :class="{ pressed: isPressed === categorie.id }"
  />

  <RouterLink class="button-search-nav" :to="{ name: 'searchoptionalcategorie' }">
    <NavButton Navigation="Weiter"
  /></RouterLink>
</template>
<script>
import CategorieButton from '@/components/CategorieButton.vue'
import NavButton from '@/components/NavButton.vue'
import headerLogo from '@/components/headerLogo.vue'
import BackArrow from '@/components/BackArrow.vue'
import HeadLine from '@/components/HeadLine.vue'

import BackgroundGradient from '@/components/BackgroundGradient.vue'

import popupButtonTutorial from '@/components/popupButtonTutorial.vue'

import { storeData } from '@/stores/store.js'
import blurEffect from '@/components/blurEffect.vue'

export default {
  components: {
    CategorieButton,
    NavButton,
    headerLogo,
    BackArrow,
    HeadLine,
    popupButtonTutorial,
    blurEffect,
    BackgroundGradient
  },

  data() {
    return {
      store: storeData(),
      isPressed: null,
      searchcategoryPopup: true,
      hideTutorials: localStorage.getItem('hideTutorials') === 'true'
    }
  },

  created() {
    let currentUserID = localStorage.getItem('currentUserID').replace(/"/g, '')
    this.store.temporaryData.ownPoisList = this.store.temporaryData.currentPois.filter(
      (poi) => poi.createdBy === currentUserID
    )
    if (this.store.temporaryData.ownPoisList.length < 6) {
      // es werden mit createDummyPois() 6 dummyeinträge erstellt, sodass es nur einmalig pro account durchgeführt wird
      this.store.createDummyPois()
    }
  },

  methods: {
    saveButtonValue(categorie) {
      this.isPressed = categorie.id
      document.getElementById(categorie.id).classList.add('pressed')
      if (this.isPressed !== null) {
        document.getElementById(this.isPressed).classList.remove('pressed')
      }
      const buttonValue = document.getElementById(categorie.id).textContent
      this.store.temporaryData.choosenCategory = buttonValue
      localStorage.setItem('buttonValue', buttonValue)
    },
    categoryClosePopup() {
      this.searchcategoryPopup = false
    }
  }
}
</script>

<style scoped>
.header-buttons {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 20px;
  background-color: transparent;
}
.pressed {
  color: var(--white);
  background-color: var(--black);
}
.button-search-categorie {
  align-self: center;
}
.headline-searchcategorie {
  align-self: center;
}
.button-search-nav {
  align-self: center;
}
.category-popup-container {
  position: relative;
  align-self: center;
  width: 300px;
  z-index: 101;
}
.category-svg-popup {
  position: absolute;
  width: 300px;
  height: 300px;
  top: 195px;
  left: 20px;
}
.category-popup-button {
  position: absolute;
  top: 440px;
  left: 110px;
}
.category-popup-text {
  position: absolute;
  top: 250px;
  left: 45px;
  color: var(--white);
}
@media screen and (min-width: 600px) {
  .category-popup-container {
    top: 51px;
  }
}
</style>
