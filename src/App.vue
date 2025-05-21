<script>
import CreatePlayer from '@/components/CreatePlayer.vue'
import AllPlayers from '@/components/AllPlayers.vue'

export default {
  name: 'App',
  components: {
    CreatePlayer,
    AllPlayers
  },

  data() {
    return {
      playerName: '',
      playerLives: null,
      playersList: [],
      tabId: 0,
    }
  },

  methods: {
    addPlayer() {
      this.playersList = [
        ...this.playersList,
        {
          name: this.playerName,
          lives: this.playerLives,
        }
      ];
      this.playerName = '';
      this.playerLives = null;
    },
  },
}
</script>

<template>
  <section class="players container">
    <div class="switch">
      <ul class="switch__options">
        <li
          class="switch__option"
          :class="{'switch__option_active': tabId === 0}"
        >
          <button @click="tabId = 0" class="switch__action">Добавление</button>
        </li>
        <li
          class="switch__option"
          :class="{'switch__option_active': tabId === 1}" 
        >
          <button @click="tabId = 1" class="switch__action">Все игроки</button>
        </li>
      </ul>
    </div>
    <CreatePlayer
      v-if="tabId === 0"
      v-model:player-name="playerName"
      v-model:player-lives="playerLives"
      :playersList="playersList"
      @add-player="addPlayer"
    />
    <AllPlayers
      v-if="tabId === 1"
      v-model:players-list="playersList"
    />
  </section>
</template>

<style>

/* normalize */
*, *::before, *::after {
  box-sizing: border-box;
}
body, h1, h2, h3, h4, h5, p {
  margin: 0;
}
input {
  border: none;
  padding: 0;
  background-color: transparent;
}
input:focus {
  outline: none;
}
button {
  border: none;
  padding: 0;
  background-color: transparent;
  cursor: pointer;
}
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
input:-webkit-autofill,
input:-webkit-autofill:hover, 
input:-webkit-autofill:focus, 
input:-webkit-autofill:active{
  -webkit-background-clip: text;
  -webkit-text-fill-color: #ffffff;
  transition: background-color 5000s ease-in-out 0s;
  box-shadow: inset 0 0 0 0 transparent;
}
/* normalize end */

/* common */
:root {
  --color-white: #ffffff;
  --color-grey-dark: #344453;
  --color-black: #000000;

  --color-pink: #ff9edf;

  --color-blue: #1aa3ff;

  --color-error: red;
  --color-success: green;

  --brrad-el: 20px;
  --brrad-block: 30px;
  --offset-active: 5px;
}

body {
  background-color: var(--color-grey-dark);
}

.container {
  margin: 0 auto;
  padding: 0 20px 0 20px;
  max-width: 1200px;
}
/* common end */

.players {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  padding-top: 50px;
  padding-bottom: 50px;
  color: var(--color-grey-dark);
}
.switch {
  display: flex;
  justify-content: center;
  margin: 0 0 20px 0;
}
.switch__options {
  display: flex;
  border-radius: var(--brrad-block);
  padding: 40px;
  background-color: var(--color-white);
}
.switch__option {}
.switch__option.switch__option_active {}
.switch__option:not(:last-of-type) {
  margin: 0 15px 0 0;
}
.switch__action {
  position: relative;
  padding: 20px;
  background-color: var(--color-pink);
  color: var(--color-white);
  font-weight: 600;
  font-size: 20px;
}
.switch__option:first-of-type .switch__action {
  border-radius: var(--brrad-el) 0 0 var(--brrad-el);
}
.switch__option:last-of-type .switch__action {
  border-radius: 0 var(--brrad-el) var(--brrad-el) 0;
}
.switch__action::before {
  position: absolute;
  left: calc(var(--offset-active) * -1);
  top: calc(var(--offset-active) * -1);
  width: calc(100% + (var(--offset-active) * 2));
  height: calc(100% + (var(--offset-active) * 2));
  border: 2px solid var(--color-pink);
  border-radius: calc(var(--brrad-el) + var(--offset-active));
}
.switch__action:hover::before {
  content: '';
}
.switch__option:first-of-type .switch__action::before {
  border-radius: calc(var(--brrad-el) + var(--offset-active)) 0 0 calc(var(--brrad-el) + var(--offset-active));
}
.switch__option:last-of-type .switch__action::before {
  border-radius: 0 calc(var(--brrad-el) + var(--offset-active)) calc(var(--brrad-el) + var(--offset-active)) 0;
}
.switch__option.switch__option_active .switch__action {
  background-color: var(--color-blue);
}
.switch__option.switch__option_active .switch__action::before {
  content: '';
  border-color: var(--color-blue);
}
</style>
