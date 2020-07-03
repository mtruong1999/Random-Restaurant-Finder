<script>
    import {Link} from 'svelte-routing';
    // doesnt work, look into menu opening on click
    document.addEventListener('DOMContentLoaded', function(){
        var elems = document.querySelectorAll('.sidenav');
        var instances = M.Sidenav.init(elems, options);
    });
</script>

<nav>
    <div class="nav-wrapper deep-purple lighten-2">
        <div class="container">
            <Link to="/"><span class="brand-logo">Random Restaurant</span></Link>
            <a href="#" data-target="mobile-demo" class="sidenav-trigger"><i class="material-icons">menu</i></a>
            <ul id="nav-mobile" class="right hide-on-med-and-down">
                <li><Link to="/">Home</Link></li>
                <li><Link to="/about">About</Link></li>
            </ul>
        </div>
    </div>
</nav>

<ul class="sidenav" id="mobile-demo">
    <li><Link to="/">Home</Link></li>
    <li><Link to="/about">About</Link></li>
</ul>