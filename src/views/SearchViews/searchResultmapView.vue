<template>
  <BackgroundGradient />
  <div class="header-buttons">
    <RouterLink :to="{ name: 'searchresultlist' }"> <BackArrow /></RouterLink>
    <RouterLink :to="{ name: 'home' }"> <headerLogo /></RouterLink>
  </div>

  <div style="height: 600px; width: 100%" id="map"></div>
</template>

<script>
import L from 'leaflet' // npm install leaflet@latest notwendig
import 'leaflet/dist/leaflet.css'
import { storeData } from '@/stores/store.js'
import { toRaw, createApp } from 'vue'
import BackgroundGradient from '@/components/BackgroundGradient.vue'
import BackArrow from '@/components/BackArrow.vue'
import headerLogo from '@/components/headerLogo.vue'
import babyChangingTableIcon from '@/assets/icons/map-icons/Baby-Changing-Table.svg'
import toiletIcon from '@/assets/icons/map-icons/Toilet-Man-Woman-1--Streamline-Flex.svg'
import entranceIcon from '@/assets/icons/map-icons/Entrance.svg'
import rampIcon from '@/assets/icons/map-icons/Ramp-Up.svg'
import gastroIcon from '@/assets/icons/map-icons/Gastro.svg'
import liftIcon from '@/assets/icons/map-icons/Lift.svg'
import locationIcon from '@/assets/icons/map-icons/Location.svg'
import favoriteStarSvg from '@/components/favoriteStar.vue'
export default {
  components: { BackArrow, headerLogo, BackgroundGradient },
  data() {
    return {
      store: storeData(),
      zoom: 14,
      map: null,
      serachCircle: null,
      ownPosition: null,
      categoryIconURL: ''
    }
  },
  mounted() {
    ///// EINBINDEN DER LEAFLET Karte ////////////////////////////////////////////////////////////////////////
    this.$nextTick(() => {
      // Initailisieren der Karte
      this.map = L.map('map').setView(
        [this.store.temporaryData.ownXCoordinate, this.store.temporaryData.ownYCoordinate],
        this.zoom
      )
      if (this.map) {
        L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
          maxZoom: 19,
          attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
        }).addTo(this.map)
        L.control.scale({ imperial: false }).addTo(this.map)

        // Legende erstellen
        let legend = L.control({ position: 'bottomleft' })

        legend.onAdd = function () {
          let div = L.DomUtil.create('div', 'info legend')
          div.innerHTML = `
          <h3>Legende</h3>
          <div class="legend-container">
            <img src="${locationIcon}" style="width: 30px;" id="location">
            <label for="location" >&nbsp;Sie sind hier!</label>
            <img src="${gastroIcon}" style="width: 30px;" id="gastro">
            <label for="gastro"" >&nbsp;Gastronomie</label>
            <img src="${babyChangingTableIcon}" style="width: 30px;" id="baby-changing">
            <label for="baby-changing" >&nbsp;Wickelplatz</label>
            <img src="${entranceIcon}" style="width: 30px;" id="entrance">
            <label for="entrance" >&nbsp;Zugang&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</label>
            <img src="${liftIcon}" style="width: 30px;" id="lift">
            <label for="lift" >&nbsp;Fahrstuhl&nbsp;&nbsp;&nbsp;</label>
            <img src="${rampIcon}" style="width: 30px;" id="ramp">
            <label for="ramp" >&nbsp;Rampe&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</label>
            <img src="${toiletIcon}" style="width: 30px;" id="toilette">
            <label for="toilette" >&nbsp;Toilette</label>
          </div>
          `
          return div
        }

        legend.addTo(this.map)

        // Hinzufügen des Suchradius
        this.serachCircle = L.circle(
          [this.store.temporaryData.ownXCoordinate, this.store.temporaryData.ownYCoordinate],
          {
            radius: this.store.temporaryData.searchDistance, // in meter
            fillColor: 'blue',
            color: 'red',
            weight: 1,
            opacity: 1,
            fillOpacity: 0.15
          }
        )
        this.serachCircle.addTo(this.map)
        // eigener Standort
        this.ownPosition = L.marker(
          [this.store.temporaryData.ownXCoordinate, this.store.temporaryData.ownYCoordinate],
          {
            icon: L.icon({
              iconUrl: locationIcon,
              iconSize: [30, 30],
              iconAnchor: [10, 10],
              popupAnchor: [0, -4]
            })
          }
        )
          .addTo(toRaw(this.map))
          .bindPopup('Dein Standort!')
        // pois Anzeigen
        this.store.temporaryData.poiListforMap.forEach((element) => {
          if (element.poiName === ' Wickelplatz') {
            this.categoryIconURL = babyChangingTableIcon
          } else if (element.poiName === ' Toilette') {
            this.categoryIconURL = toiletIcon
          } else if (element.poiName === ' Zugang') {
            this.categoryIconURL = entranceIcon
          } else if (element.poiName === ' Rampe') {
            this.categoryIconURL = rampIcon
          } else if (element.poiName === ' Gastronomie') {
            this.categoryIconURL = gastroIcon
          } else if (element.poiName === ' Fahrstuhl') {
            this.categoryIconURL = liftIcon
          }

          L.marker([element.xCoordinates, element.yCoordinates], {
            icon: L.icon({
              iconUrl: this.categoryIconURL,
              iconSize: [30, 30],
              iconAnchor: [10, 10],
              popupAnchor: [0, -4]
            })
          })
            .addTo(toRaw(this.map)) // toRaw entfernt die proxiierung und löst einen konflikt von vue3 und map
            .bindPopup(
              `
            <div class="custom-popup">
              <h2>${element.poiName}</h2>
              <b>Details: </b> <span>${element.detailCategories}</span> <br>
              <b>Mindestbreite Tür: </b> <span>${element.minWidth} cm</span> <br>
              <b>Öffungszeiten: </b><span>${element.openingTimes}</span> <br>
              <b>Derzeit nutzbar? </b><span>${element.status}</span> <br>
              <br>
              <b>Kommentar: </b><span>${element.comment}</span> 
              </div>
              <br>
              <div id="favorite-star-${element.id}" class="favorite-star-container"></div>
              <button class="navigate-button" id="navigateButton-${element.id}">Zeig mir den Weg</button>
              `
            )
            .on('popupopen', () => {
              document
                .getElementById(`navigateButton-${element.id}`)
                .addEventListener('click', () => {
                  this.store.openExternMapToNavigate(element.xCoordinates, element.yCoordinates)
                })
              const favoriteStarContainer = document.getElementById(`favorite-star-${element.id}`)
              if (favoriteStarContainer) {
                const favoriteStarApp = createApp(favoriteStarSvg, { poiId: element.id })
                favoriteStarApp.mount(favoriteStarContainer)
              }
            })
          /*.bindTooltip(element.poiName, {
              permanent: true,
              direction: 'top',
              offset: L.point(5, -8)
            })
            .openTooltip()
             das dauerhafte Label wird aufgrund der Legende nict mehr benötigt, sodass die karte übersichtlicher wird */
        })
      }
    })
  }
}
</script>

<style>
/* scoped muss weggelassen werden, damit die styles auf leaflet angwendet werden können */
.legend {
  background-color: var(--white);
  height: 260px;
  border-radius: 20px;
}
.legend-container {
  display: flex;
  width: 120px;
  flex-wrap: wrap;
  margin-left: 5px;
}
h3 {
  text-align: center;
}
label {
  font-weight: bold;
  margin-top: 5px;
}
.header-buttons {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 20px;
  background-color: transparent;
}
.custom-popup {
  word-wrap: break-word;
  word-break: break-word;
  min-width: 140px;
  min-height: 200px;
  background-color: var(--white);
  border-radius: 8px;
  padding-bottom: 50px;
  padding-top: 5px;
  padding-left: 10px;
  padding-right: 10px;
  margin: -16px -24px -58px -20px;
  color: var(--black);
}
.navigate-button {
  background-color: var(--black);
  color: var(--red);
  border-radius: 10px;
  border: 1px solid var(--red);
  padding: 5px 10px;
}
.navigate-button:hover {
  color: var(--black);
  border: 1px solid var(--black);
  background-color: var(--red);
  cursor: pointer;
}
.favorite-star-container {
  margin-left: 250px;
  top: 10px;
  position: absolute;
}
</style>
