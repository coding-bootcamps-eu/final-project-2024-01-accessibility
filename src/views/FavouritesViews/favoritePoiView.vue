<template>
  <BackgroundGradient />
  <div class="header-buttons">
    <RouterLink :to="{ name: 'home' }"> <BackArrow /></RouterLink>
    <RouterLink :to="{ name: 'home' }"> <headerLogo /></RouterLink>
    <RouterLink :to="{ name: 'favoritemap' }"><EarthMap /></RouterLink>
  </div>
  <HeadLine
    Headline="Hier findest du alle deine favorisierten Orte"
    class="headline-text"
  ></HeadLine>

  <div
    class="favorite-poi-container"
    v-for="favoritePoi of this.store.temporaryData.dataFromFavoritePois"
    :key="favoritePoi"
  >
    <button class="favoritelist-button">
      <router-link :to="'/infopoi/' + favoritePoi.id">
        <div class="favoritelist-poiname">{{ favoritePoi.poiName }}</div>
        <img
          class="categorie-icon"
          :src="this.store.getIconOfCategory(favoritePoi)"
          alt="Kategorie Icon"
        />
        <div class="detailcategories-container">
          <div
            class="favoritelist-detailcategories"
            v-for="detailcategorie of favoritePoi.detailCategories"
            :key="'detail-' + favoritePoi.id + '-' + detailcategorie"
          >
            ✅ {{ detailcategorie }}
          </div>
        </div>
        <div class="address-container">
          <img
            class="location-list-icon"
            src="@/assets/icons/location-indicator.png"
            alt="location-logo"
          />
          &nbsp; {{ favoritePoi.city }}, {{ favoritePoi.street }} {{ favoritePoi.number }}
        </div>
      </router-link>
    </button>
  </div>
</template>

<script>
import EarthMap from '@/components/EarthMap.vue'
import BackArrow from '@/components/BackArrow.vue'
import headerLogo from '@/components/headerLogo.vue'
import { storeData } from '@/stores/store.js'
import BackgroundGradient from '@/components/BackgroundGradient.vue'
import HeadLine from '@/components/HeadLine.vue'

export default {
  components: {
    BackArrow,
    headerLogo,
    EarthMap,
    BackgroundGradient,
    HeadLine
  },
  data() {
    return {
      store: storeData()
    }
  },
  async created() {
    await this.store.getPoiDataFromAPI()
    await this.store.getUserDataFromAPI()
    this.store.getDataFromCurrentUser()
    this.store.getDataFromFavoritePois()
  }
}
</script>
<style scoped>
.favorite-poi-container {
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
.favoritelist-button {
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
.favoritelist-button:hover {
  background-color: rgb(252, 220, 180);
  cursor: pointer;
}
.favoritelist-detailcategories {
  text-align: start;
  font-size: 14px;
  width: 70%;
}
.detailcategories-container {
  display: flex;
  flex-direction: column;
  min-height: 50px;
  min-width: 370px;
  justify-content: flex-start;
  padding-bottom: 5px;
}

.favoritelist-poiname {
  font-size: 20px;
  font-weight: bold;
  color: var(--red);
  align-self: flex-start;
}
.headline-text {
  margin-bottom: 30px;
}
.address-container {
  font-weight: 200;
  align-self: start;
  margin-top: 4px;
  margin-left: 4px;
  text-align: left;
}
.location-list-icon {
  width: 10px;
}
@media screen and (min-width: 600px) {
  .favoritelist-button {
    width: 70%;
  }
}
@media screen and (min-width: 768px) {
  .favoritelist-button {
    width: 50%;
  }
}
</style>
