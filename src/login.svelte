<script lang="ts">
  import { navigate } from "svelte-routing";
  import { user } from './stores.js';

  let user1 = 'player1';
  let user2 = 'player2';

  function handleClick(){
    if (user1 === '' || user2 === ''){
      alert('Please enter both names');
      return;
    }
    user.set([
    {
      "username":user1,
      "score":0
    },
    {
      "username":user2,
      "score":0
    }]);
    navigate("/play")
  }
</script>

<main>
  <h1>Page 1</h1>
  <input type="text" bind:value={user1} />
  <input type="text" bind:value={user2} />
  <button on:click={handleClick}>Goto Page 2</button>
</main>
