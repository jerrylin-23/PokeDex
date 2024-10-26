<template>
  <div class="container mx-auto p-6">
    <div v-if="loading" class="text-center py-4">
      <p>Loading Pokémon...</p>
    </div>

    <div v-else class="grid grid-cols-1 md:grid-cols-3 gap-6">
      <!-- Sidebar: Search & Pokémon List -->
      <div class="bg-white rounded-lg shadow-lg p-4">
        <h1 class="text-2xl font-bold text-center mb-4">Pokédex</h1> <!-- Added Title Here -->
        <input 
          v-model="searchTerm" 
          @input="filterPokemon" 
          placeholder="Search Pokémon..." 
          class="w-full p-2 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-400"
        />
        
        <ul class="mt-4 space-y-2 max-h-[75vh] overflow-y-auto">
          <li 
            v-for="pokemon in filteredPokemon" 
            :key="pokemon.id" 
            @click="selectPokemon(pokemon)"
            class="flex items-center space-x-4 p-2 rounded-md cursor-pointer hover:bg-gray-100"
          >
            <img :src="pokemon.sprite" alt="Pokemon" class="h-12" />
            <span class="capitalize text-lg font-medium">{{ pokemon.name }}</span>
          </li>
        </ul>
      </div>

      <!-- Main Content: Pokémon Details -->
      <div class="col-span-2 bg-white rounded-lg shadow-lg p-6">
        <div v-if="selectedPokemon">
          <div class="text-center">
            <h2 class="text-3xl font-bold capitalize mb-4">{{ selectedPokemon.name }}</h2>
            <img :src="selectedPokemon.sprite" class="h-48 mx-auto mb-6" :alt="selectedPokemon.name" />
          </div>

          <div class="flex justify-center space-x-4 mb-6">
            <button 
              v-for="tab in tabs" 
              :key="tab" 
              @click="switchTab(tab)"
              :class="['px-4 py-2 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-400', 
                { 'bg-blue-500 text-white': currentTab === tab, 'border': currentTab !== tab }]"
            >
              {{ tab }}
            </button>
          </div>

          <!-- Enhanced Stats Display -->
          <div v-if="currentTab === 'About'" class="grid grid-cols-1 gap-4">
          <div class="mt-4 p-4 border rounded-lg">
    <h3 class="text-lg font-semibold">Pokémon Lore</h3>
   <div class="lore-container">
                <p class="text-sm">{{ cleanLore }}</p>
              </div>
  </div>
            <div 
              v-for="stat in selectedPokemon.stats" 
              :key="stat.name"
              :class="['p-4 rounded-lg border-2', getStatColor(stat.value)]"
            >
              <div class="flex justify-between items-center mb-2">
                <span class="capitalize font-semibold text-gray-700">
                  {{ stat.name.replace('-', ' ') }}
                </span>
                <span class="font-bold">{{ stat.value }}</span>
              </div>
              <div class="w-full bg-gray-200 rounded-full h-2.5">
                <div 
                  :class="['h-2.5 rounded-full', getProgressColor(stat.value)]"
                  :style="{ width: `${(stat.value / 255) * 100}%` }"
                />
              </div>
            </div>
          </div>

          <!-- Moves Tab -->
          <div v-if="currentTab === 'Moves'" class="space-y-4">
            <div v-if="loadingMoves" class="text-center">
              <p>Loading moves...</p>
            </div>

            <div v-else class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div v-for="move in selectedPokemon.moveDetails" :key="move.name" class="p-4 border rounded-lg shadow-sm">
                <div class="flex justify-between items-center mb-2">
                  <h3 class="text-lg font-medium capitalize">{{ move.name }}</h3>
                  <span 
                    class="px-2 py-1 rounded-full text-sm text-white capitalize"
                    :class="getTypeColor(move.type)"
                  >
                    {{ move.type }}
                  </span>
                </div>

                <div class="grid grid-cols-2 gap-2 text-sm">
                  <div><strong>Power:</strong> {{ move.power }}</div>
                  <div><strong>Accuracy:</strong> {{ move.accuracy }}</div>
                  <div><strong>PP:</strong> {{ move.pp }}</div>
                  <div><strong>Category:</strong> {{ move.damage_class }}</div>
                </div>

                <p class="mt-2 text-sm">{{ move.effect }}</p>
              </div>
            </div>
          </div>

          <!-- Enhanced Evolution Chain -->
          <div v-if="currentTab === 'Evolution'" class="mt-8">
            <h3 class="text-xl font-semibold mb-4">Evolution Chain</h3>
            <div class="flex items-center justify-center space-x-4">
              <template v-for="(evo, index) in evolutionChain" :key="evo.name">
                <div class="text-center">
                  <img :src="evo.sprite" :alt="evo.name" class="mx-auto h-24 w-24" />
                  <span class="block mt-2 capitalize">{{ evo.name }}</span>
                </div>
                <div v-if="index < evolutionChain.length - 1" class="text-4xl text-gray-400">
                  →
                </div>
              </template>
            </div>
          </div>

        </div>
      </div>
    </div>
  </div>
</template>

<style>
.lore-container {
  max-height: 100px; /* Adjust this value as needed */
  overflow-y: auto; /* Enables vertical scrolling */
  padding-right: 1rem; /* Adds space for scrollbar */
}
</style>

<script>
export default {
  data() {
    return {
      pokemon: [],
      filteredPokemon: [],
      loading: true,
      searchTerm: '',
      selectedPokemon: null,
      currentTab: 'About',
      tabs: ['About', 'Moves', 'Evolution'],
      moveDetails: new Map(),
      loadingMoves: false,
      evolutionChain: null,
      lore: '',
    };
  },
  computed: {
  cleanLore() {
    // Remove unwanted characters (like weird arrow characters)
    return this.lore.replace(/[\u2191\u2193]/g, '').replace(/\n/g, ' '); // Adjust regex as necessary
  },
},


  async created() {
    try {
      const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=1118');
      const data = await response.json();

      const pokemonData = await Promise.all(
        data.results.map(async (pokemon) => {
          const detailResponse = await fetch(pokemon.url);
          const details = await detailResponse.json();

          return {
            id: details.id,
            name: details.name,
            sprite: details.sprites.front_default,
            types: details.types.map((type) => type.type.name),
            stats: details.stats.map((stat) => ({
              name: stat.stat.name,
              value: stat.base_stat,
            })),
            moves: details.moves.slice(0, 4).map((move) => ({
              name: move.move.name,
              url: move.move.url,
            })),
          };
        })
      );

      this.pokemon = pokemonData;
      this.filteredPokemon = pokemonData;
    } catch (error) {
      console.error('Error fetching Pokémon:', error);
    } finally {
      this.loading = false;
    }
  },

  methods: {
    filterPokemon() {
      const term = this.searchTerm.toLowerCase();
      this.filteredPokemon = this.pokemon.filter((p) =>
        p.name.toLowerCase().includes(term)
      );
    },

    async selectPokemon(pokemon) {
      this.selectedPokemon = pokemon;
      this.currentTab = 'About';

     const speciesResponse = await fetch(
  `https://pokeapi.co/api/v2/pokemon-species/${pokemon.id}/`
);
      const speciesData = await speciesResponse.json();
      await this.fetchEvolutionChain(speciesData.evolution_chain.url);
      this.lore = await this.fetchPokemonLore(pokemon.id);
    },

    async fetchEvolutionChain(url) {
      try {
        const response = await fetch(url);
        const data = await response.json();

        const chain = [];
        let current = data.chain;
        
        while (current) {
          // Fetch the Pokemon details to get the sprite
        const pokemonResponse = await fetch(
  `https://pokeapi.co/api/v2/pokemon/${current.species.name}/`
);
          const pokemonData = await pokemonResponse.json();
          
          chain.push({
            name: current.species.name,
            sprite: pokemonData.sprites.front_default
          });
          
          current = current.evolves_to[0];
        }
        
        this.evolutionChain = chain;
      } catch (error) {
        console.error('Error fetching evolution chain:', error);
      }
    },

    async switchTab(tab) {
      this.currentTab = tab;
      if (tab === 'Moves' && this.selectedPokemon) {
        await this.loadSelectedPokemonMoves();
      }
    },

    async loadSelectedPokemonMoves() {
      this.loadingMoves = true;
      try {
        const moveDetails = await Promise.all(
          this.selectedPokemon.moves.map((move) =>
            this.fetchMoveDetails(move.url)
          )
        );
        this.selectedPokemon.moveDetails = moveDetails.filter(
          (move) => move !== null
        );
      } catch (error) {
        console.error('Error loading moves:', error);
      } finally {
        this.loadingMoves = false;
      }
    },

    async fetchMoveDetails(moveUrl) {
      if (this.moveDetails.has(moveUrl)) return this.moveDetails.get(moveUrl);

      try {
        const response = await fetch(moveUrl);
        const data = await response.json();

        const moveDetail = {
          name: data.name,
          type: data.type.name,
          power: data.power || '—',
          accuracy: data.accuracy || '—',
          pp: data.pp,
          damage_class: data.damage_class.name,
          effect:
            data.effect_entries.find((e) => e.language.name === 'en')?.effect ||
            'No description available',
        };

        this.moveDetails.set(moveUrl, moveDetail);
        return moveDetail;
      } catch (error) {
        console.error('Error fetching move details:', error);
        return null;
      }
    },
    async fetchPokemonLore(pokemonId) {
  try {
    const response = await fetch(`https://pokeapi.co/api/v2/pokemon-species/${pokemonId}/`);

    const speciesData = await response.json();

    const flavorTexts = speciesData.flavor_text_entries
      .filter(entry => entry.language.name === 'en') // Filter for English entries
      .map(entry => entry.flavor_text.replace(/[\n\r]/g, ' ')); // Replace newlines and carriage returns with space

    // Join the texts into a single string and remove extra spaces
    const lore = flavorTexts.join(' ').replace(/\s+/g, ' ').trim();

    return lore;
  } catch (error) {
    console.error('Error fetching Pokémon lore:', error);
  }
},

    getTypeColor(type) {
      const colors = {
        normal: 'bg-gray-500',
        fire: 'bg-red-500',
        water: 'bg-blue-500',
        grass: 'bg-green-500',
        electric: 'bg-yellow-500',
        ice: 'bg-cyan-500',
        fighting: 'bg-red-700',
        poison: 'bg-purple-500',
        ground: 'bg-yellow-700',
        flying: 'bg-indigo-400',
        psychic: 'bg-pink-500',
        bug: 'bg-lime-500',
        rock: 'bg-yellow-800',
        ghost: 'bg-purple-700',
        dragon: 'bg-indigo-700',
        dark: 'bg-gray-700',
        steel: 'bg-gray-400',
        fairy: 'bg-pink-400',
      };
      return colors[type] || 'bg-gray-500';
    },

    getStatColor(value) {
      if (value >= 100) return 'bg-green-100 border-green-500';
      if (value >= 80) return 'bg-blue-100 border-blue-500';
      if (value >= 60) return 'bg-yellow-100 border-yellow-500';
      return 'bg-red-100 border-red-500';
    },

    getProgressColor(value) {
      if (value >= 100) return 'bg-green-500';
      if (value >= 80) return 'bg-blue-500';
      if (value >= 60) return 'bg-yellow-500';
      return 'bg-red-500';
    }
  },
};
</script>
