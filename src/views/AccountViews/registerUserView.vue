<template>
  <BackgroundGradient />
  <img
    class="logo-img-register"
    src="/src/assets/images/final logo with background small (2).jpeg"
    alt="Logo der App"
  />
  <h1 class="heading-register">Willkommen bei Abled</h1>
  <InputField
    class="input-register"
    :value="username"
    @input="updateUsername($event.target.value)"
    @blur="checkUsernameDuplicate"
    placeholder="Benutzername eingeben"
    label
  >
    ></InputField
  >
  <InputField
    class="input-register"
    :value="email"
    @input="updateEmail($event.target.value)"
    @change="checkEmailValidity"
    placeholder="E-Mail-Adresse eingeben"
  >
    ></InputField
  >
  <InputField
    class="input-register"
    :value="password"
    @input="updatePassword($event.target.value)"
    @focus="showPasswordConditions = true"
    type="password"
    placeholder="Passwort eingeben"
  >
    ></InputField
  >
  <div v-show="showPasswordConditions" class="password-conditions">
    <ul class="password-conditions-list">
      <li v-for="condition in passwordConditions" :key="condition.condition">
        <span
          :class="{
            condition: condition.fulfilled,
            'condition-unfulfilled': !condition.fulfilled
          }"
          >{{ condition.condition }}</span
        >: <span v-if="condition.fulfilled">&#10004;</span
        ><span v-if="!condition.fulfilled" class="cross">&#10006;</span>
      </li>
    </ul>
  </div>

  <RouterLink class="login-link" :to="{ name: 'login' }">
    Du hast schon einen Account? <br />Hier geht's zum Login</RouterLink
  >
  <NavButton class="button-register" :Navigation="'Registrieren'" @click="registerUser"></NavButton>

  <div v-if="WelcomePopup" class="popup-mail">
    <div class="popup-content">
      <h2>Herzlich Willkommen!</h2>
      <hr />
      <p class="popup-text">
        Wir freuen uns, dass Sie unsere App ausprobieren wollen. <br />Erstellen Sie gerne einen
        Test-Account, um auf die App zugreifen zu können. <br /><br />

        Und bitte denken Sie daran, das die App sich noch in der Entwicklung befindet und
        dementsprechend noch nicht alle Features implementiert sind. <br />
        <br />Viel Spaß mit Abled!
      </p>
      <button class="popup-button" @click="closePopup">Verstanden</button>
    </div>
  </div>

  <div v-if="showPopup" class="popup-mail">
    <p>{{ popupMessage }}</p>
    <button class="popup-mail-button" @click="closePopup">{{ popupButtonLabel }}</button>
  </div>
</template>

<script>
import { storeData } from '@/stores/store.js'

import InputField from '@/components/InputField.vue'

import NavButton from '@/components/NavButton.vue'
import BackgroundGradient from '@/components/BackgroundGradient.vue'

export default {
  components: {
    InputField,
    NavButton,
    BackgroundGradient
  },
  data() {
    return {
      username: '',
      email: '',
      password: '',
      userCounter: 3,
      showPopup: false,
      WelcomePopup: true,
      usernameExists: false,
      popupMessage: '',
      popupButtonLabel: 'OK',
      passwordConditions: [
        { condition: 'Mindestens 8 Zeichen lang', fulfilled: false },
        { condition: 'Mindestens ein Sonderzeichen', fulfilled: false },
        { condition: 'Mindestens eine Zahl', fulfilled: false },
        { condition: 'Mindestens ein Großbuchstabe', fulfilled: false },
        { condition: 'Mindestens ein Kleinbuchstabe', fulfilled: false }
      ]
    }
  },
  methods: {
    async getUserData() {
      try {
        await storeData.getUserDataFromAPI()
        console.log(
          'Benutzerdaten erfolgreich vom Server abgerufen:',
          storeData.temporaryData.currentUserData
        )
      } catch (error) {
        console.error('Fehler beim Abrufen der Benutzerdaten:', error)
      }
    },
    updateUsername(value) {
      this.username = value
    },
    updateEmail(value) {
      this.email = value
    },
    updatePassword(value) {
      this.password = value
      this.updatePasswordConditions()
    },
    validateEmail(email) {
      const regex = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/
      return regex.test(email)
    },
    showPopupWithMessage(message) {
      this.popupMessage = message
      this.showPopup = true
    },

    checkInputValidity() {
      if (!this.validateEmail(this.email)) {
        this.showPopupWithMessage('Ungültige E-Mail-Adresse.')
        return false
      }

      if (!this.username.trim()) {
        this.showPopupWithMessage('Benutzername darf nicht leer sein.')
        return false
      }
    },
    showUsernameTakenPopup() {
      this.showPopup = true
      this.popupMessage = 'Der Benutzername ist bereits vergeben.'
    },

    showEmailInvalidPopup() {
      this.popupMessage =
        'Ungültige E-Mail-Adresse. Bitte geben Sie eine gültige E-Mail-Adresse ein.'
      this.showPopup = true
      this.popupButtonLabel = 'OK'
    },

    checkUsernameDuplicate() {
      const store = storeData() // Rufe das Store-Objekt auf
      store
        .getUserDataFromAPI() // Rufe die Methode vom Store-Objekt auf
        .then(() => {
          const userData = store.temporaryData.currentUserData // Erhalte die Benutzerdaten
          const usernameExists = userData.some((user) => user.username === this.username) // Prüfe auf vorhandenen Benutzernamen

          this.usernameExists = usernameExists

          if (this.usernameExists) {
            this.showUsernameTakenPopup()
          }
        })
        .catch((error) => {
          console.error('Fehler beim Überprüfen des Benutzernamens:', error)
        })
    },
    registerUser() {
      if (!this.username.trim() || !this.email.trim() || !this.password.trim()) {
        console.error('Bitte füllen Sie alle Felder aus.')
        return
      }
      if (!this.validateEmail(this.email)) {
        this.showPopup = true
        return
      }
      this.checkUsernameDuplicate()

      //const userID = this.generateUserID()
      const userData = {
        //id: userID,
        username: this.username,
        email: this.email,
        password: this.password,
        favoritePoisOfUser: []
      }

      localStorage.setItem('userData', JSON.stringify(userData))

      this.goToPrio()
    },
    updatePasswordConditions() {
      this.passwordConditions.forEach((condition, index) => {
        switch (index) {
          case 0:
            condition.fulfilled = this.password.length >= 8
            break
          case 1:
            condition.fulfilled = /[!@#$%^&*()_+=[\]{};':"\\|,.<>/?]/.test(this.password)
            break
          case 2:
            condition.fulfilled = /\d/.test(this.password)
            break
          case 3:
            condition.fulfilled = /[A-Z]/.test(this.password)
            break
          case 4:
            condition.fulfilled = /[a-z]/.test(this.password)
            break
          default:
            break
        }
      })
    },

    validatePassword(password) {
      if (password.length < 8) {
        return {
          valid: false
        }
      }

      const specialCharacters = /[!@#$%^&*()_+=[\]{};':"\\|,.<>/?]+/
      if (!specialCharacters.test(password)) {
        return {
          valid: false
        }
      }

      const numbers = /\d/
      if (!numbers.test(password)) {
        return {
          valid: false
        }
      }

      const uppercaseLetters = /[A-Z]/
      if (!uppercaseLetters.test(password)) {
        return {
          valid: false
        }
      }

      const lowercaseLetters = /[a-z]/
      if (!lowercaseLetters.test(password)) {
        return {
          valid: false
        }
      }

      return {
        valid: true
      }
    },
    goToPrio() {
      this.$router.push({ name: 'prio' })
    },
    closePopup() {
      this.showPopup = false
      this.WelcomePopup = false
    },
    checkEmailValidity() {
      if (!this.validateEmail(this.email)) {
        this.showEmailInvalidPopup()
        return
      }
    }
  }
}
</script>

<style>
.input-register {
  padding: 0.5rem;
  width: 90%;
  background-color: var(--white);
  border-radius: 0.5rem;
  margin: 2rem;
  color: var(--black);
  border: 1px solid var(--black);
  align-self: center;
  min-height: 3rem;
}

.logo-img-register {
  width: 50%;
  max-width: 400px;
  margin-top: 0.9rem;
  margin-bottom: 2rem;
  margin-right: 10px;
  border-radius: 40% 0px 0px 10px;
  align-self: center;
}
.heading-register {
  align-self: center;
  margin-top: 0;
  margin-bottom: 4rem;
  text-align: center;
  color: var(--red);
}
.login-link {
  width: 90%;
  align-self: center;
  padding: 0 0.5rem 0.5rem;
  margin-top: 1rem;
}

.button-register {
  align-self: center;
}

.popup-mail {
  display: flex;
  flex-direction: column;
  justify-content: center;
  position: fixed;
  width: 90%;
  max-width: 400px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: var(--white);
  padding: 20px;
  border: 4px solid var(--black);
  border-radius: 0.25rem;
  z-index: 9999;
  align-items: center;
}

.popup-content {
  background-color: var(--white);
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 0 10px var(--black);
}

.popup-mail-button {
  margin-top: 10px;
  justify-content: center;
  width: 3rem;
  color: var(--white);
  background-color: var(--black);
  border: 0.1rem solid var(--black);
  border-radius: 0.25rem;
  padding: 0.5rem;
  align-self: center;
}
.popup-mail-button {
  background-color: var(--white);
  border-radius: 1rem;
  padding: 1rem;
  width: 90%;
  align-self: center;
}
.password-conditions {
  background-color: var(--white);
  border-radius: 1rem;
  padding: 1rem;
  width: 90%;
  align-self: center;
}

.password-conditions-list {
  list-style: none;
}

.condition-unfulfilled {
  color: var(--red);
}

.condition {
  color: var(--black);
}
.cross {
  color: var(--red);
}

.popup-text {
  color: var(--black);
}
.popup-button {
  width: 100px;
  height: 30px;
  color: var(--white);
  background-color: var(--black);
  border-radius: 6px;
  border: 2px solid black;
}
.popup-button:hover {
  color: var(--red);
  border: 2px solid var(--red);
}
#app {
  height: 100%;
  display: flex;
  flex-direction: column;
}
@media screen and (max-width: 600px) {
  .popup-mail {
    width: 70%;
  }
}

@media screen and (max-width: 600px) {
  .heading-register {
    margin-top: 2.5rem;
  }
  .logo-img-register {
    margin-top: 2rem;
    margin-bottom: 2rem;
  }
}

@media screen and (min-width: 768px) {
  .logo-img-register {
    margin-top: 0.5rem;
    margin-bottom: 2.5rem;
    max-width: 280px;
  }
}
</style>
