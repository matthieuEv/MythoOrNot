<script lang="ts">
  import { navigate } from "svelte-routing";
  import { user, settings } from '../stores.js';

  let user1 = '';
  let user2 = '';

  let nbGames = 10;
  let nbImg = 5;
  let points = 10;

  function handleClick(){
    if (user1 === '' || user2 === ''){
      alert('Les champs ne peuvent pas être vides');
      return;
    }
    if (5 < nbGames && nbGames > 50){
      alert('Le nombre de parties doit être compris entre 5 et 50');
      return;
    }
    if (1 < nbImg && nbImg > 10){
      alert('le nombre de cartes doit être compris entre 1 et 10');
      return;
    }
    if (1 < points && points > 10){
      alert('Le nombre de points doit être compris entre 1 et 10');
      return;
    }
    user.set([
    {
      "username":user1,
      "score":0,
      "color":"yellow"
    },
    {
      "username":user2,
      "score":0,
      "color":"green"
    }]);
    settings.set({
      "nbGames":nbGames,
      "nbImg":nbImg,
      "points":points
    });
    navigate("/play")
  }

  window.addEventListener("beforeunload", function (event) {
    event.preventDefault();
  });

</script>

<main>
  <div id="header">
    <h1>Myth</h1>
    <img src="/eyes.png" alt="logo" />
    <h1>r not</h1>
  </div>
  <div id="inputs">
    <div class="contain" style="background-color: yellow;">
      <input type="text" bind:value={user1} placeholder="Joueur 1"/>
    </div>
    <div class="contain" style="background-color: green;">
      <input type="text" bind:value={user2} placeholder="Joueur 2"/>
    </div>
  </div>
  <button class="btn" on:click={handleClick}>Jouer</button>
  <div id="settings">
    <form action="">
      <label for="nbGames">Nombres de parties: </label>
      <input type="number" id="nbGames" name="nbGames" bind:value={nbGames}>
      <label for="nbImg">Nombre d'images: </label>
      <input type="number" id="nbImg" name="nbImg" bind:value={nbImg}>
      <label for="points">Points Gagnés: </label>
      <input type="number" id="points" name="points" bind:value={points}>
    </form>
  </div>
</main>

<style lang="scss">
  main{
    display: flex;
    flex-direction: column;
    align-items: center;
    min-height: calc(100vh - 4rem);
    justify-content: center;

    #settings{
      display: flex;
      flex-direction: column;
      align-items: start;
      justify-content: center;
      padding: 20px;
      width: calc(100% - 40px);
      position: relative;

      form{
        display: flex;
        flex-direction: column;
        align-items: start;
        justify-content: center;
        position: relative;
        width: 100%;

        label{
          font-size: .9em;
          font-weight: 500;
          color: var(--color4);
        }

        input{
          background-color: var(--color2);
          width: 75%;
          height: 1.75em;
          border-radius: 1em;
          font-size: .9em;
          padding: 0 1em;
          color: var(--color5);
          outline: none;
          border: none;

          &::placeholder{
            color: var(--color5);
            opacity: .5;
            font-weight: 600;
          }
        
        }
      }
    }
    
    #header{
      display: flex;
      justify-content: center;
      align-items: center;

      img{
        width: 50px;
        height: 50px;
      }
    }
    #inputs{
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 20px;
      width: calc(100% - 40px);

      .contain{
        display: flex;
        justify-content: center;
        align-items: center;
        width: 100%;
        padding: 2px;
        margin: 1em 0;
        border-radius: 2em;
        
        input{
          margin: 10px;
          border: none;
          border: none;
          background-color: var(--color3);
          width: 100%;
          height: 4em;
          border-radius: 1em;
          font-size: 1.2em;
          padding: 0 1em;
          color: var(--color5);
          font-weight: 800;
          outline: none;
  
          &::placeholder{
            color: var(--color5);
            opacity: .5;
            font-weight: 600;
          }
        }
      }
    }
    .btn{
      justify-content: center;
      align-items: center;
      padding: .75em 2em;
      border: none;
      background-color: var(--color2);
      color: var(--color5);
      font-size: 1.5em;
      font-weight: 400;
      transform: rotate(-7deg);
      border-radius: 8px;
    }
  }
</style>