<script>
    let radius = 5;

</script>

<style>

</style>
 
<h1>Random Eater</h1>

<div class="row">
    <div class="col s12">
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
    </div>
</div>