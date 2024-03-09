<script lang="ts">
    import { onMount,  } from 'svelte';
    import { user } from './stores.js';
    import { get } from 'svelte/store';
    import { navigate } from 'svelte-routing';

    let users = get(user);
    //sort users by score
    users.sort((a:any, b:any) => b.score - a.score);
</script>

<main>
    <h1>Win</h1>
    <h2>{users[0].username} wins with {users[0].score} points</h2>
    <h2>{users[1].username} has {users[1].score} points</h2>
</main>

<button on:click={()=>navigate('/')}>Play Again</button>