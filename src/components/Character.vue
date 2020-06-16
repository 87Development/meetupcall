<template>
    <transition name="fade">
    <div @click="actionModal" class="character__wrapper my-2 px-2 w-1/2 overflow-hidden sm:my-2 sm:px-2 sm:w-1/3 md:my-2 md:px-2 md:w-1/2 lg:my-2 lg:px-2 lg:w-1/4 xl:my-2 xl:px-2 xl:w-1/4 cursor-pointer">
            <div class="relative">
                <img :src="character.image" class="min-w-full h-auto object-cover" />
            </div>
        </div>
    </transition>
</template>
<script>
export default {
    name: 'Character',
    props:['character', 'doModal'],
    methods: {
        showMoreInfo() {
            console.log("in child");
        },
        actionModal() {
            this.$emit('doModal', this.character);
        }
    }
}
</script>

<style scoped>
    .fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
