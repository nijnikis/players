<script>
import { filterNumber } from '@/utils/common.js'

export default {
  props: {
    playersList: [],
  },

  emits: ['update:playersList'],
  
  data () {
    return {
      livesTmp: { name: '', lives: null },
      playersListRaw: [],
      rating: [],
    };
  },
  
  methods: {
    updatePlayersList() {
      console.log('updatePlayersList this.playersListRaw', this.playersListRaw);
      this.$emit('update:playersList', [...this.playersListRaw]);
    },
    cleanAllErrors() {
      this.playersListRaw = [...this.playersListRaw.map(playerItem => (
        {
          ...playerItem,
          nameError: null,
          livesError: null,
        }
      ))];
    },

    handleBlurName(event, player) {
      const nameNew = event.target.value;
      const playerName = player.name;

      if (playerName === nameNew) {
        this.cleanAllErrors();
        return;
      }

      if (nameNew === '') {
        this.playersListRaw = [...this.playersListRaw.map(playerItem => (
          playerItem.name === playerName
            ? {
              ...playerItem,
              nameError: 'Укажите имя игрока',
            } : playerItem
        ))];
        return;
      }

      const playerWithSameName = this.playersList.find(playerItem => playerItem.name === nameNew);
      if (playerWithSameName) {
        this.playersListRaw = [...this.playersListRaw.map(playerItem => (
          playerItem.name === playerName
            ? {
              ...playerItem,
              nameError: 'Имя уже занято',
            } : playerItem
        ))];
      } else {
        const playersListNew = [...this.playersListRaw.map(playerItem => (
          playerItem.name === playerName
            ? {
              ...playerItem,
              name: nameNew,
              nameError: null,
            } : {
              ...playerItem,
              nameError: null,
            }
        ))];
        this.$emit('update:playersList', playersListNew);
      }
    },
    handleInputLives(event, player) {
      const livesNew = filterNumber(event.target.value);
      const livesToSet = (!livesNew && (livesNew !== 0)) ? null : livesNew;
      const playerName = player.name;
      this.livesTmp = { name: playerName, lives: livesToSet };
    },
    handleBlurLives(event, player) {
      const livesNew = filterNumber(event.target.value);
      const playerName = player.name;
      this.livesTmp = { name: '', lives: null };

      if (player.lives === livesNew) {
        this.cleanAllErrors();
        return;
      }

      if (livesNew === 0) {
        this.playersListRaw = [...this.playersListRaw.map(playerItem => (
          playerItem.name === playerName
            ? {
              ...playerItem,
              livesError: 'Количество жизней должно быть больше 0',
            } : playerItem
        ))];
        return;
      }

      if (!livesNew) {
        this.playersListRaw = [...this.playersListRaw.map(playerItem => (
          playerItem.name === playerName
            ? {
              ...playerItem,
              livesError: 'Укажите количество жизней',
            } : playerItem
        ))];
        return;
      }

      const playersListNew = [...this.playersListRaw.map(playerItem => (
        playerItem.name === playerName
          ? {
            ...playerItem,
            lives: livesNew,
            livesError: null,
          } : {
            ...playerItem,
            livesError: null,
          }
      ))];
      this.$emit('update:playersList', playersListNew);
    },

    handleClickDecr (player) {
      this.cleanAllErrors();
      const playerName = player.name;
      const livesNew = player.lives - 1;
      if (livesNew) {
        const playersListNew = [...this.playersListRaw.map(playerItem => (
          playerItem.name === playerName
            ? {
              ...playerItem,
              lives: livesNew,
            } : {
              ...playerItem,
            }
        ))];
        this.$emit('update:playersList', playersListNew);
      } else {
        this.playersListRaw = [...this.playersListRaw.map(playerItem => (
          playerItem.name === playerName
            ? {
              ...playerItem,
              livesError: 'Количество жизней должно быть больше 0',
            } : playerItem
        ))];
      }
    },
    
    handleClickIncr (player) {
      this.cleanAllErrors();
      const playerName = player.name;
      const livesNew = player.lives + 1;
      const playersListNew = [...this.playersListRaw.map(playerItem => (
        playerItem.name === playerName
          ? {
            ...playerItem,
            lives: livesNew,
          } : {
            ...playerItem,
          }
      ))];
      this.$emit('update:playersList', playersListNew);
    },
  },

  mounted() {
    this.playersListRaw = [...this.playersList];
    const ratingNew = [...this.playersList];
    ratingNew.sort((a, b) => b.lives - a.lives);
    this.rating = [...ratingNew];
  },
  watch: {
    playersList(value) {
      this.playersListRaw = [...value];
      const ratingNew = [...this.playersList];
      ratingNew.sort((a, b) => b.lives - a.lives);
      this.rating = [...ratingNew];
    },
  },
}
</script>

<template>
  <div class="all">
    <div class="all__section">
      <h2 class="all__head">Редактирование</h2>
      <ul v-if="playersListRaw?.length > 0" class="edit">
        <li
          v-for="player in playersListRaw"
          :key="player.name"
          class="edit__item"
        >
          <div class="edit__container edit__container_name">
            <label class="edit__label">
              <p class="edit__label-text">Имя игрока</p>
              <input
                :value="player.name"
                @blur="(event) => handleBlurName(event, player)"
                id="name"
                class="edit__input edit__input_name"
                type="text"
                placeholder="Введите имя игрока"
              />
              <p
                v-if="player.nameError"
                class="edit__label-error"
              >{{ player.nameError }}</p>
            </label>
          </div>
          <div class="edit__container edit__container_lives">
            <label class="edit__label">
              <p class="edit__label-text">Количество жизней</p>
              <input
                :value="livesTmp.name === player.name ? livesTmp.lives : player.lives"
                @input="(event) => handleInputLives(event, player)"
                @blur="(event) => handleBlurLives(event, player)"
                id="name"
                class="edit__input edit__input_name"
                type="text"
                placeholder="Введите количество жизней"
              />
              <p
                v-if="player.livesError"
                class="edit__label-error"
              >{{ player.livesError }}</p>
            </label>
            <button
              @click.prevent="handleClickDecr(player)"
              class="edit__counter edit__counter_decr"
            >-</button>
            <button
              @click.prevent="handleClickIncr(player)"
              class="edit__counter edit__counter_incr"
            >+</button>
          </div>
        </li>
      </ul>
      <p v-else class="all__placeholder">Список игроков пуст</p>
    </div>
    <div class="all__section">
      <h2 class="all__head">Рейтинг</h2>
      <ul v-if="playersList?.length > 0" class="rating">
        <li
          v-for="(player, index) in rating"
          :key="player.name"
          class="rating__item"
        >
          <span class="rating__item-text rating__item-text_position">{{ index + 1 }}</span>
          <span class="rating__item-text">У игрока</span>
          <span class="rating__item-text rating__item-text_name">{{ player.name }}</span>
          <span class="rating__item-text">{{ `${player.lives} жизней` }}</span>
        </li>
      </ul>
      <p v-else class="all__placeholder">Список игроков пуст</p>
    </div>
  </div>
</template>

<style scoped>

.all {
  display: flex;
  flex-direction: column;
  margin: 0 auto;
  border-radius: var(--brrad-block);
  padding: 20px 50px 20px 50px;
  max-width: 800px;
  background-color: var(--color-white);
}
.all__section {
  padding: 20px 0 20px 0;
}
.all__head {
  padding: 10px 0 10px 0;
  font-size: 20px;
  color: var(--color-grey-dark);
}

.edit {
  padding: 10px 0 10px 0;
}
.edit__item {
  display: flex;
  justify-content: space-between;
  padding: 10px 0 10px 0;
}
.edit__container {
  position: relative;
  display: flex;
  align-items: center;
  width: 40%;
}
.edit__container.edit__container_name {}
.edit__container.edit__container_lives {}
.edit__label {
  position: relative;
  padding: 10px 10px 20px 10px;
  background-color: var(--color-pink);
  border-radius: var(--brrad-el);
  width: 100%;
}
.edit__container.edit__container_lives .edit__label {
  padding: 10px 10px 20px 20px;
}
.edit__label::before {
  position: absolute;
  left: calc(var(--offset-active) * -1);
  top: calc(var(--offset-active) * -1);
  width: calc(100% + (var(--offset-active) * 2));
  height: calc(100% + (var(--offset-active) * 2));
  border: 2px solid var(--color-pink);
  border-radius: calc(var(--brrad-el) + var(--offset-active));
}
.edit__label:focus-within::before,
.edit__label:hover::before {
  content: '';
}
.edit__container:nth-child(2n) .edit__label,
.edit__container:nth-child(2n) .edit__label::before {
  border-top-right-radius: 0;
  border-bottom-left-radius: 0;
}
.edit__container:nth-child(2n - 1) .edit__label,
.edit__container:nth-child(2n - 1) .edit__label::before {
  border-top-left-radius: 0;
  border-bottom-right-radius: 0;
}
.edit__label-text {
  position: relative;
  font-size: 12px;
  color: var(--color-white);
}
.edit__input {
  position: relative;
  padding: 10px 10px 10px 10px;
  width: 100%;
  font-size: 16px;
  color: var(--color-white);
}
.edit__input::placeholder {
  color: var(--color-white);
}
.edit__input.edit__input_name {}
.edit__input.edit__input_lives {}
.edit__label-error {
  position: absolute;
  left: 10px;
  bottom: 10px;
  font-size: 12px;
  color: var(--color-error);
}
.edit__container.edit__container_lives .edit__label-error {
  left: 20px;
}
.edit__counter {
  position: absolute;
  display: flex;
  justify-content: center;
  align-items: center;
  top: 50%;
  border-radius: 99px;
  padding: 0 0 2px 0;
  width: 40px;
  height: 40px;
  background-color: var(--color-blue);
  font-size: 24px;
  font-weight: 600;
  color: var(--color-white);
  transform: translate(-50%, -50%);
}
.edit__counter.edit__counter_decr {
  left: 0;
}
.edit__counter.edit__counter_incr {
  left: 100%;
}
.edit__counter::before {
  position: absolute;
  left: calc(var(--offset-active) * -1);
  top: calc(var(--offset-active) * -1);
  width: calc(100% + (var(--offset-active) * 2));
  height: calc(100% + (var(--offset-active) * 2));
  border: 2px solid var(--color-blue);
  border-radius: 99px;
}
.edit__counter:hover::before {
  content: '';
}

.rating {}
.rating__item {
  padding: 5px 0 5px 0;
}
.rating__item-text {
  font-size: 18px;
}
.rating__item-text::after {
  content: ' ';
}
.rating__item-text.rating__item-text_position {
  padding: 0 10px 0 0;
}
.rating__item-text.rating__item-text_position::after {
  content: '.';
}
.rating__item-text.rating__item-text_name {}
.rating__item-text.rating__item-text_position,
.rating__item-text.rating__item-text_name {
  font-weight: 600;
}

.all__placeholder {
  padding: 5px 0 5px 0;
}

</style>