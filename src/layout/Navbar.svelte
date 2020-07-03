<script>
    import {Link} from 'svelte-routing';
</script>

<nav>
    <div class="nav-wrapper deep-purple lighten-2">
        <div class="container">
            <Link to="/"><span class="brand-logo">Random Restaurant</span></Link>
            <ul id="nav-mobile" class="right hide-on-med-and-down">
                <li><Link to="/">Home</Link></li>
                <li><Link to="/about">About</Link></li>
            </ul>
        </div>
    </div>
</nav>
