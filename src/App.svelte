<script>
  import { onMount } from 'svelte';
  import axios from 'axios';

  let pokemonList = [];
  let selectedPokemon = null;
  let currentPage = 1;
  let pokemonsPerPage = 20;
  let totalPokemons = 0;

  onMount(async () => {
    await fetchPokemons();
  });

  async function fetchPokemons() {
    const response = await axios.get('https://pokeapi.co/api/v2/pokemon?limit=100');
    pokemonList = response.data.results;
    totalPokemons = pokemonList.length;
  }

  async function selectPokemon(url) {
    const response = await axios.get(url);
    selectedPokemon = response.data;
  }

  $: paginatedPokemonList = pokemonList.slice(
    (currentPage - 1) * pokemonsPerPage,
    currentPage * pokemonsPerPage
  );

  $: totalPages = Math.ceil(totalPokemons / pokemonsPerPage);

  function changePage(increment) {
    currentPage = Math.max(1, Math.min(currentPage + increment, totalPages));
  }
</script>

<main>
  <header>
    <img src="/pokedex.png" alt="Pokédex" class="logo">
  </header>
  <div class="container">
    <div class="pokemon-list">
      <h2>Pokémon List</h2>
      <div class="list-container">
          <button on:click={() => selectPokemon(pokemon.url)}>
            {pokemon.name}
          </button>
        {/each}
      </div>
      <div class="pagination">
        <div class="pagination-buttons">
          <button on:click={() => changePage(-1)} disabled={currentPage === 1}>Previous</button>
          <button on:click={() => changePage(1)} disabled={currentPage === totalPages}>Next</button>
        </div>
        <div class="page-info">
          Page {currentPage} of {totalPages}
        </div>
      </div>
    </div>
    <div class="pokemon-detail">
      <h2>Pokémon Details</h2>
      {#if selectedPokemon}
        <div class="detail-container">
          <h3>{selectedPokemon.name}</h3>
          <img src={selectedPokemon.sprites.front_default} alt={selectedPokemon.name} />
          <div class="stats">
            <p>Height: {selectedPokemon.height / 10}m</p>
            <p>Weight: {selectedPokemon.weight / 10}kg</p>
          </div>
          <h4>Abilities:</h4>
          <ul>
            {#each selectedPokemon.abilities as ability}
              <li>{ability.ability.name}</li>
            {/each}
          </ul>
        </div>
      {:else}
        <p class="select-prompt">Select a Pokémon to view details</p>
      {/if}
    </div>
  </div>
</main>

<style>
  :global(body) {
    font-family: 'Roboto', sans-serif;
    background-color: #fff9c4; /* Light pastel yellow */
    margin: 0;
    padding: 0;
    color: #333;
  }

  main {
    max-width: 1200px;
    margin: 0 auto;
    padding: 20px;
  }

  header {
    text-align: center;
    margin-bottom: 20px;
  }

  .logo {
    max-width: 300px;
    height: auto;
  }

  .container {
    display: flex;
    gap: 20px;
  }

  .pokemon-list, .pokemon-detail {
    background-color: #fffde7; /* Even lighter yellow */
    border-radius: 15px;
    padding: 20px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  }

  .pokemon-list {
    flex: 1;
    background-color: #ffcdd2; /* Light pastel red */
  }

  .pokemon-detail {
    flex: 2;
  }

  h2 {
    text-align: center;
    margin-bottom: 20px;
    color: #333;
  }

  .list-container {
    max-height: 500px;
    overflow-y: auto;
    padding-right: 10px;
  }

  button {
    display: block;
    width: 100%;
    padding: 10px;
    margin-bottom: 10px;
    background-color: #81d4fa; /* Light pastel blue */
    color: #333;
    border: none;
    border-radius: 25px;
    cursor: pointer;
    transition: background-color 0.3s, transform 0.2s;
    text-transform: capitalize;
  }

  button:hover {
    background-color: #4fc3f7;
    transform: translateY(-2px);
  }

  .pagination {
    margin-top: 20px;
    text-align: center;
  }

  .pagination-buttons {
    display: flex;
    justify-content: space-between;
    margin-bottom: 10px;
  }

  .pagination button {
    padding: 5px 15px;
    background-color: #81d4fa;
    color: #333;
    border: none;
    border-radius: 25px;
    cursor: pointer;
    transition: background-color 0.3s;
    font-weight: bold;
  }

  .pagination button:hover {
    background-color: #4fc3f7;
  }

  .pagination button:disabled {
    background-color: #e0e0e0;
    cursor: not-allowed;
  }

  .page-info {
    font-size: 0.9em;
    color: #333;
  }

  .detail-container {
    text-align: center;
  }

  .detail-container h3 {
    font-size: 2em;
    color: #f44336; /* Keeping the Pokédex red for contrast */
    text-transform: capitalize;
    margin-bottom: 10px;
  }

  .detail-container img {
    width: 200px;
    height: 200px;
    margin: 0 auto;
    display: block;
  }

  .stats {
    display: flex;
    justify-content: center;
    gap: 20px;
    margin-top: 10px;
  }

  .stats p {
    font-weight: bold;
    color: #333;
  }

  ul {
    list-style-type: none;
    padding: 0;
    margin-top: 20px;
  }

  li {
    background-color: #a5d6a7; /* Light pastel green */
    color: #333;
    margin-bottom: 5px;
    padding: 5px 10px;
    border-radius: 15px;
    display: inline-block;
    margin-right: 5px;
  }

  .select-prompt {
    text-align: center;
    font-style: italic;
    color: #666;
  }
</style>