<script lang="ts">
    import { Router, Route, navigate } from "svelte-routing";
    import Login from "./login.svelte";
    import Play from "./play.svelte";
    import Win from "./win.svelte";
</script>

<main>
    <Router>
        <div>
            <Route path="/" component={Login} />
            <Route path="/play" component={Play} />
            <Route path="/win" component={Win} />
        </div>
    </Router>
</main>