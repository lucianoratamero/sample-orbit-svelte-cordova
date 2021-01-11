<script>
  import { onMount } from "svelte";

  import activateDb from "./db/activateDb";
  import MoonRepository from "./db/repositories/MoonRepository";
  import PlanetRepository from "./db/repositories/PlanetRepository";

  let planets, moons;

  onMount(async () => {
    await activateDb();
    planets = await PlanetRepository.findRecords();
    moons = await MoonRepository.findRecords();
  });

  async function saveNewPlanet(event) {
    const formData = new FormData(event.target);
    await PlanetRepository.createRecord(formData);
    planets = await PlanetRepository.findRecords();
  }

  async function deletePlanetById(id) {
    await PlanetRepository.removeRecordById(id);
    moons = await MoonRepository.findRecords();
    planets = await PlanetRepository.findRecords();
  }
</script>

<h1>Welcome to Orbit/Cordova/Svelte sample app!</h1>

<form on:submit|preventDefault={saveNewPlanet}>
  <label for="name">Planet name:</label>
  <input type="text" name="name" required />

  <label for="classification">Classification:</label>
  <input type="text" name="classification" required />

  <label for="atmosphere">
    Does it have an atmosphere?
    <input id="atmosphere" type="checkbox" name="atmosphere" value="true" />
  </label>

  <button>Save</button>
</form>

{#if planets}
  <h2>Planets</h2>
  {#each planets as planet}
    <dl>
      <dt>ID:</dt>
      <dd>{planet.id}</dd>

      <dt>Name:</dt>
      <dd>{planet.attributes.name}</dd>

      <dt>Classification:</dt>
      <dd>{planet.attributes.classification}</dd>

      <dt>Has atmosphere:</dt>
      <dd>{planet.attributes.atmosphere ? "yes" : "no"}</dd>

      <button on:click={() => deletePlanetById(planet.id)}>Delete</button>
    </dl>
  {/each}
{/if}

{#if moons}
  <h2>Moons</h2>
  {#each moons as moon}
    <dl>
      <dt>ID:</dt>
      <dd>{moon.id}</dd>

      <dt>Name:</dt>
      <dd>{moon.attributes.name}</dd>

      <dt>Planet:</dt>
      <dd>
        {planets.filter(
          (planet) => planet.id === moon.relationships.planet.data.id
        )[0].attributes.name}
      </dd>
    </dl>
  {/each}
{/if}

<style>
  h1 {
    color: purple;
  }
  dl {
    padding: 0.8rem;
    border: 1px solid;
  }
  dd {
    margin-bottom: 0.4rem;
  }
</style>
