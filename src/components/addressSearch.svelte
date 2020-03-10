<script>
  let value = ''
  let searchAddrs = []
  let error = false
  export let view

  function _set(addr) {
    //set value to clicked addr , then clear searchAddrs
    if (addr) value = addr
    searchAddrs = []
    fetch(`https://geosearch.planninglabs.nyc/v1/search?text=${value}&size=5`)
      .then(response => response.json())
      .then(response => {
        //use the first address
        if (response.features.length > 0) {
          const coords = response.features[0].geometry.coordinates
          view.goTo({
            target: coords,
            zoom: 17
          })
        } else {
          //throw error
          error = true
        }
      })
  }

  function _search() {
    if (value.length > 1) {
      fetch(`https://geosearch.planninglabs.nyc/v1/autocomplete?text=${value}`)
        .then(response => response.json())
        .then(
          response =>
            (searchAddrs = response.features
              .map(feature =>
                feature.properties.label.replace(', New York, NY, USA', '')
              )
              .slice(0, 5))
        )
    } else {
      searchAddrs = []
    }
    return true
  }
</script>

<form on:submit|preventDefault="{_set}">
  <div class="field is-horizontal">
    <div class="field-label is-normal">
      <label class="label">Search by Address</label>
    </div>
    <div class="field-body">
      <div class="field">
        <div class="control is-expanded">
          <input
            id="address"
            placeholder="1 Centre Street"
            type="text"
            name="address"
            bind:value
            autocomplete="off"
            on:keyup="{() => {_search(); error = false;}}"
            class="{!error ? 'input is-fullwidth' : 'input is-fullwidth is-danger'}"
          />
        </div>
      </div>
      <button type="submit" class="button">Search</button>
    </div>
  </div>
  {#if error}
  <p class="help is-danger">
    No addresses found
  </p>
  {/if}

  <ul>
    {#each searchAddrs as addr}
    <li on:click="{() => _set(addr)}">{addr}</li>
    {/each}
  </ul>
</form>

<style>
  form {
    margin-bottom: 1rem;
  }
  #address {
    width: 100%;
    padding: 5px 15px;
    box-sizing: border-box;
    margin-bottom: 5px;
  }
  input[type='submit'] {
    background-color: #6a6a6a;
    border: none;
    color: white;
    padding: 5px 10px;
    text-decoration: none;
    cursor: pointer;
  }
  ul {
    padding-left: 10px;
    color: rgb(61, 61, 61);
    margin: 0px !important;
  }
  li {
    margin-left: 5px;
  }
</style>
