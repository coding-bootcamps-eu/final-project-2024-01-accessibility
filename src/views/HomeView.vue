<template>
  <div class="background-home"></div>

  <blurEffect v-if="appPopup || searchFunctionPopup"></blurEffect>

  <!--  Popup zur Vorstellung der APP  -->
  <div class="app-popup-container" v-if="appPopup">
    <svg
      class="app-svg-popup"
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
            M 26.937 15.811 H 49.945 V 7.883 H 45.91 L 52.165 0.518 L 57.512 7.883 H 53.577 V 15.853 H 92.213 C 99.077 15.97 99.009 15.994 99.035 23.033 V 89.995 C 98.964 92.982 98.935 92.982 95.977 92.953 C 92.033 92.982 96.023 100.001 91.979 99.921 H 7.04 C 3.167 99.949 6.953 93.025 2.972 92.967 C 0.072 93.025 0.043 92.996 0.111 89.682 V 22.989 C 0.19 16.087 0.219 16.058 7.034 16 Z"
          />
        </clipPath>
      </defs>
      <rect width="200px" height="200px" clip-path="url(#clipPolygon)" style="fill: var(--black)" />
    </svg>

    <p class="app-popup-text">
      Diese App richtet sich an Menschen mit <br />
      Mobilitätseinschränkungen. <br />
      Oft bestehen erhöhte Anforderungen an die Umgebung, wie z. B. barrierefreie Toiletten oder
      extra breite Zugänge. <br />
      Auch ist die Information, ob Fahrstühle funktionieren für sie essentiell. Diese App soll daher
      Informationen über öffentliche Orte sammeln und dem Nutzer leicht verständlich zur Verfügung
      stellen.
    </p>
    <popupButtonTutorial
      class="app-popup-button"
      :buttonLabel="'Verstanden!'"
      @click="appClosePopup"
    />
  </div>

  <!--  Popup zur Empfehlung der Suchfunktion  -->
  <div class="searchfunction-popup-container" v-if="searchFunctionPopup">
    <svg
      class="searchfunction-svg-popup"
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
      class="searchfunction-popup-button"
      :buttonLabel="'Mach ich!'"
      @click="searchfunctionClosePopup"
    />
    <p class="searchfunction-popup-text">
      Wir <br />
      empfehlen <br />
      Ihnen, sich <br />
      zunächst die <br />
      Suchfunktion anzusehen, <br />
      um die wesentlichen <br />
      Funktionen dieser App <br />kennenzulernen. <br />
    </p>
  </div>

  <LandingPageTitle
    :username="this.store.temporaryData.DataFromCurrentUser.username"
    class="welcome-text"
  />
  <div class="grid-container">
    <RouterLink :to="{ name: 'searchpoi' }" class="search btn-search">
      <button class="btn-style search-button-width">
        <div class="icon"><img src="@/assets/icons/search.svg" alt="Search Icon" /> Suche</div>
      </button>
    </RouterLink>

    <RouterLink :to="{ name: 'newpoicategorie' }" class="new">
      <button class="btn-new-poi btn-style">
        <div class="icon"><img src="@/assets/icons/Pencil.svg" alt="newpoi Icon" /></div>
        Neuen Ort erstellen
      </button>
    </RouterLink>

    <RouterLink :to="{ name: 'favoritepoi' }" class="favorite">
      <button class="btn-favorite btn-style">
        <div class="icon"><img src="@/assets/icons/Star.svg" alt="favorite Icon" /></div>
        Deine Favoriten
      </button>
    </RouterLink>

    <RouterLink :to="{ name: 'ownpoi' }" class="own">
      <button class="btn-own-poi btn-style">
        <div class="icon"><img src="@/assets/icons/folder.svg" alt="ownpoi Icon" /></div>
        geteilte Orte
      </button>
    </RouterLink>

    <RouterLink :to="{ name: 'account' }" class="account">
      <button class="btn-account btn-style">
        <div class="icon"><img src="@/assets/icons/user-account.svg" alt="account Icon" /></div>
        Dein Account
      </button>
    </RouterLink>
  </div>
  <div class="tutorial-settings">
    <label>
      <input type="checkbox" v-model="hideTutorials" @change="savePreference" />
      Hide Tutorials
    </label>
  </div>
</template>
<script>
import { storeData } from '@/stores/store.js'
import router from '@/router'
import LandingPageTitle from '@/components/LandingPageTitle.vue'
import popupButtonTutorial from '@/components/popupButtonTutorial.vue'
import blurEffect from '@/components/blurEffect.vue'

export default {
  components: {
    LandingPageTitle,
    popupButtonTutorial,
    blurEffect
  },
  data() {
    return {
      store: storeData(),
      searchFunctionPopup: false,
      appPopup: true
    }
  },
  methods: {
    searchfunctionClosePopup() {
      this.searchFunctionPopup = false
    },
    appClosePopup() {
      this.appPopup = false
      this.searchFunctionPopup = true
    }
  },
  created() {
    const currentUserID = localStorage.getItem('currentUserID')
    if (!currentUserID) {
      router.push({ name: 'register' })
    } else {
      this.store.getPoiDataFromAPI()
      this.store.getUserDataFromAPI().then(() => {
        if (this.store.temporaryData.currentUserData.length > 0) {
          this.store.getDataFromCurrentUser()
        }
      })
      this.store.resetTemporaryLists()
      this.store.temporaryData.currentUserId = JSON.parse(currentUserID)
    }
  },
  updated() {
    // DELETE the Dummydata
    // sollte immer auskommentiert sein, nur zum gezielten löschen einmalig ausführen
    /*
    if (this.store.temporaryData.currentPois) {
      this.store.temporaryData.currentPois.forEach(async (element) => {
        if (element.creationDate === 'Dummys bekommen kein Datum') {
          this.store.deletePoifromAPI(element.id)
        }
      })
    }
    */
  }
}
</script>
<style scoped>
.welcome-text {
  margin-bottom: 20px;
  margin-top: 10px;
}

.background-home {
  position: absolute;
  z-index: -1;
  top: 0;
  left: 0;
  width: 100%;
  height: 300px;
  background-image: linear-gradient(
      180deg,
      transparent 138.8px,
      var(--red) 140px,
      var(--white) 144px,
      transparent 145px
    ),
    linear-gradient(
      180deg,
      transparent 146px,
      var(--red) 148px,
      var(--red) 153px,
      transparent 155px
    ),
    linear-gradient(
      180deg,
      transparent 156px,
      var(--white) 157px,
      var(--red) 161px,
      transparent 162px
    ),
    linear-gradient(
      180deg,
      var(--black) 167px,
      var(--black) 299.4px,
      /* Anpassen der Höhe des schwarzen Streifens */ transparent 300px
    );
  border-radius: 20px 20px 0 0;
}

.grid-container {
  align-self: center;
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr;
  grid-auto-flow: row;
  gap: 0.6rem;
  grid-template-areas:
    'search search'
    'newpoi favorite'
    'ownpoi account';
}

.btn-search {
  width: 330px;
  grid-area: search;
  grid-row-start: 1;
  _grid-row-end: span 1;
  border-radius: 0.5rem;
  background-color: var(--white);
}

.btn-favorite {
  grid-area: favorite;
  width: 10rem;
}
.btn-new-poi {
  grid-area: newpoi;
  width: 10rem;
}
.btn-own-poi {
  grid-area: ownpoi;
  width: 10rem;
}
.btn-account {
  grid-area: account;
  width: 10rem;
}
.btn-style {
  height: 7rem;
  border-radius: 0.5rem;
  background-color: var(--white);
  font-weight: bold;
}
.btn-style:hover {
  border: 5px solid var(--black);
  cursor: pointer;
}
.search-button-width {
  width: 330px;
}
.searchfunction-svg-popup {
  position: absolute;
  background-color: transparent;
  width: 300px;
  height: 300px;
}
.searchfunction-popup-container {
  top: 375px;
  width: 350px;
  position: relative;
  align-self: center;
  display: flex;
  justify-content: center;
  z-index: 101;
}
.searchfunction-popup-text {
  position: absolute;
  color: var(--white);
  top: 60px;
  left: 40px;
  width: 400px;
}
.searchfunction-popup-button {
  position: absolute;
  background-color: var(--white);
  margin-right: 150px;
  top: 250px;
  color: var(--black);
  font-weight: bold;
  border: 2px solid var(--red);
  border-radius: 5px;
}
.app-svg-popup {
  position: absolute;
  background-color: transparent;
  width: 320px;
  height: auto;
}
.app-popup-container {
  top: 180px;
  width: 300px;
  position: relative;
  align-self: center;
  display: flex;
  justify-content: center;
  z-index: 101;
}
.app-popup-text {
  position: absolute;
  color: var(--white);
  z-index: 10;
  top: 50px;
  left: 10px;
  width: 290px;
}
.app-popup-button {
  position: absolute;
  background-color: var(--white);
  margin-right: 150px;
  top: 250px;
  color: var(--black);
  font-weight: bold;
  border: 2px solid var(--red);
  margin-top: 20px;
  border-radius: 5px;
  margin-left: auto;
  margin-right: auto;
  padding: 10px 20px;
}

#app {
  position: relative;
}

@media screen and (min-width: 600px) {
  .searchfunction-popup-container {
    top: 390px;
  }
}
</style>
