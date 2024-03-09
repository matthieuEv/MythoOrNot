<script lang="ts">
    import { user } from '../stores.js';
    import { get } from 'svelte/store';
    import { navigate } from 'svelte-routing';
    import { onMount } from 'svelte';

    window.addEventListener("beforeunload", function (event) {
        event.preventDefault();
    });
    let isUser = false;
    let users:any[] = [];

    onMount(async () => {
    const value = get(user);
    if (value !== undefined) {
        isUser = true;
        users = get(user).sort((a:any, b:any) => b.score - a.score);
    } else {
        navigate('/');
    }
    });
</script>

{#if isUser}
<main>
    <div id="users">
        <div class="user">
            <span class="winner">👑</span>
            <h1 class="winner" style="color: {users[0].color};">{users[0].username}</h1>
            <h3>Score: {users[0].score}</h3>
        </div>
        <div class="user">
            <span class="looser">☔</span>
            <h1 class="looser" style="color: {users[1].color};">{users[1].username}</h1>
            <h3>Score: {users[1].score}</h3>
        </div>
    </div>
    <button class="btn" on:click={()=>navigate('/')}>Play Again</button>
</main>
{/if}

<style lang="scss">
    main{
        display: flex;
        flex-direction: column;
        align-items: center;
        min-height: calc(100vh - 4rem);
        justify-content: center;

        #users{
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            margin-bottom: 2em;
            position: relative;
            gap: 3em;

            .user{
                h1{
                    margin: 0;
                }

                h1.winner{
                    font-size: 3em;
                    font-weight: 700;
                }
                h1.looser{
                    font-size: 2em;
                    font-weight: 400;
                    transform: rotate(10deg);

                }

                span.winner{
                    position: absolute;
                    top: 0;
                    left: 0;
                    font-size: 3em;
                    transform: translate(-50%, -50%) rotate(-30deg);
                }
                span.looser{
                    position: absolute;
                    bottom: 0;
                    right: 0;
                    font-size: 3em;
                    transform: translate(0%, -75%);
                    z-index: 1;
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
            border-radius: 8px;
        }
    }
</style>