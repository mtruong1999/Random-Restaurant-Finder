<script>
    export let radius;
</script>

<p style="font-weight:bold">Select search radius</p>
<form action="#">
    <p>
        <label>
            <input name="fivemiles" type="radio" bind:group={radius} value=5>
            <span>5 miles</span>
        </label>
    </p>
    <p>
        <label>
            <input name="tenmiles" type="radio" bind:group={radius} value=10> 
            <span>10 miles</span>
        </label>
    </p>
    <p>
        <label>
            <input name="twentymiles" type="radio" bind:group={radius} value=20>
            <span>20 miles</span>
        </label>
    </p>
</form>