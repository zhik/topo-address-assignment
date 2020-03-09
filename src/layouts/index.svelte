<script>
  import MapSearch from '../components/mapSearch.svelte'
  import AddressSearch from '../components/addressSearch.svelte'
  import BBLSearch from '../components/bblSearch.svelte'

  let view
</script>

<main>
  <h3 class="title is-3">Manhattan Topographical Bureau</h3>
  <h6 class="subtitle is-6">Address assignment statuses</h6>

  <div id="map">
    <p class="has-background-light">
      Search by an address or BBL, then click on a lot to view more details
    </p>
    <AddressSearch {view}></AddressSearch>
    <hr />
    <BBLSearch {view}></BBLSearch>
    <MapSearch bind:view="{view}"></MapSearch>
  </div>
</main>

<style>
  #map {
    max-width: 1000px;
    height: 500px;
  }
</style>
