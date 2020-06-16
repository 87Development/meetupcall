<template>
<div class="bg-white shadow-lg">
        <div class="relative z-10" style="clip-path: polygon(0 0, 100% 0, 100% 100%, 0 calc(100% - 5vw));">
      <img class="w-full" :src="character.image"
           alt="Profile image" />
      <div class="text-center absolute w-full" style="bottom: 4rem">
        <p class="text-white tracking-wide uppercase text-lg font-bold" style="text-shadow:2px 1px 1px rgba(0,0,0,0.6);">{{ character.name}}</p>
      </div>
    </div>
    <div class="pt-6 pb-8 text-gray-600 text-center">
      <p>{{character.species}}</p>
      <p class="text-sm">Located on {{character.location.name}}</p>
    </div>

    <div class="pb-10 uppercase text-center tracking-wide flex justify-around">
      <div class="status">
        <p class="text-gray-400 text-sm">Status</p>
        <p class="text-lg font-semibold" :class="{
            'text-green-500' : character.status === 'Alive',
            'text-red-500' : character.status === 'Dead',
            'text-gray-500' : character.status === 'Unknown'
            }">{{character.status}}</p>
      </div>
      <div class="episode">
        <p class="text-gray-400 text-sm">Episodes</p>
        <p class="text-lg font-semibold text-blue-300">{{character.episode.length}}</p>
      </div>
    </div>
    </div>
</template>

<script>
export default {
    name: 'MoreCharacterInfo',
    props: ['character']
}
</script>

<style scoped>

.alive:before {
    content: " ",
    
}

</style>