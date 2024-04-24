<template>
  <div class="header-buttons">
    <RouterLink :to="{ name: 'home' }"> <headerLogo /></RouterLink>
  </div>
  <HeadLine class="headline-account" :Headline="'Verwalte deine Account-Daten'" />

  <label class="label-username label-account" for="username">Benutzername</label>
  <input
    class="input-account"
    type="text"
    id="username"
    v-model="updatedUserData.username"
    :placeholder="updatedUserData.username ? updatedUserData.username : 'username'"
    :disabled="!editMode"
  />

  <label class="label-account" for="email">E-mail</label>
  <input
    class="input-account"
    type="email"
    id="email"
    v-model="updatedUserData.email"
    :placeholder="updatedUserData.email ? updatedUserData.email : 'Email'"
    :class="{ 'invalid-email': !emailValid }"
  />

  <label class="label-account" for="password">Passwort</label>
  <input
    class="input-account"
    type="password"
    id="password"
    v-model="updatedUserData.password"
    :placeholder="updatedUserData.password ? updatedUserData.password : 'Passwort'"
  />
  <label class="label-account" for="mobilityAssistance">Mobilitätshilfe</label>
  <input
    class="input-account"
    type="text"
    id="mobilityAssistance"
    v-model="updatedUserData.mobilityAssistance"
    :placeholder="
      updatedUserData.mobilityAssistance
        ? updatedUserData.mobilityAssistance
        : 'mobility assistance'
    "
  />
  <label class="label-account" for="mobilityAssistanceWidth">Mobilitätshilfe Breite (in cm)</label>
  <input
    class="input-account"
    type="text"
    id="mobilityAssistanceWidth"
    v-model="updatedUserData.mobilityAssistanceWidth"
    :placeholder="
      updatedUserData.mobilityAssistanceWidth
        ? updatedUserData.mobilityAssistanceWidth
        : 'mobilityAssistance width'
    "
  />
  <LöschenButton
    class="button-account"
    :Löschen="'Account-Löschen'"
    @click="confirmDelete"
  ></LöschenButton>
  <NavButton
    class="button-account"
    :Navigation="'Speichern'"
    @click="validateAndSaveUserData"
  ></NavButton>

  <div v-if="showConfirmation" class="confirmation-popup">
    <div class="confirmation-message">
      <p>Sind Sie sicher, dass Sie den Account dauerhaft löschen möchten?</p>
      <button @click="deleteUser">Ja</button>
      <button @click="cancelDelete">Nein</button>
    </div>
  </div>

  <div v-if="showEmailErrorPopup" class="email-popup">
    <p>Ungültige E-Mail-Adresse. Bitte geben Sie eine gültige E-Mail-Adresse ein.</p>
    <button @click="closeEmailErrorPopup">OK</button>
  </div>
</template>

<script>
import { storeData } from '@/stores/store.js'
import HeadLine from '@/components/HeadLine.vue'
import NavButton from '@/components/NavButton.vue'
import headerLogo from '@/components/headerLogo.vue'
import LöschenButton from '@/components/LöschenButton.vue'

export default {
  components: { NavButton, headerLogo, LöschenButton, HeadLine },
  data() {
    return {
      store: storeData(),
      updatedUserData: JSON.parse(localStorage.getItem('updatedUserData')) || {
        username: '',
        email: '',
        password: '',
        mobilityAssistance: '',
        mobilityAssistanceWidth: parseInt('')
      },
      userData: {
        id: '',
        userName: '',
        eMailAddress: '',
        passWord: '',
        mobilityAssistance: '',
        mobilityAssistanceWidth: ''
      },

      currentUserID: localStorage.getItem('currentUserID').replace(/"/g, ''),
      showConfirmation: false,
      emailValid: true,
      showEmailErrorPopup: false
    }
  },

  created() {
    this.fetchAndFilterUserData()
  },

  watch: {
    updatedUserData: {
      deep: true,
      handler(newValue) {
        this.updateUserDataInLocalStorage(newValue)
      }
    }
  },

  methods: {
    async fetchAndFilterUserData() {
      try {
        await this.store.getUserDataFromAPI()
        const currentUser = this.store.temporaryData.currentUserData.find((user) => {
          return user.id === this.currentUserID
        })

        if (!currentUser) {
          console.error('Benutzer nicht gefunden.')
          return
        }

        this.updatedUserData = {
          id: currentUser.id,
          username: currentUser.username || '',
          email: currentUser.email || '',
          password: currentUser.password || '',
          mobilityAssistance: currentUser.mobilityAssistance || '',
          mobilityAssistanceWidth: parseInt(currentUser.mobilityAssistanceWidth) || 0
        }
      } catch (error) {
        console.error('Fehler beim Abrufen und Filtern der Benutzerdaten:', error)
      }
    },

    isValidEmail(email) {
      const emailRegex = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/
      return emailRegex.test(email)
    },
    validateAndTrimEmail() {
      const email = this.updatedUserData.email.trim()
      if (!this.isValidEmail(email)) {
        this.showEmailErrorPopup = true
        this.emailValid = false
        return
      }
      this.emailValid = true
      this.userData.eMailAddress = email
    },

    closeEmailErrorPopup() {
      this.showEmailErrorPopup = false
    },
    trimOtherFields() {
      this.updatedUserData.password = this.updatedUserData.password.trim()
      this.updatedUserData.mobilityAssistance = this.updatedUserData.mobilityAssistance.trim()
    },
    updateUserDataInLocalStorage(userData) {
      try {
        localStorage.setItem('updatedUserData', JSON.stringify(userData))
      } catch (error) {
        console.error('Fehler beim Aktualisieren der Benutzerdaten im lokalen Speicher:', error)
      }
    },

    async saveUserDataAndSendToServer() {
      try {
        const updatedUserData = JSON.parse(localStorage.getItem('updatedUserData'))
        if (!updatedUserData) {
          console.error('Keine aktualisierten Benutzerdaten im lokalen Speicher gefunden.')
          return
        }
        this.store.temporaryData.changedUserData = {
          id: updatedUserData.id,
          username: updatedUserData.username || '',
          email: updatedUserData.email || '',
          mobilityAssistance: updatedUserData.mobilityAssistance || '',
          mobilityAssistanceWidth: parseInt(updatedUserData.mobilityAssistanceWidth) || 0,
          password: updatedUserData.password || ''
        }

        await this.store.updateUserAtAPI(this.store.temporaryData.changedUserData.id)
      } catch (error) {
        console.error(
          'Fehler beim Speichern der aktualisierten Benutzerdaten im temporären Speicher:',
          error
        )
      }
    },

    validateAndSaveUserData() {
      try {
        this.validateAndTrimEmail(this.updatedUserData)
        this.trimOtherFields(this.updatedUserData)
        const updatedUserData = JSON.parse(localStorage.getItem('updatedUserData'))

        if (!updatedUserData) {
          console.error('Keine aktualisierten Benutzerdaten im lokalen Speicher gefunden.')
          return
        }

        this.saveUserDataAndSendToServer(updatedUserData)
      } catch (error) {
        console.error('Fehler beim Speichern der Benutzerdaten:', error)
      }
    },

    confirmDelete() {
      this.showConfirmation = true
    },
    async deleteUser() {
      const userId = localStorage.getItem('currentUserID').replace(/"/g, '')

      try {
        await this.store.deleteUserfromAPI(userId)
        localStorage.removeItem('updatedUserData')
        localStorage.removeItem('currentUserID')
      } catch (error) {
        console.error('Fehler beim Löschen des Benutzers:', error)
      }
      this.showConfirmation = false
    }
  }
}
</script>

<style>
.confirmation-popup {
  position: fixed;
  width: 300px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: var(--white);
  padding: 20px;
  border: 4px solid var(--black);
  z-index: 9999;
}
.label-username {
  margin-top: 2rem;
}

.label-account {
  margin-left: 2rem;
}
.input-account {
  padding: 0.5rem;
  width: 80%;
  background-color: var(--white);
  border-radius: 0.5rem;
  margin: 1rem 2rem 2rem 2rem;
  color: var(--black);
  border: 1px solid var(--black);
  min-height: 3rem;
}
.input.invalid-email {
  border-color: var(--black);
}

.email-popup {
  color: var(--red);
  position: fixed;
  width: 300px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: var(--white);
  padding: 20px;
  border: 4px solid var(--black);
  z-index: 9999;
}
.button-account{
  align-self: center;
}
.headline-account {
  align-self: center;
}
.header-buttons {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 10px 0;
  background-color: transparent;
}
</style>
