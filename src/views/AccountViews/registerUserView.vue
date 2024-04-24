<template>
  <img
    class="logo-img-register"
    src="/src/assets/images/final_logo_comprimized.png"
    alt="Logo der App"
  />
  <h1 class="heading-register">Willkommen bei Abled</h1>
  <InputField
    class="input-register"
    :value="username"
    @input="updateUsername($event.target.value)"
    placeholder="Benutzername eingeben"
    label
  >
    ></InputField
  >
  <InputField
    class="input-register"
    :value="email"
    @input="updateEmail($event.target.value)"
    placeholder="E-Mail-Adresse eingeben"
  >
    ></InputField
  >
  <InputField
    class="input-register"
    :value="password"
    @input="updatePassword($event.target.value)"
    type="password"
    placeholder="Passwort eingeben"
  >
    ></InputField
  >
  <NavButton class="button-register" :Navigation="'Registrieren'" @click="registerUser"></NavButton>
</template>

<script>
import InputField from '@/components/InputField.vue'

import NavButton from '@/components/NavButton.vue'

export default {
  components: {
    InputField,
    NavButton
  },
  data() {
    return {
      username: '',
      email: '',
      password: '',
      userCounter: 3
    }
  },
  methods: {
    updateUsername(value) {
      this.username = value
    },
    updateEmail(value) {
      this.email = value
    },
    updatePassword(value) {
      this.password = value
    },
    validateEmail(email) {
      const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
      return regex.test(email)
    },
    /*generateUserID() {
      this.userCounter++
      return 100 + this.userCounter
    },*/

    registerUser() {
      if (!this.username.trim() || !this.email.trim() || !this.password.trim()) {
        console.error('Bitte füllen Sie alle Felder aus.')
        return
      }
      if (!this.validateEmail(this.email)) {
        alert('Ungültige E-Mail-Adresse.')
        return
      }

      //const userID = this.generateUserID()
      const userData = {
        //id: userID,
        username: this.username,
        email: this.email,
        password: this.password
      }

      localStorage.setItem('userData', JSON.stringify(userData))
      //localStorage.setItem('userId', userID)
      this.goToPrio()
    },
    goToPrio() {
      this.$router.push({ name: 'prio' })
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
.button-register {
  align-self: center;
}

.logo-img-register {
  width: 100%;
  margin-top: 2rem;
}
.heading-register {
  align-self: center;
  margin-top: 3rem;
}

#app {
  height: 100%;
  display: flex;
  flex-direction: column;
}
</style>
