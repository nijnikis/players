<script>
import { filterNumber } from '@/utils/common.js'

export default {
  props: {
    playerName: String,
    playerLives: Number || null,
    playersList: [],
  },

  emits: [
    'update:playerName',
    'update:playerLives',
  ],
  
  data () {
    return {
      playerNameError: '',
      playerLivesError: '',
      playerLivesRaw: { value: null },
      isFormValid: true,
    };
  },
  
  methods: {
    cleanFormErrors() {
      this.playerNameError = '';
      this.playerLivesError = '';
    },
    validateName() {
      const playerWithSameName = this.playersList.find(player => player.name === this.playerName);

      if(playerWithSameName) {
        this.playerNameError = 'Это имя уже занято';
        this.isFormValid = false;
      }
    },
    validateLives() {
      if(typeof this.playerLivesRaw.value === 'number' && this.playerLivesRaw.value <= 0) {
        this.playerLivesError = 'Количество жизней должно быть больше 0';
        this.isFormValid = false;
      }
    },
    validateFormForSubmit() {
      this.cleanFormErrors();
      this.isFormValid = true;
      
      if(!this.playerName) {
        this.playerNameError = 'Укажите имя игрока';
        this.isFormValid = false;
      }

      this.validateName();

      this.validateLives();

      if(this.playerLivesRaw.value === null) {
        this.playerLivesError = 'Укажите количество жизней';
        this.isFormValid = false;
      }
    },

    addPlayer() {
      this.$emit('add-player');
    },

    handleInputName(event) {
      const inputValue = event.target.value;
      this.$emit('update:playerName', inputValue);
    },
    handleInputLives(event) {
      const inputValue = event.target.value;
      
      if (inputValue === '') {
        this.playerLivesRaw = {value: null};
      } else if (inputValue === '0') {
        this.playerLivesRaw = {value: 0};
      } else {
        const inputValueNumber = filterNumber(inputValue);
        const playerLivesToSet = (!inputValueNumber && (inputValueNumber !== 0)) ? null : inputValueNumber;
        this.playerLivesRaw = {value: playerLivesToSet};
      }
    },
    handleSubmitAddPlayer(event) {
      event.preventDefault();
      this.validateFormForSubmit();
      if (this.isFormValid) {
        this.addPlayer();
      }
    },
  },

  mounted() {
    this.playerLivesRaw.value = this.playerLives;
  },
  watch: {
    playerName() {
      this.playerNameError = '';
      this.validateName();
    },
    playerLives(lives) {
      this.playerLivesError = '';
      this.playerLivesRaw.value = lives;
      this.validateLives();
    },
    playerLivesRaw (livesRaw) {
      this.$emit('update:playerLives', livesRaw.value);
    },
  },
}
</script>

<template>
  <div class="add-player">
    <form @submit="handleSubmitAddPlayer" class="add-player__form">
      <label class="add-player__label">
        <p class="add-player__label-text">Имя игрока</p>
        <input
          :value="playerName"
          @input="handleInputName"
          id="name"
          class="add-player__input add-player__input_name"
          type="text"
          placeholder="Введите имя игрока"
        />
        <p
          v-if="playerNameError"
          class="add-player__label-error"
        >{{ playerNameError }}</p>
      </label>
      <label class="add-player__label">
        <p class="add-player__label-text">Количество жизней</p>
        <input
          :value="playerLivesRaw.value"
          @input="handleInputLives"
          id="lives"
          class="add-player__input add-player__input_lives"
          type="text"
          placeholder="Введите количество жизней"
        />
        <p
          v-if="playerLivesError"
          class="add-player__label-error"
        >{{ playerLivesError }}</p>
      </label>
      <button class="add-player__submit" type="submit">Добавить</button>
    </form>
  </div>
</template>

<style scoped>
.add-player {}
.add-player__form {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 0 auto;
  border-radius: var(--brrad-block);
  padding: 50px 50px 50px 50px;
  max-width: 600px;
  background-color: var(--color-white);
}
.add-player__head {
  padding: 20px 0 20px 0;
  font-size: 20px;
  text-align: center;
  color: var(--color-pink);
}
.add-player__label {
  position: relative;
  margin: 0 0 20px 0;
  padding: 10px 10px 20px 10px;
  max-width: 400px;
  width: 100%;
  background-color: var(--color-pink);
  border-radius: var(--brrad-el);
}
.add-player__label::before {
  position: absolute;
  left: calc(var(--offset-active) * -1);
  top: calc(var(--offset-active) * -1);
  width: calc(100% + (var(--offset-active) * 2));
  height: calc(100% + (var(--offset-active) * 2));
  border: 2px solid var(--color-pink);
  border-radius: calc(var(--brrad-el) + var(--offset-active));
}
.add-player__label:focus-within::before,
.add-player__label:hover::before {
  content: '';
}
.add-player__label:nth-child(2n),
.add-player__label:nth-child(2n)::before {
  border-top-right-radius: 0;
  border-bottom-left-radius: 0;
}
.add-player__label:nth-child(2n - 1),
.add-player__label:nth-child(2n - 1)::before {
  border-top-left-radius: 0;
  border-bottom-right-radius: 0;
}
.add-player__label-text {
  position: relative;
  font-size: 12px;
  color: var(--color-white);
}
.add-player__input {
  position: relative;
  padding: 10px 10px 10px 10px;
  width: 100%;
  font-size: 16px;
  color: var(--color-white);
}
.add-player__input::placeholder {
  color: var(--color-white);
}
.add-player__input.add-player__input_name {}
.add-player__input.add-player__input_lives {}
.add-player__label-error {
  position: absolute;
  left: 10px;
  bottom: 10px;
  font-size: 12px;
  color: var(--color-error);
}
.add-player__submit {
  position: relative;
  border-radius: var(--brrad-el);
  padding: 20px 40px 20px 40px;
  background-color: var(--color-blue);
  font-size: 24px;
  font-weight: 600;
  color: var(--color-white);
}
.add-player__submit::before {
  position: absolute;
  left: calc(var(--offset-active) * -1);
  top: calc(var(--offset-active) * -1);
  width: calc(100% + (var(--offset-active) * 2));
  height: calc(100% + (var(--offset-active) * 2));
  border: 2px solid var(--color-blue);
  border-radius: calc(var(--brrad-el) + var(--offset-active));
}
.add-player__submit:hover::before {
  content: '';
}
</style>
