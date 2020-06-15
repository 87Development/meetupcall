<template>
  <div class="w-screen h-100 bg-gray-900 flex direction-row justify-center py-16 relative">
      <Modal v-if="showModal" />
      <div class="flex mx-4 flex-wrap">
         <div class="w-full flex flex-wrap md:justify-between mb-4">
          <button class="bg-blue-600 w-full mb-3 md:mb-0 md:w-auto text-white font-bold py-2 px-4 border-b-4 hover:border-b-2 hover:border-t-2 border-blue-900 rounded" @click="showMeARandom">Show Random</button>
          <button class="bg-blue-600 w-full mb-3 md:mr-auto md:ml-3 md:mb-0 md:w-auto text-white font-bold py-2 px-4 border-b-4 hover:border-b-2 hover:border-t-2 border-blue-900 rounded" @click="filtersVisible = !filtersVisible">{{filterState}} Filters</button>
          <input class="h-10 bg-white w-full md:w-auto border border-gray-300 rounded p-4 text-gray-600 text-sm focus:outline-none focus:border-gray-400" v-model="searchValue" placeholder="Filter by name..." type="text"/>
        </div>
        <transition name="expand">
          <div v-if="filtersVisible" class="w-full flex flex-wrap items-center mb-4">
              <div class="w-1/2 md:w-auto mr-8 mb-4">
                <label class="text-left block uppercase tracking-wide text-white text-s font-bold mb-2">
                  Species
                </label>
                <div class="relative text-left">
                  <select v-model="filter.species" class="block appearance-none w-full bg-grey-lighter border border-grey-lighter text-grey-darker py-3 px-4 pr-8 rounded" id="grid-state">
                    <option value="">Select</option>
                    <option value="human">Human</option>
                    <option value="alien">Alien</option>
                  </select>
                  <div class="pointer-events-none absolute top-0 right-0 flex mt-4 items-center px-2 text-grey-darker">
                    <svg class="h-4 w-4 stroke-current text-grey-darker" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"/></svg>
                  </div>
                </div>
              </div>
              <div class="w-1/2 md:w-auto mr-8 mb-4">
                <label class="text-left block uppercase tracking-wide text-white text-s font-bold mb-2">
                  Gender
                </label>
                <div class="relative text-left">
                  <select v-model="filter.gender" class="block appearance-none w-full bg-grey-lighter border border-grey-lighter text-grey-darker py-3 px-4 pr-8 rounded" id="grid-state">
                    <option value="">Select</option>
                    <option value="male">Male</option>
                    <option value="female">Female</option>
                    <option value="genderless">Genderless</option>
                    <option value="unknown">Unknown</option>
                  </select>
                  <div class="pointer-events-none absolute top-0 right-0 flex mt-4 items-center px-2 text-grey-darker">
                    <svg class="h-4 w-4 stroke-current text-grey-darker" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"/></svg>
                  </div>
                </div>
              </div>
              <!-- <div class="w-1/2 md:w-auto">
                <label class="text-left block uppercase tracking-wide text-white text-s font-bold mb-2">
                  Deceased
                </label>
                <div class="relative text-left mb-4">
                  <div class="relative inline-block w-10 select-none transition duration-200 ease-in">
                    <input type="checkbox" v-model="filter.status" name="toggle" id="toggle" class="toggle-checkbox absolute block w-6 h-6 rounded-full bg-white border-4 appearance-none cursor-pointer"/>
                    <label for="toggle" class="toggle-label block overflow-hidden h-6 rounded-full bg-gray-300 cursor-pointer"></label>
                  </div>
                </div>
              </div> -->
              <div class="w-full text-left">
                  <button @click="handleSearchWithFilters" type="button" class="bg-blue-600 w-full mb-3 md:mr-auto md:mb-0 md:w-auto text-white font-bold py-2 px-4 border-b-4 hover:border-b-2 hover:border-t-2 border-blue-900 rounded">Filter</button>
                  <button @click="handleFiltersReset" type="button" class="ml-4 bg-blue-600 w-full mb-3 md:mr-auto md:mb-0 md:w-auto text-white font-bold py-2 px-4 border-b-4 hover:border-b-2 hover:border-t-2 border-blue-900 rounded">Reset</button>
              </div>
          </div>
        </transition>
        <div class="flex flex-1 flex-wrap -mx-3 overflow-hidden sm:-mx-2 md:-mx-2 lg:-mx-2 xl:-mx-2 relative">
            <div class="z-10 text-center font-black fixed bottom-0 left-0 w-full flex items-center justify-center align-center p-4 bg-white bg-opacity-50 text-black">
              Scroll To Load More
            </div>
            <Character v-for="character in this.charactersToSearch" :key="character.id" :name="character.name" :character="character" @click="showMoreInfo" />
        </div>
      </div>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios';
import '../assets/css/main.css';
import Character from '../components/Character';
import Modal from '../components/Modal';
export default {
  name: 'Home',
  created() {
    window.addEventListener('scroll', () => {
      this.bottom = this.bottomVisible()
    });
  },
  destroyed() {
    window.removeEventListener('scroll', this.bottomVisible);
  },
  mounted() {
    this.getBaseData();
  },
  beforeDestroy() {
    document.removeEventListener('scroll', this.handleScroll);
  },
  components: {
    Character,
    Modal
  },
  data() {
    return {
      nextUrl: '',
      bottom:false,
      filtersVisible: false,
      filterByDeceased: false,
      filter: {
        species: '',
        gender: '',
        deceased: false
      },
      filterByGender: '',
      searchValue: '',
      characters: [],
      characterCount: 0,
      showModal: false
    }
  },
  watch: {
    bottom(bottom) {
      if(bottom) {
        this.addCharacters()
      }
    }
  },
  methods: {
    bottomVisible() {
      const scrollY = window.scrollY
      const visible = document.documentElement.clientHeight
      const pageHeight = document.documentElement.scrollHeight
      const bottomOfPage = visible + scrollY >= pageHeight
      return bottomOfPage || pageHeight < visible
    },
    addCharacters() {
      const url = this.nextUrl
      axios.get(url).then(response => {
        this.characters.push(...response.data.results);
        this.nextUrl = response.data.info.next;
        if (this.bottomVisible()) {
          this.addCharacters()
        }
      });
    },
    getBaseData() {
      axios.get('https://rickandmortyapi.com/api/character').then(response => {
        this.characters.push(...response.data.results);
        this.characterCount = response.data.info.count
        this.nextUrl = response.data.info.next;
      });
    },  
    handleSearchWithFilters() {
       this.searchValue = '';
       axios.get('https://rickandmortyapi.com/api/character/?' + this.filterQuery).then(response => {
          this.characters = response.data.results
          //console.log(this.character);
       });
    },
    handleFiltersReset() {
      this.searchValue = '';
      this.filter.gender = '';
      this.filter.species = '';
      this.getBaseData();
    },
    showMeARandom() {
      const id = Math.floor(Math.random() * this.characterCount) + 1;
       axios.get('https://rickandmortyapi.com/api/character/' + id).then(response => {
          this.character = response.data;
          console.log(this.character);
       });
    },
    showMoreInfo() {
      console.log("show more info");
    },
    // filterByName(evt) { 
    //  if(evt.target.value.length > 2 && evt.target.value != "") {
    //    console.log("started");
    //    this.$children.filter(child => {
    //     console.log(child);
    //     if(child.$props.name.includes(evt.target.value)) {
    //       child._props.visible = false
    //     }
    //    });
    //  } else if(evt.target.value == "") {
    //    console.log("restarted");
    //    this.$children.filter(child => {
    //      child.$props.visible = true;
    //    });
    //  }
    // },
    showMore() {
     
    },
    handleModalShow() {
      this.showModal = true;
    }
  },
  computed: {
    charactersToSearch() {
      return this.characters.filter(character => character.name.toLowerCase().includes(this.searchValue.toLowerCase()));
    },
    filterState() {
      if(this.filtersVisible) {
        return "Hide";
      }
      return "Show";
    },
    filterQuery() {
      let data = this.filter;
      const ret = [];
      for (let d in data) {
          ret.push(encodeURIComponent(d) + '=' + encodeURIComponent(data[d]));
      }
      return ret.join('&');
    },
    deceasedForApi() {
      return {
        status: this.filter.deceased ? 'dead' : 'alive',
        ...this.filter
      }
    }
  }
}
</script>

<style scoped>

  .toggle-checkbox:checked {
    @apply: right-0 bg-blue-600;
    right: 0;
    border-color: #3382CC;
  }
  .toggle-checkbox:checked + .toggle-label {
    @apply: bg-blue-600;
    background-color: #3382CC;
  }

  /* always present */
.expand-transition {
  transition: all .3s ease;
  height: 30px;
  padding: 10px;
  background-color: #eee;
  overflow: hidden;
}
/* .expand-enter defines the starting state for entering */
/* .expand-leave defines the ending state for leaving */
.expand-enter, .expand-leave {
  height: 0;
  padding: 0 10px;
  opacity: 0;

}
</style>