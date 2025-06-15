<template>
  <div>
    <h1>CRYPTO</h1>
    <BaseInput @changeAmount="changeAmount" @convert="convert" @favourite="favourite" />
    <p v-if="error != ''" class="error">{{error}}</p>
    <p v-if="result != 0" class="result">Результат : {{result}}</p>
    <FavuoriteComponent :favs="favs" @setfromFavs="setfromFavs" v-if="favs.length > 0"/>
    <div className="selectors">
      <SelectorCrypto @changeCrypto="changeCryptoFirst" :cryptoNow="cryptoFirst" />
      <SelectorCrypto @changeCrypto="changeCryptoSecond" :cryptoNow="cryptoSecond" />
    </div>
  </div>
</template>

<script>
import BaseInput from './components/BaseInput.vue';
import SelectorCrypto from './components/SelectorCrypto.vue';
import CryptoConvert from 'crypto-convert';
import FavuoriteComponent from './components/FavouriteComponent.vue';

const convert = new CryptoConvert();

export default {
  components: {
    BaseInput,
    SelectorCrypto,
    FavuoriteComponent
  },
  data(){
    return{
      amount: 0,
      cryptoFirst: '',
      cryptoSecond: '',
      error: '',
      result: 0,
      favs: []
    }
  },
  methods: {
    changeAmount(val){
      this.amount = val;
      this.result = 0;
    },
    changeCryptoFirst(val){
      this.cryptoFirst = val;
    },
    changeCryptoSecond(val){
      this.cryptoSecond = val;
    },
    async convert(){
      this.result = 0;
      if(this.amount <= 0){
        this.error = "Введіть число більше 0";
        return;
      } else if (this.cryptoFirst === '' || this.cryptoSecond === ''){
        this.error = "Оберіть криптовалюти";
        return;
      } else if (this.cryptoFirst === this.cryptoSecond){
        this.error = "Оберіть різні криптовалюти";
        return;
      }

      this.error = '';

      await convert.ready();

      this.result = convert[this.cryptoFirst][this.cryptoSecond](this.amount);
    },
    favourite(){
      if(this.cryptoFirst === '' || this.cryptoSecond === ''){
        this.error = "Оберіть криптовалюти, які додати до списку";
        return;
      } else if (this.cryptoFirst === this.cryptoSecond){
        this.error = "Оберіть різні криптовалюти";
        return;
      }

      this.error = '';

      const existingPairIndex = this.favs.findIndex(
        (fav) => fav.from === this.cryptoFirst && fav.to === this.cryptoSecond
      );

      if (existingPairIndex > -1) {
        this.favs.splice(existingPairIndex, 1);
        this.error = `Пару ${this.cryptoFirst}/${this.cryptoSecond} видалено зі списку обраних.`;
      } else {
        // Пари не існує, додаємо її
        this.favs.push({
          from: this.cryptoFirst,
          to: this.cryptoSecond,
        });
        // Можна додати повідомлення про успішне додавання, якщо потрібно
        // this.error = `Пару ${this.cryptoFirst}/${this.cryptoSecond} додано до списку обраних.`;
      }
    },
    setfromFavs(index){
      this.cryptoFirst = this.favs[index].from;
      this.cryptoSecond = this.favs[index].to;

    }
  }
}
</script>

<style scoped>
.selectors{
  display: flex;
  justify-content: space-around;
  width: 700px;
  margin: 0 auto;
}
.error{
  display: inline-block; 
  background-color: #d03939;
  color: white;
  padding: 5px 10px;
  border-radius: 3px;
}
.error::before{
  content: "! ";
}
.result{
    font-family: "Nabla", "Montserrat", sans-serif;
    font-size: 2em;
    color: whitesmoke;
}
</style>
