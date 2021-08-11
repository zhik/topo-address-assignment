<script>
  let block = "";
  let lot = "";
  let error = false;

  export let view;
  export let plutoLayer;

  function _set(addr) {
    fetch(
      `https://cors-e.herokuapp.com/https://api.cityofnewyork.us/geoclient/v1/bbl.json?borough=manhattan&block=${block}&lot=${lot}&app_id=d2a49e32&app_key=c8c857bdc19eb8829bf9fa5a920df18c`
    )
      .then((response) => response.json())
      .then((response) => {
        if ("latitudeInternalLabel" in response.bbl) {
          const { latitudeInternalLabel, longitudeInternalLabel } =
            response.bbl;
          const coords = [longitudeInternalLabel, latitudeInternalLabel];
          view.goTo({
            target: coords,
            zoom: 17,
          });

          //search plutoLayer
          const point = {
            x: coords[0],
            y: coords[1],
            spatialReference: {
              wkid: 4326,
            },
            type: "point",
          };
          console.log(point);
          plutoLayer
            .queryFeatures({
              //query object
              geometry: point,
              spatialRelationship: "intersects",
              returnGeometry: false,
              outFields: ["BBL"],
              distance: 10,
              units: "feet",
            })
            .then((featureSet) => {
              console.log(featureSet.features);
              // open popup of query result
              view.popup.open({
                location: point,
                features: featureSet.features
              });
            });
        } else {
          //throw error
          error = true;
        }
      });
  }
</script>

<form on:submit|preventDefault={_set}>
  <div class="field is-horizontal">
    <div class="field-label is-normal">
      <label class="label">Search by BBL</label>
    </div>
    <div class="field-body">
      <div class="field">
        <div class="control">
          <input
            id="block"
            placeholder="Block"
            type="text"
            pattern="\d*"
            name="block"
            bind:value={block}
            on:keyup={() => (error = false)}
            autocomplete="off"
            class={!error
              ? "input is-fullwidth"
              : "input is-fullwidth is-danger"}
            maxlength="5"
          />
        </div>
      </div>
      <div class="field">
        <div class="control">
          <input
            id="lot"
            placeholder="Lot"
            type="text"
            pattern="\d*"
            name="lot"
            bind:value={lot}
            on:keyup={() => (error = false)}
            autocomplete="off"
            class={!error
              ? "input is-fullwidth"
              : "input is-fullwidth is-danger"}
            maxlength="4"
          />
        </div>
      </div>
      <button type="submit" class="button">Search</button>
    </div>
  </div>
  {#if error}
    <p class="help is-danger">Block and/or Lot is invalid</p>
  {/if}
</form>

<style>
  form {
    margin-bottom: 1rem;
  }
  input[type="submit"] {
    background-color: #6a6a6a;
    border: none;
    color: white;
    padding: 5px 10px;
    text-decoration: none;
    cursor: pointer;
  }
</style>
