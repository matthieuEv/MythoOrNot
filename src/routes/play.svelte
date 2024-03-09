<script lang="ts">
    import { onMount,  } from 'svelte';
    import { user, settings } from '../stores.js';
    import { get } from 'svelte/store';
    import { navigate } from 'svelte-routing';

    console.log("settings: ", get(settings));

    window.addEventListener("beforeunload", function (event) {
        event.preventDefault();
    });
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
    let isSettings = false;
    let imgLoaded = false;
    let currentState = State.LOADING;
    let nbGames:any;
    let nbImg:any;
    let points:any;
    
    onMount(async () => {
        const value = get(user);
        const settingsValue = get(settings);
        if (value != undefined && settingsValue != undefined){
            isSettings = true;
            isUser = true;
            nbGames = settingsValue.nbGames;
            nbImg = settingsValue.nbImg;
            points = settingsValue.points;
            console.log('nbGames: ',nbGames);
            console.log('nbImg: ',nbImg);
            console.log('points: ',points);
        } else{
            navigate('/');
        }
    });

    async function fetchImg() {
        imgLoaded = false;
        let imageUrls = [];
        while (imageUrls.length < nbImg){
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
        imgLoaded = true;
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
                u[(currentPlayer + 1) % 2].score += points;
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

<!-- svelte-ignore a11y-click-events-have-key-events -->
<!-- svelte-ignore a11y-no-static-element-interactions -->
{#if isUser && isSettings}
<main>
    <h1>
        {#if currentState === State.LOADING || currentState === State.CHOOSE}
            C'est à <span style="color: {get(user)[currentPlayer].color};">{get(user)[currentPlayer].username}</span> de choisir l'image
        {:else if currentState === State.HIDE}
            Est ce que <span style="color: {get(user)[currentPlayer].color};">{get(user)[currentPlayer].username}</span> mytho?
        {:else if currentState === State.SHOW}
            Est ce que <span style="color: {get(user)[(currentPlayer + 1) % 2].color};">{get(user)[(currentPlayer + 1) % 2].username}</span> a eu bon?
        {/if}
    </h1>
    <div class="flip-card" 
        class:rotate={currentState === State.HIDE} 
        on:click={()=>{
              if (!timeoutId) timeoutId = setTimeout(delayedClick, 200);
        }}
    >
        <div class="flip-card-inner" class:rotate={currentState === State.HIDE}>
          <div class="flip-card-front">
            {#if imgLoaded}
                <img
                    src={listImg[indexImg]}
                    alt=""
                />
            {/if}
          </div>
          <div class="flip-card-back"></div>
        </div>
    </div>
    <div id="buttonContainer">
        <!-- BACK ARROW CHOOSE -->
        <button 
        on:click={
            ()=> { if (indexImg > 0) indexImg -= 1; }
        } 
        class:hidden={currentState === State.HIDE || currentState === State.SHOW}
        disabled={indexImg === 0 || currentState === State.LOADING}
        >
        <span class="material-symbols-rounded">chevron_left</span>
        </button>
        <!-- NEXT ARROW CHOOSE -->
        <button
        on:click={
            ()=> { if (indexImg < listImg.length - 1) indexImg += 1; }
        }
        class:hidden={currentState === State.HIDE || currentState === State.SHOW}
        disabled={indexImg === (nbImg-1) || currentState === State.LOADING}
        >
        <span class="material-symbols-rounded">chevron_right</span>
        </button>
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
    </div>
    <div id="score">
        {#each $user as u}
            <div class="user" style="background-color: {u.color};">
                <p>{u.username}</p>
                <p>Score: {u.score}</p>
            </div>
        {/each}
    </div>
</main>
{/if}

<style lang="scss">
    main{
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        margin-top: 3em;

        #score{
            display: flex;
            flex-direction: column;
            gap: 1em;
            width: 80vw;
            margin-top: 2em;

            .user{
                display: flex;
                justify-content: space-around;
                align-items: center;
                border-radius: 8px;
                color: var(--color5);
                transform: rotate(-2.5deg);
                font-weight: 700;
            }
        }

        #buttonContainer{
            width: 80vw;
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 1em;

            button{
                text-align: center;
                padding: 1em 3em;
                width: 100%;
                background-color: var(--color2);
                color: var(--color5);

                &:disabled{
                    opacity: .25;
                }

                span{
                    vertical-align: middle;
                    font-size: 3em;
                }
            }
        }

        h1{
            font-size: 1.5em;
            margin: 0;
            font-weight: 500;
            height: 2.5em;

            span{
                font-weight: 700;
            }
        }
        
        .hidden {
            display: none;
        }
        .flip-card {
            width: 80vw;
            height: 80vw;
            background-color: transparent;
            margin: 2em 0;
            border-radius: 1em;
            overflow: hidden;

            img{
                width: 100%;
                height: 100%;
                object-fit: cover;
            }
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

        .flip-card-front{
            background-color: #00000023;
        }
        .flip-card-back {
            background-image: url("../assets/card.png");
            transform: rotateY(180deg);
            background-size: cover;
        }
    }
</style>