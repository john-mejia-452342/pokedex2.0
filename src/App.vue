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
              <div class="container-imagen-poke">
                <div class="imagenpoke">
                  <img :src="pokemonSeleccionado ? pokemonSeleccionado.imagen : ''" alt="Imagen del Pokémon">
                </div>
              </div>
            </div>
            <p>{{ pokemonSeleccionado ? capitalizeFirstLetter(pokemonSeleccionado.nombre) : '' }}</p>

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
            <div class="container-skills">
              <div class="skill">
                <p><span>HP</span>{{pokemonSeleccionado ? pokemonSeleccionado.hp :  ''}}</p>
                <div class="progress" :style="{ '--wth': pokemonSeleccionado ? pokemonSeleccionado.hp + 'px' : 0 }"></div>
              </div>
              <div class="skill">
                <p><span>Attack</span>{{pokemonSeleccionado ? pokemonSeleccionado.attack : ''}}</p>
                <div class="progress" :style="{ '--wth': pokemonSeleccionado ? pokemonSeleccionado.attack + 'px' : 0 }"></div>
              </div>
              <div class="skill">
                <p><span>Defense</span>{{pokemonSeleccionado ? pokemonSeleccionado.defense : ''}}</p>
                <div class="progress" :style="{ '--wth': pokemonSeleccionado ? pokemonSeleccionado.defense  + 'px' : 0 }"></div>
              </div>
              <div class="skill">
                <p><span>Special Attack</span>{{pokemonSeleccionado ? pokemonSeleccionado.special_attack   : ''}}</p>
                <div class="progress" :style="{ '--wth': pokemonSeleccionado ? pokemonSeleccionado.special_attack + 'px': 0 }"></div>
              </div>
              <div class="skill">
                <p><span>Special Defense</span>{{pokemonSeleccionado ? pokemonSeleccionado.special_defense  : ''}}</p>
                <div class="progress" :style="{ '--wth': pokemonSeleccionado ? pokemonSeleccionado.special_defense  + 'px' : 0 }"></div>
              </div>
              <div class="skill">
                <p><span>Speed</span>{{pokemonSeleccionado ? pokemonSeleccionado.speed : ''}}</p>
                <div class="progress" :style="{ '--wth': pokemonSeleccionado ? pokemonSeleccionado.speed  + 'px': 0 }"></div>
              </div>
            </div>
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
    <div class="sidebar" v-if="mostrarSidebar">
      <div class="checkbox-container">
        <button class="btn-cerrar-sidebar" @click="mostrarSidebar = false">
          <i class="fa-solid fa-times"></i>
        </button>
        <label v-for="(tipo, index) in tiposDisponibles" :key="index">
          <input type="checkbox" :value="tipo" v-model="tiposSeleccionados">
          {{ capitalizeFirstLetter(tipo) }}
          <i :class="['fa-solid', getTipoIcono(tipo)]"></i>
        </label>
      </div>
    </div>
    <div class="busquedas">
      <div class="filtro">
        <button type="button" class="btn btn-info" @click="funcionMostrarSidebar()">
          <i class="fa-solid fa-filter"></i> Filtrar
        </button>
      </div>
      <div class="input-pokemon">
        <input type="text" placeholder="Escribe el nombre o número del Pokémon" v-model="busqueda">
        <button type="button" class="btn btn-primary"><i class="fa-solid fa-magnifying-glass"  @click="filtrarPokemons()"></i></button>
      </div>
    </div>
    <div class="pikas">
      <img :src="pika" alt="">
      <img :src="pika" alt="" class="pika-der">
    </div>
    <div class="container-cards">
      <div class="card" v-for="(pokemon, index) in filteredPokemons" :key="index">
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
    <button type="button" class="btn btn-warning" @click="mostrarmas()">Show More</button>
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

let busqueda = ref("");
let filteredPokemons = ref([]);
let pokemons = ref([]);
let pokemonSeleccionado = ref(null); 
let tiposSeleccionados = ref([])
let mostrarSidebar = ref(false);
let offset = ref(0);

console.log(tiposSeleccionados);
console.log(busqueda);

//Obtener info de pokemons
async function obtenerPokemon(url) {
  try {
    let r = await axios.get(url);

    const tipos = ref([]);
    const debilidades = ref([]);

    // Obtener los tipos del Pokémon y almacenarlos en un conjunto (Set)
    const tiposSet = new Set(r.data.types.map(tipo => tipo.type.name.toLowerCase()));

    for (let i = 0; i < r.data.types.length; i++) {
      const tipoActual = r.data.types[i].type;

      tipos.value.push(tipoActual);

      const urldebi = tipoActual.url;
      const debilidad = await axios.get(urldebi);

      for (let j = 0; j < debilidad.data.damage_relations.double_damage_from.length; j++) {
        const debilidadActual = debilidad.data.damage_relations.double_damage_from[j].name.toLowerCase();

        // Verificar si la debilidad no es del mismo tipo que el Pokémon
        if (!tiposSet.has(debilidadActual) && !debilidades.value.includes(debilidadActual)) {
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
// Clases por tipo y icono 
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
//Tipos disponibles
const tiposDisponibles = [
  'water', 'fire', 'grass', 'electric', 'ice', 'fighting', 'poison', 'ground',
  'flying', 'psychic', 'bug', 'rock', 'ghost', 'dragon', 'dark', 'steel', 'fairy', 'normal'
];
//Iconos
function getTipoIcono(tipo) {
  const iconosPorTipo = {
    water: "fa-droplet",
    fire: "fa-fire",
    grass: "fa-leaf",
    electric: "fa-bolt",
    ice: "fa-snowflake",
    fighting: "fa-fist-raised",
    poison: "fa-skull-crossbones",
    ground: "fa-mountain",
    flying: "fa-dove",
    psychic: "fa-brain",
    bug: "fa-bug",
    rock: "fa-gem",
    ghost: "fa-ghost",
    dragon: "fa-dragon",
    dark: "fa-moon",
    steel: "fa-shield",
    fairy: "fa-magic",
    normal: "fa-circle",
  };

  return iconosPorTipo[tipo] || "fa-question";
}

//Acceder a la API 50 primeros y demas pokemones
async function obtenerPokemons() {
  try {
    let pokemonsResponse = await axios.get(`https://pokeapi.co/api/v2/pokemon?limit=50&offset=${offset.value}`);
    let pokemonUrls = pokemonsResponse.data.results.map((pokemon) => pokemon.url);

    for (const url of pokemonUrls) {
      await obtenerPokemon(url);
    }
  } catch (error) {
    console.error("Error al obtener los datos de los Pokémon:", error);
  }
}

//Primera letra en Mayuscula
function capitalizeFirstLetter(str) {
  return str ? str.charAt(0).toUpperCase() + str.slice(1) : "";
}

//Selecionar un pokemon para el modal
async function seleccionarPokemon(pokemon) {
  pokemonSeleccionado.value = pokemon;
}

//Filtrar pokemon por nombre o numero
async function filtrarPokemons() {
  const textoBusqueda = busqueda.value.toLowerCase();

  if (textoBusqueda === '') {
    // Si el campo de búsqueda está vacío, muestra todos los Pokémon.
    filteredPokemons.value = pokemons.value;
  } else {
    try {
      const r = await axios.get(`https://pokeapi.co/api/v2/pokemon/${textoBusqueda}`);
      const tipos = ref([]);
      const debilidades = ref([])

      const tiposSet = new Set(r.data.types.map(tipo => tipo.type.name.toLowerCase()));

        for (let i = 0; i < r.data.types.length; i++) {
          const tipoActual = r.data.types[i].type;

          tipos.value.push(tipoActual);

          const urldebi = tipoActual.url;
          const debilidad = await axios.get(urldebi);

          for (let j = 0; j < debilidad.data.damage_relations.double_damage_from.length; j++) {
            const debilidadActual = debilidad.data.damage_relations.double_damage_from[j].name.toLowerCase();

            // Verificar si la debilidad no es del mismo tipo que el Pokémon
            if (!tiposSet.has(debilidadActual) && !debilidades.value.includes(debilidadActual)) {
              debilidades.value.push(debilidadActual);
            }
          }
        }

      // Verifica si la respuesta contiene un Pokémon y agrégalo a la lista de filtrados.
      if (r) {
        console.log(r.data);
        filteredPokemons.value = [{
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
        }];
      } else {
        filteredPokemons.value = [];
      }
    } catch (error) {
      console.error("Error al buscar Pokémon:", error);
      filteredPokemons.value = [];
    }
  }
}

//Mostrar Sidebar
async function funcionMostrarSidebar() {
  mostrarSidebar.value = true; 
}

//Mostrar mas
function mostrarmas() {
  offset.value += 50;
  obtenerPokemons();
}
//Montar tan pronto se cargue
onMounted(async () => {
  await obtenerPokemons();
  filtrarPokemons(); 
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
.container-imagen-poke{
  height: 270px;
  width: 270px;
  background-color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  border: 1px solid grey;
  -webkit-box-shadow: 2px 2px 10px -3px rgba(0,0,0,0.57);
  -moz-box-shadow: 2px 2px 10px -3px rgba(0,0,0,0.57);
  box-shadow: 2px 2px 10px -3px rgba(0,0,0,0.57);
}
.imagenpoke{
  background-color: rgba(128, 128, 128, 0.088);
  height: 250px;
  width: 250px;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 10;
}
.imagenpoke img{
  width: 100%;
  height: 100%;
}

.container-skills{
  position: relative;
  width: 340px;
  background: rgba(0, 0, 0, .2);
  padding: 0 20px;
}

.container-skills .skill{
  margin: 20px 0;
}

.container-skills .skill p{
  width: 300px;
  display: flex;
  justify-content: space-between;
}

.container-skills .skill .progress{
  position: relative;
  width: 100%;
  height: 6px;
  background: #999;
  border-radius:6px ;
  overflow: hidden;
  margin: 5px 0;
}

.container-skills .skill .progress::before{
  content:'' ;
  position: absolute;
  width: var(--wth, 0px); 
  height: 100%;
  background: #0280fe;
}

.sidebar{
  position: fixed;
  top: 0;
  left: 5px;
  background-color: rgba(0, 0, 0, 0.297);
  width: 15%;
  height: 100vh;
  z-index: 3000;
}
.checkbox-container{
  display: flex;
  flex-direction: column;
  position: relative;
}
.btn-cerrar-sidebar{
  position: absolute;
  right: 3px;
  top: 3px;
}
</style>