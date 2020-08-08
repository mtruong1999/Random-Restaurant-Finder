<script>
    import RadiusInput from '../components/RadiusInput.svelte';
    
    let radius = 5; // RadiusInput currently returns string, need to type case to integer when using
    let location_string = '';
    let zip = null;
    let readyToSubmit = false; // set to true when have a location AND radius? or use default radius?
    let locationFetched = false;
    let loading = false;
    let locationFound = false;

    let long = null;
    let lat = null;
    let restaurant = null;

    const apiKey = __myapp.env.API_KEY;

    const yelpApiBaseUrl = 'https://api.yelp.com/v3/businesses/search';
    const corsAnywhereUrl = 'https://cors-anywhere.herokuapp.com/';
    const mpm = 1609; // meters per mile
    

    const showPosition = (position) => {
        location_string =  "Latitude: "+position.coords.latitude+"<br>Longitude: "+ position.coords.longitude;
        lat = position.coords.latitude;
        long = position.coords.longitude;
        readyToSubmit = true;
        locationFetched = true;
    }

    const getLocation = () => {
        if(navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(showPosition);
        } else {
            location_string = "Geolocation is not supported by this browser";
        }
    }
    const handleZip = () => {
        if(zip == '')
        {
            readyToSubmit = false;
        } else if(!isNaN(zip))
        {
            if(zip.length === 5) // U.S. zip codes are always 5 digits long
                readyToSubmit = true;
            else
                readyToSubmit = false;
        } else {
            readyToSubmit = false;
        }
    }
    const randomInt = (max) => {
        return Math.floor(Math.random() * Math.floor(max));
    }
    function status(res) {
        if(res.status >= 200 && res.status < 300) {
            return Promise.resolve(res);
        } else {
            return Promise.reject(new Error(res.statusText));
        }
    }
    // TODO: Store fetched object so that it only requires one fetch for multiple random access
    // unless they change location
    // TODO: make restaurant display card into a component and pass it the full individual
    // object
    function onGetRestaurant(event) {
        //console.log(zip);
        // TODO: Move this to a separate component
        let url;
        loading = true;
        if(locationFetched)
        {
            url = `${yelpApiBaseUrl}?latitude=${lat}&longitude=${long}`;
        } else if(zip != null)
        {
            url = `${yelpApiBaseUrl}?location=${zip}`;
        }

        // approximate radius conversion
        url += `&radius=${parseInt(radius) * mpm}&open_now=true&term=food`; // might want to set limit to more in future for random carousel
        fetch(corsAnywhereUrl + url, { // need to look into cors anywhere
            method: 'GET',
            headers: {
                'Authorization' : `bearer ${apiKey}`,
            }
        })
            .then(status)
            .then(res => {
                return res.json();
            })
            .then(data => {
                // TODO: look into being able to access rand int on the FULL data.total
                //let randInt = randomInt(data.total)
                if(data.businesses.length === 0) {
                    // do something
                    locationFound = false;
                }
                let randInt = randomInt(data.businesses.length);
                restaurant = data.businesses[randInt];
                locationFound = true;
            }).catch((error) => {
                // TODO: do something better when error
                console.log('Request failed', error);
            });
        // TODO: Add conversion from miles to meters for GET request
        // TODO: Set business type to restaurant
        // Done
        loading = false;
    }

</script>

<style>

</style>
 
<h3>Random Restaurant</h3>
{#if !loading}
    <div class="row">
        <div class="col s5">
            <div class="input-field">
                <label for="zip-code">Zip code</label>
                <input type="text" bind:value={zip} on:input={handleZip}/>
            </div>
        </div>
    </div>
    <div class="row">
        <div class="col s12">
            <button class="waves-effect waves-light btn deep-purple lighten-1" on:click={getLocation}>
                <i class="material-icons left">location_on</i>
                Use my current location
            </button>
            <p>{@html location_string}</p>
        </div>
    </div>
    <div class="row">
        <div class="col s6">
            <RadiusInput bind:radius={radius}/>
        </div>
    </div>
    <div class="row">
        <div class="col s6">
            {#if readyToSubmit}
                <button class="waves-effect waves-light pulse btn deep-purple lighten-1" on:click={onGetRestaurant}>
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
{:else}
    <div class="progress">
        <div class="indeterminate"></div>
    </div>
    
{/if}
{#if locationFound}
<div class="row">
    <div class="col s6">
        <div class="card">
            <div class="card-content">
                <p class="card-title">{restaurant.name}</p>
                <p>Address: {restaurant.location.display_address}</p><br>
                <p>Phone: {restaurant.display_phone}</p>
                <p>Distance: {Math.trunc(restaurant.distance / mpm)} mi.</p>
            </div>
            <div class="card-action">
                <a href={restaurant.url}>Yelp Reviews</a>
            </div>
        </div>
    </div>
</div>
{/if}