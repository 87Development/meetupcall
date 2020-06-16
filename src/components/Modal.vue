<template>
    <transition name="modal">
        <div v-if="visible" @click="actionClose" class="fixed z-10 top-0 left-0 h-screen w-screen flex bg-black bg-opacity-50 justify-centent align-center items-center">
            <div class="flex w-screen h-screen justify-center items-center">
                <div class="w-3/4 mx-auto shadow-lg flex justify-center items-center" @click.stop>
                    <div class="modal-body flex relative">
                        <button type="button" class="appearance-none absolute top-0 right-0 mt-2 mr-2 z-20" @click="actionClose">
                            <svg class="ml-auto fill-current text-gray-700 w-6 h-6 cursor-pointer" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 18 18">
                                <path d="M14.53 4.53l-1.06-1.06L9 7.94 4.53 3.47 3.47 4.53 7.94 9l-4.47 4.47 1.06 1.06L9 10.06l4.47 4.47 1.06-1.06L10.06 9z"/>
                            </svg>
                        </button>
                        <slot />
                    </div>
                </div>
            </div>
        </div>
    </transition>
</template>

<script>
export default {
    props: ['visible', 'close'],
    mounted() {
        document.addEventListener("keydown", (e) => {
        if (this.show && e.keyCode == 27) {
            this.close();
        }
    });
    },
    methods: {
        actionClose() {
            this.$emit('close');
        }
    }
}
</script>

<style scoped>

    .modal-enter {
        opacity: 0;
    }

    .modal-leave-active {
        opacity: 0;
    }

    .modal-enter .modal-container,
    .modal-leave-active .modal-container {
        -webkit-transform: scale(1.1);
        transform: scale(1.1);
    }

</style>