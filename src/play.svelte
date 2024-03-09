<script lang="ts">
    import { onMount,  } from 'svelte';
    import { user } from './stores.js';
    import { get } from 'svelte/store';
    import { navigate } from 'svelte-routing';

    const nbGames = 5;
    const enum State {
        LOADING,
        CHOOSE,
        HIDE,
        SHOW,
        NEXT,
        WIN
    }

    let listImg: string[] = [];
    let indexImg = 0;
    let currentPlayer = 0;
    let gamesPlayed = 0
    let isUser = false;
    let currentState = State.LOADING;
  
    onMount(async () => {
        const value = get(user);
        if (value != undefined){
            isUser = true;
        } else{
            navigate('/');
        }
    });

    async function fetchImg() {
        let imageUrls = [];
        while (imageUrls.length < 5){
            try {
                const response = await fetch('https://source.unsplash.com/random');
            if (!response.ok) {
                throw new Error(`Failed to fetch image: ${response.statusText}`);
            }

            const imageUrl = response.url;
            if (imageUrl.startsWith('https://images.')) {
                imageUrls.push(imageUrl);
            }
            } catch (error) {
                console.error('Error fetching image:', error);
            }
        }
        console.log(imageUrls);
        return imageUrls;
    }

    async function loadImages() {
        listImg = await fetchImg();
        currentState = State.CHOOSE;
    }

    function answer(isTrue: boolean) {
        gamesPlayed++;
        console.log('gamesPlayed: ',gamesPlayed);
        if (isTrue){
            user.update((u) => {
                u[(currentPlayer + 1) % 2].score += 1;
                return u;
            });
        }
        console.log(get(user));
        currentPlayer = (currentPlayer + 1) % 2;
        if (gamesPlayed === nbGames){
            currentState = State.WIN;
        } else {
            currentState = State.LOADING;
        }
    }

    let timeoutId: any;

    function delayedClick() {
        if (currentState === State.CHOOSE) {
            currentState = State.HIDE;
        } else if (currentState === State.HIDE) {
            currentState = State.SHOW;
        }

        timeoutId = null;
    }

    $: {
        if (isUser){
            switch (currentState) {
                case State.LOADING:
                    console.log('---Loading---');
                    listImg = [];
                    indexImg = 0;
                    loadImages();
                    break;
                case State.CHOOSE:
                    console.log('---Choose---');
                    break;
                case State.HIDE:
                    console.log('---Hide---');
                    break;
                case State.SHOW:
                    console.log('---Show---');
                    break;
                case State.WIN:
                    console.log('---Win---');
                    navigate('/win');
                    break;
            }
        }
    }
</script>
  
{#if isUser}
<main>
    <h1>{get(user)[currentPlayer].username}</h1>
    <!-- svelte-ignore a11y-click-events-have-key-events -->
    <!-- svelte-ignore a11y-no-static-element-interactions -->
    <div class="flip-card" 
        class:rotate={currentState === State.HIDE} 
        on:click={()=>{
              if (!timeoutId) timeoutId = setTimeout(delayedClick, 200);
        }}
    >
        <div class="flip-card-inner" class:rotate={currentState === State.HIDE}>
          <div class="flip-card-front">
            <img
              src={listImg[indexImg]}
              alt="Random Unsplash"
              width="500"
              height="500"
              style="object-fit: cover;"
            />
          </div>
          <div class="flip-card-back"></div>
        </div>
    </div>
    <!-- BACK ARROW CHOOSE -->
    <button 
        on:click={
            ()=> { if (indexImg > 0) indexImg -= 1; }
        } 
        class:hidden={currentState === State.HIDE || currentState === State.SHOW}
        disabled={indexImg === 0 || currentState === State.LOADING}
    >
        <span class="material-symbols-rounded">arrow_back_ios</span>
    </button>
    <!-- NEXT ARROW CHOOSE -->
    <button
        on:click={
            ()=> { if (indexImg < listImg.length - 1) indexImg += 1; }
        }
        class:hidden={currentState === State.HIDE || currentState === State.SHOW}
        disabled={indexImg === 4 || currentState === State.LOADING}
    >
        <span class="material-symbols-rounded">arrow_forward_ios</span>
    </button>
    <!-- CHOOSE BUTTON-->
    <!-- <button 
        on:click={()=> { currentState = State.HIDE }}
        class:hidden={currentState === State.HIDE || currentState === State.SHOW}
        disabled={currentState === State.LOADING}
    >
        <span class="material-symbols-rounded">done</span>
    </button> -->
    <!-- TRUE BUTTON -->
    <button
        class:hidden={currentState !== State.SHOW}
        on:click={()=> {answer(true)}}
    >
        <span class="material-symbols-rounded">done</span>
    </button>
    <!-- FALSE BUTTON -->
    <button
        class:hidden={currentState !== State.SHOW}
        on:click={()=> {answer(false)}}
    >
        <span class="material-symbols-rounded">close</span>
    </button>

        {#each $user as u}
            <p>{u.username} - {u.score}</p>
        {/each}
</main>
{/if}

<style lang="scss">
    main{
        display: flex;
        flex-direction: column;
        align-items: center;
        
        .hidden {
            display: none;
        }
        .flip-card {
            width: 500px;
            height: 500px;
            background-color: transparent;
        }
        .flip-card-inner {
            position: relative;
            width: 100%;
            height: 100%;
            text-align: center;
            transition: transform 0.6s;
            transform-style: preserve-3d;
        }
        .rotate {
            transform: rotateY(180deg);
        }
        .flip-card-front, .flip-card-back {
            position: absolute;
            width: 100%;
            height: 100%;
            -webkit-backface-visibility: hidden;
            backface-visibility: hidden;
        }
        .flip-card-back {
            background-color: #ff0000;
            transform: rotateY(180deg);
        }
    }
</style>