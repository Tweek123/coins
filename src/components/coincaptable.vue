<template>
<table class="table">
  <thead class="table__header">
    <tr class="table__header-row">
      <th class="table__header-col table__header-col-w-16"><p class="table__header-text">Название</p></th>
      <th class="table__header-col table__header-col-w-20"><p class="table__header-text">Стоимость</p></th>
      <th class="table__header-col table__header-col-w-32"><p class="table__header-text">Рыночная Капитализация</p></th>
      <th class="table__header-col table__header-col-w-32"><p class="table__header-text">Суточный объем</p></th>
    </tr>
  </thead>
  <tbody class="table__body">
    <tr class="table__body-row"
        v-for="coin in coins"
        v-bind:key="coin.id"
      >
    <th class = "table__body-col table__header-col-w-16"><p class="tabe__body-text">{{ coin.id}}</p></th>
      <td class="table__body-col table__header-col-w-20"><p class="tabe__body-text">{{ coin.priceUsd}}</p></td>
      <td class="table__body-col table__header-col-w-32"><p class="tabe__body-text">{{ coin.marketCapUsd}}</p></td>
      <td class="table__body-col table__header-col-w-32"><p class="tabe__body-text">{{ coin.volumeUsd24Hr}}</p></td>
    </tr>
  </tbody>
</table>
</template>
<script>
export default {
  data() {
    return {
      coins: []
  }
},

  mounted() {

      var $this = this;
      
      $.ajax({
				      type : 'GET',
				      url : 'https://api.coincap.io/v2/assets?limit=15'
			}).done(function (response) {
        
              $this.coins = response.data;
              let socketUrl = 'wss://ws.coincap.io/prices?assets='
          
              $this.coins.forEach(coin => {
                  socketUrl = socketUrl + coin.id+',';
              });
              
              const pricesWs = new WebSocket(socketUrl)

              pricesWs.onmessage = function (msg) {
              
              let socketCoins = JSON.parse(msg.data); 

              $this.coins.map(function(coin) {
                  if(socketCoins[coin.id]!== undefined) {
                    coin.priceUsd = socketCoins[coin.id];
                  }
              });

              }



        });
  }
}
</script>

<style lang="css">
   * {
     padding: 0;
     margin: 0;
   }
  .table  {
    position: relative;
    margin: 100px auto;
    width: 90%;
  }

  .table__header {
    position: absolute;
    width: 87vw;
  } 
  .table__body {
    display: block;
    margin-top: 45px;
    height: 400px;
    overflow: hidden;
  }

  .table__body:hover {
    overflow-y: scroll;
  }

  .table__header-row {
    display: flex;
    flex-direction: row;
    border-bottom: 1px solid black;
  }
  .table__header-col {
    display: flex;
    padding: 10px 0px;
  }
  .table__body-row {
    display: flex;
    flex-direction: row;
    border-bottom: 1px solid black;
    max-width: 87vw;
  }
  .table__body-col {
    padding: 10px 0px;
    text-align: left;
  }

  .table__header-col-w-16 {
    width: 16%;
  }

  .table__header-col-w-20 {
    width: 20%;
  }

  .table__header-col-w-32 {
    width: 32%
  }

  @media (max-width: 1200px) {
    .table__header-col-w-32 { 
      width: 0%;
      display: none;
    }

    .table__header-col-w-16, .table__header-col-w-20 {
      width: 50%;
    }

    .table__header {
      width: 86vw;
    }

    .table__body-row {
      max-width: 86vw;
    }
  }
  
  @media (max-width: 700px) {

    .table__header {
      width: 82vw;
    }

    .table__body-row {
      max-width: 82vw;
    }

  }
</style>
