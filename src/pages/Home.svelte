<script>
    import RadiusInput from '../components/RadiusInput.svelte';

    let radius = 5; // RadiusInput currently returns string, need to type case to integer when using
    let location_string = '';
    let zip = null;
    let readyToSubmit = false; // set to true when have a location AND radius? or use default radius?

    const handlebtn = () => {
        console.log(typeof(radius));
    }
    

    const showPosition = (position) => {
        location_string =  "Latitude: "+position.coords.latitude+"<br>Longitude: "+ position.coords.longitude;
    }

    const getLocation = () => {
        if(navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(showPosition);
        } else {
            location_string = "Geolocation is not supported by this browser";
        }
    }
    

</script>

<style>

</style>
 
<h1>Random Restaurant</h1>

<div class="row">
    <div class="col s6">
        <div class="input-field">
            <label for="zip-code">Zip code</label>
            <input type="text" bind:value={zip}/>
        </div>
        <button class="waves-effect waves-light btn" on:click={getLocation}>
            <i class="material-icons left">location_on</i>
            Use my current location
        </button>
        <p>{@html location_string}</p>
    </div>
    <div class="col s6">
        <RadiusInput bind:radius={radius}/>
        <button class="btn" on:click={handlebtn}>CLICK</button>
    </div>
</div>
<div class="row">
    <div class="col s6">
        {#if readyToSubmit}
            <button class="waves-effect waves-light pulse btn">
                <i class="material-icons left">restaurant</i>
                Get restaurant
            </button>
        {:else}
            <button class="btn disabled">
                <i class="material-icons left">restaurant</i>
                Get restaurant
            </button>
        {/if}
    </div>
</div>