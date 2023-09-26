<template>
  <div class="container">
    <!-- Modal -->
    <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
      <div class="modal-dialog modal-xl">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="exampleModalLabel">Detalles</h1>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            <div class="container-modal-content">
              <p>Nombre: {{ pokemonSeleccionado ? capitalizeFirstLetter(pokemonSeleccionado.nombre) : '' }}</p>
              <img :src="pokemonSeleccionado ? pokemonSeleccionado.imagen : ''" alt="Imagen del Pokémon">
            </div>
            <div class="container-debi-type">
              <div v-if="pokemonSeleccionado">
                <!-- Tipo -->
                <h1>Tipo</h1>
                <div v-for="(tipo, i) in pokemonSeleccionado.tipos" :key="i">
                  <button :class="['btn-tipo', getTipoClase(tipo.name).clase]">
                    <i :class="[getTipoClase(tipo.name).icono]"></i>
                    {{ capitalizeFirstLetter(tipo.name) }}
                  </button>
                </div>
                <!-- Debilidades -->
                <h1>Debilidades</h1>
                <div v-for="(tipo, i) in pokemonSeleccionado.debilidades" :key="i">
                  <button :class="['btn-tipo', getTipoClase(tipo).clase]">
                    <i :class="[getTipoClase(tipo).icono]"></i>
                    {{ capitalizeFirstLetter(tipo) }}
                  </button>
                </div>
              </div>
            </div>
            <section class="habi">
              <h2>Stast</h2>
              <h4>Hp:</h4>
              <div class="contai">
                <div class="porcen Hp">{{pokemonSeleccionado ? pokemonSeleccionado.hp : ''}}</div>
              </div>
              <h4>Attack:</h4>
              <div class="contai">
                <div class="porcen Hp">{{pokemonSeleccionado ? pokemonSeleccionado.attack : ''}}</div>
              </div>
              <h4>Defense:</h4>
              <div class="contai">
                <div class="porcen Hp">{{pokemonSeleccionado ? pokemonSeleccionado.defense : ''}}</div>
              </div>
              <h4>Special Attack:</h4>
              <div class="contai">
                <div class="porcen Hp">{{pokemonSeleccionado ? pokemonSeleccionado.special_attack : ''}}</div>
              </div>
              <h4>Special defense:</h4>
              <div class="contai">
                <div class="porcen Hp">{{pokemonSeleccionado ? pokemonSeleccionado.special_defense : ''}}</div>
              </div>
              <h4>Speed:</h4>
              <div class="contai">
                <div class="porcen Hp">{{pokemonSeleccionado ? pokemonSeleccionado.speed : ''}}</div>
              </div>
            </section>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
          </div>
        </div>
      </div>
    </div>
    <!-- Pokedex -->
    <header>
      <div class="titulo">
        <img :src="pokeball" alt="" class="pokeball-der">
        <img :src="pokedex" alt="">
        <img :src="pokeball" alt="" class="pokeball-izq">
      </div>
    </header>
    <div class="busquedas">
      <div class="filtro">
        <button type="button" class="btn btn-info"><i class="fa-solid fa-filter"></i>Filtrar</button>
      </div>
      <div class="input-pokemon">
        <input type="text" placeholder="Escribe el nombre del pokemon">
        <button type="button" class="btn btn-primary"><i class="fa-solid fa-magnifying-glass"></i></button>
      </div>
    </div>
    <div class="pikas">
      <img :src="pika" alt="">
      <img :src="pika" alt="" class="pika-der">
    </div>
    <div class="container-cards">
      <div class="card" v-for="(pokemon, index) in pokemons" :key="index">
        <div class="imagen-fondo">
          <button class="btn-poke" @click="seleccionarPokemon(pokemon)" data-bs-toggle="modal" data-bs-target="#exampleModal"><img :src="pokemon.imagen" alt=""></button>
        </div>
        <div class="info">
          <h2>N° {{ pokemon.numero }}</h2>
          <h1>{{ capitalizeFirstLetter(pokemon.nombre) }}</h1>
        </div>
        <div class="container-tipos">
          <div v-for="(item, i) in pokemon.tipos" :key="i">
            <button :class="['btn-tipo', getTipoClase(pokemon.tipos[i].name).clase]">
              <i :class="[getTipoClase(pokemon.tipos[i].name).icono]"></i>
              {{ capitalizeFirstLetter(item.name) }}
            </button>
          </div>
        </div>
      </div>
    </div>
    <button type="button" class="btn btn-warning">Show More</button>
    <footer>
    </footer>
  </div>
</template>

<script setup>
import axios from 'axios'
import { ref, onMounted } from 'vue';
import pokeball from '/src/assets/pokeball.png'
import pika from '/src/assets/pika.png'
import pokedex from '/src/assets/Pokédex.png'


let pokemons = ref([]);
async function obtenerPokemon(url) {
  try {
    let r = await axios.get(url);
    console.log(r);

    const tipos = ref([]);
    const debilidades = ref([])

    for (let i = 0; i < r.data.types.length; i++) {
      const tipoActual = r.data.types[i].type;
      const tipopoke = r.data.types[i].type.name.toLowerCase(); // Convertir a minúsculas

      // Agregar el tipo al arreglo de tipos sin importar si ya está presente
      tipos.value.push(tipoActual);

      let urldebi = tipoActual.url; // Usar la URL del tipo directamente

      let debilidad = await axios.get(urldebi);

      for (let j = 0; j < debilidad.data.damage_relations.double_damage_from.length; j++) {
        const debilidadActual = debilidad.data.damage_relations.double_damage_from[j].name.toLowerCase(); // Convertir a minúsculas

        // Evitar agregar la debilidad si coincide con uno de los tipos del Pokémon
        if (debilidadActual !== tipopoke && !debilidades.value.includes(debilidadActual)) {
          debilidades.value.push(debilidadActual);
        }
      }
    }
    pokemons.value.push({
      imagen: r.data.sprites.other['official-artwork'].front_default,
      nombre: r.data.name,
      numero: r.data.id,
      tipos: tipos,
      debilidades: debilidades,
      hp: r.data.stats[0].base_stat,
      attack: r.data.stats[1].base_stat,
      defense: r.data.stats[2].base_stat,
      special_attack: r.data.stats[3].base_stat,
      special_defense: r.data.stats[4].base_stat,
      speed: r.data.stats[5].base_stat,
    });
  } catch (error) {
    console.error("Error al obtener los datos de un Pokémon:", error);
  }
}


function getTipoClase(tipo) {
  const clasesPorTipo = {
    water: { clase: "tipo-agua", icono: "fa-solid fa-droplet" },
    fire: { clase: "tipo-fuego", icono: "fa-solid fa-fire" },
    grass: { clase: "tipo-planta", icono: "fa-solid fa-leaf" },
    electric: { clase: "tipo-electrico", icono: "fa-solid fa-bolt" },
    ice: { clase: "tipo-hielo", icono: "fa-solid fa-snowflake" },
    fighting: { clase: "tipo-lucha", icono: "fa-solid fa-fist-raised" },
    poison: { clase: "tipo-veneno", icono: "fa-solid fa-skull-crossbones" },
    ground: { clase: "tipo-tierra", icono: "fa-solid fa-mountain" },
    flying: { clase: "tipo-volador", icono: "fa-solid fa-dove" },
    psychic: { clase: "tipo-psiquico", icono: "fa-solid fa-brain" },
    bug: { clase: "tipo-bicho", icono: "fa-solid fa-bug" },
    rock: { clase: "tipo-roca", icono: "fa-solid fa-gem" },
    ghost: { clase: "tipo-fantasma", icono: "fa-solid fa-ghost" },
    dragon: { clase: "tipo-dragon", icono: "fa-solid fa-dragon" },
    dark: { clase: "tipo-siniestro", icono: "fa-solid fa-moon" },
    steel: { clase: "tipo-acero", icono: "fa-solid fa-shield" },
    fairy: { clase: "tipo-hada", icono: "fa-solid fa-magic" },
    normal: { clase: "tipo-normal", icono: "fa-solid fa-circle" },
  };

  return clasesPorTipo[tipo] || "tipo-desconocido";
}

async function obtenerPokemons() {
  let pokemonsResponse = await axios.get("https://pokeapi.co/api/v2/pokemon?limit=50&offset=0");
  let pokemonUrls = pokemonsResponse.data.results.map((pokemon) => pokemon.url);

  for (const url of pokemonUrls) {
    await obtenerPokemon(url);
  }
}

function capitalizeFirstLetter(str) {
  return str ? str.charAt(0).toUpperCase() + str.slice(1) : "";
}

let pokemonSeleccionado = ref(null); 

function seleccionarPokemon(pokemon) {
  pokemonSeleccionado.value = pokemon; 
}

onMounted(() => {
  obtenerPokemons();
});
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Martian+Mono:wght@400;500;600&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Dela+Gothic+One&display=swap');
.container{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  align-content: center;
  width: 100%;
}

header{
  width: 100%;
  height: 200px;
  display: flex;
  align-items: center;
}

.filtro button{
  border: none;
  height: 50px;
  width: auto;
  font-size: 20px;
  display: flex;
  align-items: center;
  gap: 4px;
}
.titulo{
  display: flex;
  width: 100%;
  align-items: center;
  justify-content: space-evenly;
}
.pokeball-izq{
  height: 100px;
  width: 100px;
  transform: scaleX(-1)
}
.pokeball-der{
  height: 100px;
  width: 100px;
}
.input-pokemon{
  display: flex;
  justify-content: flex-end;
}
.input-pokemon input{
  width: 250px;
  border-top-left-radius: 5px;
  border-bottom-left-radius: 5px;
  border: 1 px solid blue;
}
.input-pokemon button{
  border-top-left-radius: 0px;
  border-bottom-left-radius: 0px;
}
.busquedas{
  display: flex;
  width: 50%;
  justify-content: space-evenly;
}
.pikas{
  height: 120px;
  width: 100%;
  display: flex;
  justify-content:space-between ;
}
.pikas img{
  height: 100%;

}
.pika-der{
  transform: scaleX(-1)
}

.container-cards{
  display: flex;
  justify-content: center;
  align-items: flex-start;
  flex-wrap: wrap;
  background-color: white;
  width: 100%;
}
.btn-poke {
  background: none;
  border: none;
  width: 100%;
}
.btn-poke img{
  height: 100%;
  width: 100%;
  background-color: rgba(128, 128, 128, 0.088);
  border-radius: 10px;
}
.card{
  width: 300px;
  height: 460px;
  margin: 10px;
  border: none ;
}
.info{
  text-align: center;
  font-family: 'Dela Gothic One', cursive;
}
.info h2{
  margin: 8px;
  font-size: 20px;

}
.info h1{
  margin: 8px;
  font-size: 25px;
}
.container-tipos{
  display: flex;
  width: 100%;
  justify-content: space-evenly;
  flex-wrap: wrap;

}

.btn-tipo{
  border: none;
  height: 40px;
  width: 100px;
  display: flex;
  justify-content: space-around;
  align-items: center;
  border-radius: 10px;
  margin: 10px;
}
.tipo-agua{
  background-color: hsla(240, 100%, 50%, 0.501);
}
.tipo-fuego{
  background-color: rgba(255, 166, 0, 0.497);
}
.tipo-planta{
  background-color: rgba(0, 128, 0, 0.504);
}
.tipo-electrico{
  background-color: rgba(255, 255, 0, 0.486);
}
.tipo-hielo{
  background-color: rgba(0, 255, 255, 0.492);
}
.tipo-lucha{
  background-color: rgba(255, 0, 0, 0.504);
}
.tipo-planta{
  background-color: rgba(0, 128, 0, 0.504);
}
.tipo-veneno{
  background-color: rgba(128, 0, 128, 0.475);
}
.tipo-tierra{
  background-color: rgba(165, 42, 42, 0.505);
}
.tipo-volador{
  background-color: rgba(250, 221, 162, 0.46);
}
.tipo-psiquico{
  background-color: rgba(238, 120, 140, 0.481);
}
.tipo-bicho{
  background-color: rgba(122, 165, 42, 0.505);
}
.tipo-roca{
  background-color: rgba(128, 128, 128, 0.533);
}
.tipo-fantasma{
  background-color: rgba(95, 13, 95, 0.477);
}
.tipo-dragon{
  background-color: rgba(71, 5, 5, 0.505);
}
.tipo-siniestro{
  background-color: rgba(0, 0, 0, 0.455);
}
.tipo-acero{
  background-color: rgba(85, 107, 47, 0.453);
}
.tipo-hada{
  background-color: rgba(165, 42, 42, 0.505);
}
.tipo-normal{
  background-color: rgba(97, 97, 238, 0.456);
}
.btn-warning{
  margin: 10px;
}

.habi{
  width: 600px;
  margin: auto;
  padding: 10px;
}

.contai{
  width: 100%;
  background-color: #888;
  border-radius: 4rem;
}

.porcen{
  text-align: right;
  padding: 10px;
  color: white;
  font-weight: 900;
}

.Hp{
  background-color: grenn;
  animation: hp 2s forwards;
  border-radius:4rem ;
}
@keyframes hp{
  0%{
    width: 0;
  }
  100%{
    width: 90%;
  }
}
</style>