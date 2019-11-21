<template lang="html">
  <div class="container">
    <h1 class="mt-5">{{this.title}}</h1>
    <div class="row mt-5">
      <div class="col-md-6">
        <div class="player">
          <h2>🗡️ Player</h2>
          <div class="progress">
            <div class="progress-bar" role="progressbar" v-bind:style="{width: stats.player + '%'}"></div>
          </div>
        </div>
      </div>
      <div class="col-md-6">
        <div class="dragon">
          <h2>🐉 Dragon</h2>
          <div class="progress">
            <div class="progress-bar" role="progressbar" v-bind:style="{width: stats.dragon + '%'}"></div>
          </div>
        </div>
      </div>
    </div>
    <div class="row mt-5">
      <div class="col-md-6 offset-md-3">
        <div class="action-bar">
          <button class="btn btn-primary" @click="performAction('player', 'dragon', 'attacc')">Attac</button>
          <button class="btn btn-secondary" @click="performAction('player', 'dragon', 'protecc')">Protecc</button>
          <button class="btn btn-danger" @click="performAction('player', 'dragon', 'healthChecc')">Health Checc</button>
          <button class="btn btn-warning" @click="performAction('player', 'dragon', 'whatTheHecc')">What the hecc?!</button>
        </div>
        <div class="log-bar">
          <ul>
            <template v-for="item in actionLog.slice().reverse()">
              <li v-bind:class="item.performer">{{item.performer}} {{item.action}} for {{item.hitpoints}} hitpoints </li>
            </template>
          </ul>
        </div>
      </div>
    </div>
    <footer class="page-footer font-small blue pt-4">
      <div class="footer-copyright text-center py-3">
        <span>© {{year}} Copyright:</span>
        <a href="https://lagerweij.tech"> Lagerweij.tech</a>
      </div>
    </footer>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import _ from "underscore";
import bootstrap from "bootstrap";
import "bootstrap/dist/css/bootstrap.css"

export default Vue.extend({
  data: function(){
    return {
      title: "What The Hecc!?",
      stats: {
        player: 100,
        dragon: 100,
      },
      year: null,
      actionLog: []
    }
  },
  mounted: function () {
    let date = new Date();
    this.year = date.getFullYear();
  },
  methods: {
    getYear(){
      let d = Date.new();
      return d.getFullYear();
    },
    performAction(performer, target, action){
      let totalPoints
      const actions = {
        attacc: () => {
          totalPoints = this.diceRoll()
          this.stats[target] = this.stats[target] - totalPoints
        },
        protecc: () => {
          let hitpoints = this.diceRoll(0.5)
          let defpoints = this.diceRoll(0.5)
          totalPoints = hitpoints + defpoints
          
          this.stats[target] = this.stats[target] - hitpoints
          this.stats[performer] = this.stats[performer] + defpoints
        },
        healthChecc: () => {
          totalPoints = this.diceRoll()
          this.stats[performer] = this.stats[performer] + totalPoints
        },
        whatTheHecc: () => {
          this.stats["dragon"] = 100
          this.stats["player"] = 100
          this.actionLog = []
        },
      }
      actions[action]()
      this.logAction(performer, action, totalPoints)

      if (performer == "player") {
        const keys = Object.keys(actions)
        let dragonAction = keys[_.random(0, keys.length - 2)]
        this.performAction("dragon", "player", dragonAction)
      }

      if (this.stats.player <= 0) {
        actions.whatTheHecc();
        alert('Dragon wins!')
      } else if (this.stats.dragon <= 0) {
        actions.whatTheHecc();
        alert('Player wins!');
      }
      
    },
    logAction(performer, action, hitpoints){
      this.actionLog.push({
        performer: performer,
        action: action,
        hitpoints: hitpoints
      });
    },
    dragonAction(){
      this.attacc('dragon');
    },
    diceRoll(multiplier = 1){
      let result = _.random(1, 10) * multiplier;
      return result;
    }
  }
});
</script>

<style lang="sass" scoped>
  .progress
    height: 3rem
  .action-bar
    display: flex
    justify-content: space-between
  .log-bar
    ul
      padding: 0
      margin-top: 2rem
      li
        display: block
        list-item: none
        line-height: 2.5rem
        padding: 0 10px
        margin: 0.25rem 0
        &.player
          background: lighten(blue, 40%)
          border: 1px solid lighten(blue, 20%)
        &.dragon
          background: lighten(red, 40%)
          border: 1px solid lighten(red, 20%)
</style>
