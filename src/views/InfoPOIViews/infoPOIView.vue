<template>
  <div class="background-info"></div>

  <div class="header-buttons">
    <button @click="goBackToTheLastPage" class="back-arrow"><BackArrow /></button>
    <RouterLink :to="{ name: 'home' }"> <headerLogo /></RouterLink>
  </div>
  <br />
  <div v-if="findChoosenPoi" class="info-infopoi">
    <img
      class="categorie-icon"
      :src="this.store.getIconOfCategory(findChoosenPoi)"
      alt="Kategorie Icon"
    />
    <favoriteStarSvg :poiId="poiId" class="favorite-star-infopoi"></favoriteStarSvg>
    <h2>
      {{ findChoosenPoi.poiName ? findChoosenPoi.poiName : 'Poi konnte nicht geladen werden' }}
    </h2>

    <p>
      <b>Adresse:</b> <br />
      {{ findChoosenPoi.city }}, <br />
      {{ findChoosenPoi.street }}, {{ findChoosenPoi.number }} <br />{{ findChoosenPoi.zip }}
    </p>
    <div>
      <div
        class="detail-categorys"
        v-for="detailcategorie of findChoosenPoi.detailCategories"
        :key="'detail-' + findChoosenPoi.id + '-' + detailcategorie"
      >
        ✅ {{ detailcategorie }}
      </div>
    </div>

    <p>
      <b>passierbare Mindestbreite:</b>
      {{ findChoosenPoi.minWidth ? findChoosenPoi.minWidth : 'Poi konnte nicht geladen werden' }} cm
    </p>
    <p>
      <b>Entfernung Luftlinie:</b>
      {{
        this.store.calcDistance(
          findChoosenPoi.xCoordinates,
          findChoosenPoi.yCoordinates,
          this.store.temporaryData.ownXCoordinate,
          this.store.temporaryData.ownYCoordinate
        )
      }}
      Meter
    </p>
    <p>
      <b>Öffnungszeiten:</b>
      {{
        findChoosenPoi.openingTimes
          ? findChoosenPoi.openingTimes
          : 'Poi konnte nicht geladen werden'
      }}
    </p>
    <p>
      <b>Kommentar:</b>
      {{ findChoosenPoi.comment ? findChoosenPoi.comment : 'Poi konnte nicht geladen werden' }}
    </p>
    <p>
      <b>Derzeit nutzbar?</b>
      ✅
    </p>
  </div>
  <p class="ctn-btn">
    <LöschenButton class="button-infopoi" :Löschen="'Nicht nutzbar'" @click="removePoi" />
    <NavButton
      class="button-infopoi"
      :Navigation="'In Maps öffnen'"
      @click="
        store.openExternMapToNavigate(findChoosenPoi.xCoordinates, findChoosenPoi.yCoordinates)
      "
    />
  </p>
</template>

<script>
import { storeData } from '@/stores/store.js'
import BackArrow from '@/components/BackArrow.vue'
import NavButton from '@/components/NavButton.vue'
import LöschenButton from '@/components/LöschenButton.vue'
import headerLogo from '@/components/headerLogo.vue'
import favoriteStarSvg from '@/components/favoriteStar.vue'

export default {
  components: {
    headerLogo,
    BackArrow,
    NavButton,
    LöschenButton,
    favoriteStarSvg
  },
  data() {
    return {
      poiId: this.$route.params.id,
      store: storeData()
    }
  },
  methods: {
    removePoi() {
      this.$router.push({ name: 'searchresultlist' })
    },
    goBackToTheLastPage() {
      this.$router.go(-1)
    }
  },
  computed: {
    findChoosenPoi() {
      return this.store.temporaryData.currentPois.find((el) => el.id == this.poiId)
    }
  },
  mounted() {
    this.store.getPoiDataFromAPI()
  }
}
</script>

<style scoped>
.background-info {
  position: absolute;
  z-index: -1;
  top: 0;
  left: 0;
  width: 100%;
  height: 200px;
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
    linear-gradient(180deg, var(--black) 167px, var(--black) 199.6px, transparent 200px);
  border-radius: 20px 20px 0 0;
  border-radius: 20px 20px 0 0;
}

.info {
  padding-left: 0.5rem;
  background-color: var(--white);
  color: var(--black);
  border-radius: 1rem;
  width: 75%;
  margin-top: 1rem;
  margin-bottom: 0px;
  margin-left: 5px;

  display: grid;
  font-size: 16px;
}
b {
  font-size: 16px;
  padding: 0.9rem;
  padding-left: 0;
}
section {
  min-width: 320px;
}
.detail-categorys {
  margin-top: 5px;
  margin-left: 10px;
}
h2 {
  color: var(--red);
  margin: 0 0 0 10px;
  font-size: 28px;
}
.header-buttons {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 20px;
  background-color: transparent;
}
.ctn-btn {
  display: flex;
  flex-direction: column;
}
.button-infopoi {
  align-self: center;
}
.info-infopoi {
  padding-left: 0.5rem;
  padding-top: 0.5rem;
  background-color: var(--white);
  color: var(--black);
  border-radius: 1rem;
  width: 95%;
  margin-top: 3.5rem;
  margin-bottom: 1rem;
  align-self: center;
  position: relative;
}
.categorie-icon {
  position: absolute;
  top: 10px;
  right: 12px;
  width: 60px;
  border-radius: 10px;
}
.favorite-star-infopoi {
  position: absolute;
  top: 70px;
  right: 15px;
}
.back-arrow {
  background-color: transparent;
  border: 2px solid transparent;
  border-radius: 10px;
  margin-top: 0px;
}
</style>
