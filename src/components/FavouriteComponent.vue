<template>
    <div>
        <h2>Вибрані пари криптовалют:</h2>
        <div v-if="favs && favs.length > 0">
            <div @click="setfromFavs(index)" v-for="(el, index) in favs" :key="index" class="fav-item">
                <span>{{ el.from }}</span>
                <span>{{ el.to }}</span>
            </div>
        </div>
        <div v-else>
            <p class="empty-message">Список вибраних порожній.</p>
        </div>
    </div>
</template>

<script>

export default {
    name: 'FavouriteComponent',
    props: {
        favs: {
            type: Array, 
            required: true, 
            default: () => []
        },
    },
    methods: {
        setfromFavs(index){
            this.$emit('setfromFavs', index);
        }
    }
}
</script>

<style scoped>
.fav-item span {
    color: #ddd;
    margin: 5px 0;
    padding: 10px 15px;
    background-color: rgb(0, 139, 139);
    color:white;
}
.empty-message {
    color: #aaa;
    font-style: italic;
}
h2{
    color:#fff;
}
.fav-item {
    display: flex;
    justify-content: space-between;
    width: 500px;
    margin: 10px auto;
    transition: all 0.5s ease;
}
.fav-item:hover{
    opacity: 0.7;
    cursor: pointer;
    transform: scale(1.1);
}
</style>