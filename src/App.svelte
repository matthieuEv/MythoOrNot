<script lang="ts">
    import { Router, Route, navigate } from "svelte-routing";
    import Login from "./login.svelte";
    import Play from "./play.svelte";
</script>

<main>
    <Router>
        <div>
            <Route path="/" component={Login} />
            <Route path="/play" component={Play} />
        </div>
    </Router>
</main>