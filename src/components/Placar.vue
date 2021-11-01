<template>
    <section class="score">Placar <span>{{this.vitoria}}</span> x <span>{{this.derrota}}</span> Computador</section>
</template>

<script>
    export default {
        props: {
            vitoria: Number,
            derrota: Number
        }
    }
</script>

<style lang="scss">
section.score {
    border-bottom: 1px solid black;
    padding: 24px;
    font-size: 18px;

    span {
        padding: 8px;
        font-weight: bold;
        border: 1px solid black;
    }
}
</style>