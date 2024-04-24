<template>
  <HeadLine class="headline-account" :Headline="'Wähle deine Mobilitätshilfe'" />

  <PriorityButton
    v-for="mobilityAssistanceClass in store.localData.mobilityAssistanceClasses"
    :key="mobilityAssistanceClass"
    :priority="mobilityAssistanceClass"
    @click="selectMobilityAssistance(mobilityAssistanceClass)"
  />
  <label class="label-prio" for="mobilityAssistanceWidth">Mobilitätshilfe Breite</label>
  <input
    class="input-prio"
    type="number"
    id="mobilityAssistanceWidth"
    v-model="mobilityAssistanceWidth"
    :placeholder="'Breite in cm'"
  />

  <NavButton class="button-prio" Navigation="Weiter" @click="saveDataAndOpenPopup" />

  <div v-if="showPopup" class="popup">
    <div class="popup-content">
      <p>Dein Account wird angelegt.</p>
      <button @click="addUserAndNavigate">OK</button>
      <RouterLink :to="{ name: 'home' }"></RouterLink>
    </div>
  </div>
</template>

<script>
import { storeData } from '@/stores/store.js'
import HeadLine from '@/components/HeadLine.vue'
import PriorityButton from '@/components/PriorityButton.vue'
import NavButton from '@/components/NavButton.vue'

export default {
  components: { PriorityButton, NavButton, HeadLine },
  data() {
    return {
      store: storeData(),
      selectedMobilityAssistance: '',
      mobilityAssistanceWidth: '',
      showPopup: false,
      addingUser: false,
      loading: false
    }
  },

  methods: {
    selectMobilityAssistance(mobilityAssistanceClass) {
      this.selectedMobilityAssistance = mobilityAssistanceClass
    },
    saveDataAndOpenPopup() {
      this.saveDataToLocalStorage()
      this.loadUserDataToTemporaryData()
      this.showPopup = true
    },
    saveDataToLocalStorage() {
      const localUserData = JSON.parse(localStorage.getItem('userData')) || {}

      localUserData.mobilityAssistance = this.selectedMobilityAssistance
      localUserData.mobilityAssistanceWidth = this.mobilityAssistanceWidth

      localStorage.setItem('userData', JSON.stringify(localUserData))
    },
    loadUserDataToTemporaryData() {
      try {
        const localUserData = JSON.parse(localStorage.getItem('userData')) || {}

        this.store.temporaryData.newUserData = {
          id: '',
          username: localUserData.username || '',
          email: localUserData.email || '',
          mobilityAssistance: localUserData.mobilityAssistance || '',
          mobilityAssistanceWidth: localUserData.mobilityAssistanceWidth || '',
          password: localUserData.password || ''
        }

        console.log(
          'Benutzerdaten erfolgreich in temporäre Daten geladen:',
          this.store.temporaryData.newUserData
        )
      } catch (error) {
        console.error('Fehler beim Laden der Benutzerdaten in temporäre Daten:', error)
      }
    },

    async addUserAndNavigate() {
      try {
        const localUserData = JSON.parse(localStorage.getItem('userData')) || {}

        this.store.temporaryData.newUserData = localUserData
        await this.store.addNewUserToAPI()
        await this.store.getUserDataFromAPI()

        console.log('Daten von der API erhalten:', this.store.temporaryData.currentUserData)

        localStorage.setItem(
          'currentUserID',
          JSON.stringify(
            this.store.temporaryData.currentUserData[
              this.store.temporaryData.currentUserData.length - 1
            ].id
          )
        )
        this.$router.push({ name: 'home' })
      } catch (error) {
        console.error('Fehler beim Hinzufügen des Benutzers:', error)
      }
    }
  }
}
</script>

<style>
.input-prio {
  padding: 0.5rem;
  width: 90%;
  background-color: var(--white);
  border-radius: 0.5rem;
  margin: 1rem 1rem 2rem 1rem;
  color: var(--black);
  border: 1px solid var(--black);
  min-height: 3rem;
  width: 14.5rem;
  align-self: center;
}
.label-prio {
  margin-left: 3rem;
  margin-top: 1.5rem;
}
.button-prio {
  align-self: center;
}
.popup {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: var(--black);
  display: flex;
  justify-content: center;
  align-items: center;
}

.popup-content {
  background-color: var(--white);
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 10px var(--black);
}
</style>
