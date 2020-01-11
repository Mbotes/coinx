<template>
  <div class="hello container">
    <h1>{{ msg }}</h1>
    <div class="row">
      <div class="jumbotron col-xs-offset-2 col-xs-8">
        <p>
          This website indexes the top 100 cryptocurrencies by market cap (how
          much the sum of all coins is collectively worth), and gives you an easy
          way to compare cryptocurrency performance and rank over the last week.
        </p>
      </div>
    </div>

    <table class="table table-hover">
      <thead>
      <tr>
        <td>Rank</td>
        <td>Name</td>
        <td>Symbol</td>
        <td>Price ($)</td>
        <td>1H</td>
        <td>1D</td>
        <td>1W</td>
        <td>Market Cap ($)</td>
      </tr>
      </thead>
      <tbody>
      <tr  v-for='coin in coins' v-bind:key="coin.id">
        <td>{{coin.rank}}</td>
        <td>{{coin.name}}</td>
        <td>{{coin.symbol}}</td>
        <td>{{coin.price_usd | roundNumbers | nFormatter}}</td>
        <td :class="{ active: coin.percent_change_1h > 0, danger: coin.percent_change_1h < 0}">{{coin.percent_change_1h}}</td>
        <td :class="{ active: coin.percent_change_24h > 0, danger: coin.percent_change_24h < 0}">{{coin.percent_change_24h}}</td>
        <td :class="{ active: coin.percent_change_7d > 0, danger: coin.percent_change_7d < 0}">{{coin.percent_change_7d}}</td>
        <td>{{coin.market_cap_usd | nFormatter}}</td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<script>

import axios from 'axios'
export default {
  name: 'Home',
  data () {
    return {
      coins: [],
      errors: [],
      msg: 'Welcome to Coins-X-change'
    }
  },
  methods: {
    fetchCoins: function () {
      axios.get('https://api.coinmarketcap.com/v1/ticker/')
        .then(response => {
          this.coins = response.data
        })
        .catch(e => {
          this.errors.push(e)
        })
    }
  },
  filters: {
    roundNumbers: function (num) {
      return Math.round(num * 100) / 100
    },
    nFormatter: function (num) {
      if (num >= 1000000000) {
        return (num / 1000000000).toFixed(1).replace(/\.0$/, '') + 'B'
      }
      if (num >= 1000000) {
        return (num / 1000000).toFixed(1).replace(/\.0$/, '') + 'M'
      }
      if (num >= 1000) {
        return (num / 1000).toFixed(1).replace(/\.0$/, '') + 'K'
      }
      return num
    }

  },
  created () {
    this.fetchCoins()

    setInterval(function () {
      this.fetchCoins()
    }.bind(this), 10000)
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.active {
  color: green
}
.danger {
  color: red
}
</style>
