<template>
  <div>
    <div class="header">
      <h2 class="heading">POKEMON API</h2>
      <input
        class="heading__input"
        type="text"
        placeholder="Search some Pokemon"
        v-model="searchQuery"
        @input="filterPokemon"
      />
    </div>
    <div class="body">
      <div
        class="card"
        v-for="pokemon in filteredPokemon"
        :key="pokemon.name"
      >
        <div class="card__id">#{{ pokemon.id }}</div>
        <img class="cart__img" :src="pokemon.img" alt="Pokemon image" />
        <div class="cart__infor">
          <h3 class="name">{{ pokemon.name }}</h3>
          <div class="cart__infor--attr">
            <span
              v-for="type in pokemon.types"
              :key="type"
              :style="{ backgroundColor: typeColors[type] || '#ccc' }"
            >
              {{ type }}
            </span>
          </div>
        </div>
      </div>
    </div>
    <div class="loading-screen" v-if="loading">
      <p>
        Loading<span class="dot">.</span><span class="dot">.</span><span
          class="dot"
          >.</span
        >
      </p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pokemonList: [], // Danh sách Pokémon từ API
      filteredPokemon: [], // Pokémon đã được lọc theo từ khóa tìm kiếm
      searchQuery: "", // Chuỗi tìm kiếm
      loading: false, // Trạng thái loading
      typeColors: {
        poison: "#a33da2",
        grass: "#78cd56",
        fire: "#ff411c",
        flying: "#a98ff0",
        bug: "#78cd56",
        water: "#6375fd",
        normal: "",
      },
    };
  },
  methods: {
    async fetchData(url) {
      this.loading = true;
      try {
        const response = await fetch(url);
        if (!response.ok) throw new Error("Failed to fetch data");
        return await response.json();
      } catch (error) {
        console.error("Fetch error:", error);
        alert("Failed to fetch Pokémon data. Please try again later.");
        return null;
      } finally {
        this.loading = false;
      }
    },
    async fetchPokemonDetails(url) {
      const data = await this.fetchData(url);
      if (data) {
        return {
          id: data.id,
          name: data.name,
          img: data.sprites.front_default,
          types: data.types.map((t) => t.type.name),
        };
      }
      return null;
    },
    async loadPokemon() {
      const apiUrl = "https://pokeapi.co/api/v2/pokemon";
      const data = await this.fetchData(apiUrl);
      if (data && data.results) {
        const pokemonDetails = await Promise.all(
          data.results.map((pokemon) => this.fetchPokemonDetails(pokemon.url))
        );
        this.pokemonList = pokemonDetails.filter((pokemon) => pokemon !== null);
        this.filteredPokemon = this.pokemonList;
      }
    },
    filterPokemon() {
      const query = this.searchQuery.toLowerCase();
      this.filteredPokemon = this.pokemonList.filter((pokemon) =>
        pokemon.name.toLowerCase().includes(query)
      );
    },
  },
  mounted() {
    this.loadPokemon();
  },
};
</script>

<style>
body {
  width: 1200px;
  font-size: 20px;
  margin: 20px auto;
  font-family: "Open Sans", serif;
}

.header {
  text-align: center;
  margin-top: 60px;
}
.heading {
  font-size: 48px;
  color: #333;
  font-weight: 600;
}
.heading__input {
  min-height: 55px;
  min-width: 650px;
  border-radius: 40px;
  padding-left: 20px;
  font-size: 18px;
  margin: 20px 0 32px 0;
}
.body {
  margin-top: 32px;
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  height: 100%;
}
.card {
  display: inline-flex;
  flex-direction: column;

  align-items: center;
  width: 15.5%;
  border: 1px solid #ccc;
  border-radius: 20px;
  box-shadow: 5px 5px 2px rgba(0, 0, 0, 0.05);
  text-align: center;
  transition: all ease 0.3s;
  cursor: pointer;
}
.card:hover {
  transform: translateY(-8px);
}
.card__id {
  color: #333;
  margin-top: 10px;
}
.cart__img {
  margin-top: 10px;
  width: 150px;
  height: 165px;
}
.name {
  margin-bottom: 0;
  font-size: 20px;
  margin-top: 15px;
}
.cart__infor--attr {
  margin: 5px 0 15px 0;
  font-weight: 600;
  display: flex;
  justify-content: center;
}
.cart__infor--attr span {
  font-size: 16px;
  margin: 0 5px 5px 5px;
  padding: 5px;
  border-radius: 5px;
  text-transform: capitalize;
}
.cart__img.loading,
.card__id.loading,
.name.loading,
.cart__infor--attr.loading {
  height: 25px;
  width: 160px;
  border-radius: 5px;
  margin-top: 10px;
}
.cart__infor--attr.loading {
  height: 35px;
}
.cart__img.loading {
  height: 180px;
}
.loading {
  background-color: #e2e5e7;
  background-image: linear-gradient(
    90deg,
    rgba(255, 255, 255, 0),
    rgba(255, 255, 255, 0.5),
    rgba(255, 255, 255, 0)
  );
  background-size: 40px 100%;
  background-repeat: no-repeat;
  background-position: left -40px top 0;
  animation: shine 1s ease infinite;
}

@keyframes shine {
  to {
    background-position: right -40px top 0;
  }
}
.loading-screen {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, 0.8);
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 24px;
  font-family: "Roboto", sans-serif;
  color: #333;
  z-index: 1000;
}

.loading-screen.show {
  display: flex; 
}

.dot {
  animation: dots 1.5s infinite ease-in-out;
}


.dot:nth-child(1) {
  animation-delay: 0s;
}

.dot:nth-child(2) {
  animation-delay: 0.2s;
}

.dot:nth-child(3) {
  animation-delay: 0.4s;
}

@keyframes dots {
  0% {
    opacity: 0;
  }
  50% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
}
</style>
