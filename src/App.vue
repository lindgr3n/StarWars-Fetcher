<template>
  <div id="app" class="w-full">
    <div class="flex flex-col p-8 max-w-sm rounded overflow-hidden shadow-lg">
      <div class="flex flex-col" v-if="!!person.name">
        <h1>{{ person.name }}</h1>
        <h3>{{ person.homeworld.name }}</h3>

        <div class="flex justify-center p-8">
          <div class="flex pr-8">
            <p class="pr-2 font-bold">Gender:</p>
            <p>{{ person.gender }}</p>
          </div>
          <div>
            <div class="flex">
              <p class="pr-2 font-bold">Height:</p>
              <p>{{ person.height }}</p>
            </div>
            <div class="flex">
              <p class="pr-2 font-bold">Weight:</p>
              <p>{{ person.mass }}</p>
            </div>
          </div>
        </div>
        <div class="pb-4">
          <h3 class="p-4">Appears in:</h3>
          <ul class="list-reset">
            <li v-for="film in person.films" :key="film.episode_id">
              {{ film.title }}
            </li>
          </ul>
        </div>
      </div>
      <button
        @click="getRandomPerson"
        class="bg-blue hover:bg-blue-dark text-white font-bold py-2 px-4 rounded"
      >
        Get random Star Wars person
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',

  data() {
    return {
      person: {
        name: '',
        homeworld: {},
        gender: '',
        height: '',
        mass: '',
        films: []
      },
      loading: false
    };
  },

  async created() {
    this.getRandomPerson();
  },

  methods: {
    async getRandomPerson() {
      // 1-88
      const randomPerson = Math.floor(Math.random() * 88) + 1;
      const person = await this.getPerson({ id: randomPerson });
      console.log('DATA', person);
      const homeWorld = await this.getHomeworld({ url: person.homeworld });
      const films = await this.getFilms({ urls: person.films });
      person.homeworld = homeWorld;
      person.films = films;
      Object.assign(this.person, person);
    },

    async getPerson({ id }) {
      return await this.request({ url: `https://swapi.co/api/people/${id}` });
    },

    async getHomeworld({ url }) {
      return await this.request({ url });
    },

    async getFilms({ urls }) {
      const filmsPromises = urls.map(url => this.request({ url }));
      return await Promise.all(filmsPromises);
    },

    async request({ url }) {
      const response = await fetch(url);
      const json = await response.json();
      return json;
    }
  }
};
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
