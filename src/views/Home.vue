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
        <td>Logo</td>
        <td>Name</td>
        <td>Symbol</td>
        <td>Price ($)</td>
        <td>24H</td>
        <td>Price Change % 24h</td>
        <td>Market Cap ($)</td>
      </tr>
      </thead>
      <tbody>
      <tr  v-for='coin in coins' v-bind:key="coin.id">
        <td>{{coin.market_cap_rank}}</td>
        <td><img :src=coin.image height="30" width="30" alt=""></td>
        <td>{{coin.name}}</td>
        <td>{{coin.symbol}}</td>
        <td>{{coin.current_price | roundNumbers | nFormatter}}</td>
        <td>{{coin.price_change_24h | roundNumbers | nFormatter}}</td>
        <td :class="{ active: coin.price_change_percentage_24h > 0, danger: coin.price_change_percentage_24h < 0}">{{coin.price_change_percentage_24h}}</td>
        <td>{{coin.market_cap | nFormatter}}</td>
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
      axios.get('https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false')
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
