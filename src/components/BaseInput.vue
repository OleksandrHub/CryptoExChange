<template>
    <div>
        <input type="number" placeholder="Введіть число" @input="handleInputChange($event.target.value)" min="0" />
        <button @click="convert()">Конвертувати</button>
        <button @click="favourite()">Вибрані</button>
    </div>
</template>

<script>
export default {
    name: 'BaseInput',
    methods: {
        handleInputChange(value) {
            this.$emit('changeAmount', value);
        },
        convert() {
            this.$emit('convert');
        },
        favourite() {
            this.$emit('favourite');
        }
    }
}
</script>

<style scoped>
input {
    display: block;
    margin: 0 auto;
    border-radius: 3px;
    border:transparent;
    padding: 10px 15px;
    background-color: #fafafa;
    color:#333;
    outline: none;
    width: 500px;
    position: relative;
    top:-50px;
    font-size: 1.2em;
}

button {
    position: relative;
    margin: 0 10px;
    top:-20px;
    padding: 15px 20px;
    color:#fff;
    text-transform: uppercase;
    cursor: pointer;
    background-color: #1a032d;
    border: 0;
    border-radius: 3px;
    font-size: 1.2em;
}
</style>
