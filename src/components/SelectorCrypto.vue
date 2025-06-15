<template>
    <div>
        <ul>
            <li :class="{ 'active': cryptoNow === 'BTC' }" @click="setCrypto('BTC')">Bitcoin</li>
            <li :class="{ 'active': cryptoNow === 'ETH' }" @click="setCrypto('ETH')">Ethereum</li>
            <li :class="{ 'active': cryptoNow === 'SOL' }" @click="setCrypto('SOL')">Solana</li>
            <li :class="{ 'active': cryptoNow === 'DOGE' }" @click="setCrypto('DOGE')">Dogecoin</li>
            <li :class="{ 'active': cryptoNow === 'ADA' }" @click="setCrypto('ADA')">Cardano</li>
            <li :class="{ 'active': cryptoNow === 'DOT' }" @click="setCrypto('DOT')">Polkadot</li>
            <li :class="{ 'active': cryptoNow === 'USDT' }" @click="setCrypto('USDT')">USDT</li>
        </ul>
    </div>
</template>

<script>
export default {
    name: 'SelectorCrypto',
    props: {
        cryptoNow: {
            type: String,
            required: true,
            default: ''
        }
    },
    methods:{
        setCrypto(val){
            this.$emit('changeCrypto', val);
        },
    },
}
</script>

<style scoped>
ul {
    list-style: none;
    padding: 0;
    width: 200px;
    border-radius: 3px;
    border: 3px solid #000;
}
li{
    display: block;
    background-color: #1A032D;
    color:#fff;
    padding: 20px 0;
}

li:hover, li.active {
    background-color: #24043e;
    cursor: pointer;
}
</style>
