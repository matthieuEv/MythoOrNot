<script lang="ts">
    import { Router, Route } from "svelte-routing";
    import Login from "./routes/login.svelte";
    import Play from "./routes/play.svelte";
    import Win from "./routes/win.svelte";
</script>

<main style="user-select: none;">
    <Router>
        <div>
            <Route path="/" component={Login} />
            <Route path="/play" component={Play} />
            <Route path="/win" component={Win} />
        </div>
    </Router>
</main>