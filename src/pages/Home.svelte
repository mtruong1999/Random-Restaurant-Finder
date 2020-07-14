<script>
    import RadiusInput from '../components/RadiusInput.svelte';
    
    let radius = 5; // RadiusInput currently returns string, need to type case to integer when using
    let location_string = '';
    let zip = null;
    let readyToSubmit = false; // set to true when have a location AND radius? or use default radius?
    let locationFetched = false;

    let long = null;
    let lat = null;
    const apiKey = __myapp.env.API_KEY;
    //const apiBaseUrl = 'http://api.geonames.org/postalCodeSearch?';
    //const un = 'mtruong1999';
    const yelpApiBaseUrl = 'https://api.yelp.com/v3/businesses/search';
    const corsAnywhereUrl = 'https://cors-anywhere.herokuapp.com/';
    const mpm = 1609; // meters per mile
    
    const handlebtn = () => {
        console.log(typeof(radius));
        console.log(typeof(long));
        console.log(typeof(lat));
        console.log(long);
        console.log(typeof(zip));
        console.log(apiKey);
    }
    

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

    function status(res) {
        if(res.status >= 200 && res.status < 300) {
            return Promise.resolve(res);
        } else {
            return Promise.reject(new Error(res.statusText));
        }
    }
    function onGetRestaurant(event) {
        console.log(zip);
        /*
        if(zip !== null || locationFetched)
        {
            // have required info, get nearby restaurants
            if(zip !== null) // might not need this check because btn should be disabled
            {
                // zip field not empty, get long/lat for zip
                fetch(`${apiBaseUrl}postalcode=${zip}&username=${un}&country=us&type=json`)
                    .then(status)
                    .then(res => {
                        return res.json();
                    })
                    .then((data) => {
                        lat = data.postalCodes[0].lat;
                        long = data.postalCodes[0].lng;
                    }).catch((error) => {
                        // TODO: do something better when error
                        console.log('Request failed', error);
                    });
            }
        }
        */
        // Dont need zip anymore since can be passed to yelp api
        // so we can instead check whether we are wanting to use
        // zip or lat/long in request
        let url;
        if(locationFetched)
        {
            url = `${yelpApiBaseUrl}?latitude=${lat}&longitude=${long}`;
        } else if(zip != null)
        {
            url = `${yelpApiBaseUrl}?location=${zip}`;
        }
        // approximate radius conversion
        url += `&radius=${parseInt(zip) * mpm}&limit=1&open_now=true`; // might want to set limit to more in future for random carousel
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
                console.log(data);
            }).catch((error) => {
                // TODO: do something better when error
                console.log('Request failed', error);
            });
        // TODO: Add conversion from miles to meters for GET request

    }

</script>

<style>

</style>
 
<h1>Random Restaurant</h1>

<div class="row">
    <div class="col s6">
        <div class="input-field">
            <label for="zip-code">Zip code</label>
            <input type="text" bind:value={zip} on:input={handleZip}/>
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
            <button class="waves-effect waves-light pulse btn" on:click={onGetRestaurant}>
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