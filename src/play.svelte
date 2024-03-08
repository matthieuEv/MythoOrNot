<script lang="ts">
    import { onMount } from 'svelte';
    import { user } from './stores.js';
    import { get } from 'svelte/store';
    import { navigate } from 'svelte-routing';

    onMount(() => {
        const value = get(user);
        console.log(value);
    });
</script>

<main>
    <h1>Page 2</h1>
    {#each $user as u, i}
        <p>{i+1}. {u.username} - {u.score}</p>
    {/each}
    <button on:click={() => navigate('/')}>Goto Page 1</button>
</main>