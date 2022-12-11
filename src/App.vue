<template >
  <div>
    <header>
      <h1>Monster Slayer</h1>
    </header>
    <section id="monster" class="container">
      <h2>Monster Health</h2>
      <div class="healthbar">
        <div class="healthbar__value " :style="monsterBarStyle"></div>
      </div>
    </section>
    <section id="player" class="container">
      <h2>Your Health</h2>
      <div class="healthbar">
        <div class="healthbar__value" :style="playerBarStyle"></div>
      </div>
    </section>
    <section v-if="winner" class="container">
      <h2>Game Over!</h2>
      <h3 v-if="winner === 'player'">You Win the game!🥰</h3>
      <h3 v-if="winner === 'draw'">It's a draw!😎</h3>
      <h3 v-if="winner === 'monster'">You Lost the game!😑</h3>
      <button @click="startNewGame">Start New Game!</button>
    </section>
    <section v-else id="controls">
      <button @click="attackMonster">ATTACK</button>
      <button @click="specialAttackMonster" :disabled="disableButton">SPECIAL ATTACK</button>
      <button @click="healPlayer">HEAL</button>
      <button :disabled="count <= 0" @click="surrender">SURRENDER</button>
    </section>
    <section id="log" class="container">
      <h2>Battle Log</h2>
      <ul>
        <li v-for="log in logMessages" :key="log.value">
          <span class="mr-3" :class="{ 'log--player': log.who === 'player', 'log--monster': log.who === 'monster' }">{{
              log.who ===
                'player' ? "Player" : "Monster"
          }}</span>
          <span class="mr-3">{{ log.what === 'heal' ? 'heals himself for ' : 'attacks and deals' }} - </span>
          <span
            :class="{ 'log--damage': log.who === 'player' || log.who === 'monster', 'log--heal': log.what === 'heal' }">{{
    log.value
            }}</span>
        </li>
      </ul>
    </section>
  </div>
</template>
<script>

function getRandomValue(max, min) {
  return Math.floor(Math.random() * (max - min) + min);
}
export default {
  name: 'App',
  data() {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      count: 0,
      winner: null,
      logMessages: [],
    }
  },
  computed: {
    monsterBarStyle() {
      if (this.monsterHealth < 0) {
        return { width: '0%' };
      }
      return { width: this.monsterHealth + '%' }
    },
    playerBarStyle() {
      if (this.playerHealth < 0) {
        return { width: '0%' };
      }
      return { width: this.playerHealth + '%' }
    },
    disableButton() {
      return this.count < 3;
    }
  },
  methods: {
    startNewGame() {
      this.logMessages = [];
      this.monsterHealth = 100;
      this.playerHealth = 100;
      this.count = 0;
      this.winner = null;
    },
    attackMonster() {
      const attackValue = getRandomValue(12, 5)
      this.monsterHealth -= attackValue;
      this.addLogMessage('player', 'attack', attackValue)
      this.attackPlayer();
    },
    attackPlayer() {
      const attackValue = getRandomValue(15, 8)
      this.playerHealth -= attackValue;
      this.count++;
      this.addLogMessage('monster', 'attack', attackValue)
    },
    specialAttackMonster() {
      const attackValue = getRandomValue(10, 25)
      this.monsterHealth -= attackValue;
      this.attackPlayer();
      this.count = 0;
      this.addLogMessage('player', 'special-attack', attackValue)
    },
    healPlayer() {
      const healValue = getRandomValue(8, 20)
      if (this.playerHealth + healValue > 100) {
        this.playerHealth = 100;
      } else {
        this.playerHealth += healValue;
      }
      this.addLogMessage('player', 'heal', healValue)
      this.attackPlayer();
    },
    surrender() {
      this.winner = 'monster';
    },
    addLogMessage(who, what, value) {
      this.logMessages.unshift({ who: who, what: what, value: value })
    }
  },
  watch: {
    playerHealth(value) {
      if (value <= 0 && this.monsterHealth <= 0) {
        this.winner = 'draw';
      } else if (value <= 0) {
        this.winner = 'monster'
      }
    },
    monsterHealth(value) {
      if (value <= 0 && this.playerHealth <= 0) {
        this.winner = 'draw';
      } else if (value <= 0) {
        this.winner = 'player'
      }
    }
  }
}
</script>
<style lang="">
  
</style>