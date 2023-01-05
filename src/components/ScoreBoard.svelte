<script>
    import {onMount} from "svelte";
    import Dialog, {Title, Content, Actions} from '@smui/dialog';
    import Button, {Label} from "@smui/button"
    import {score} from "../stores";

    $: scores = []

    const showScores = async () => {

        for (let i = 0; i < localStorage.length; i++) {
            const username = localStorage.key(i)
            console.log(username.slice(0,2))

            if(username.slice(0,2) === "*p"){

                const tries = localStorage.getItem(username);
                scores.push({
                    username: username.slice(2),
                    tries: tries
                })
            }

        }
        return scores
    }

    let name = ""
    let count = 0;
    score.subscribe(value => {
        count = value;
    });

</script>
<style>
    .score{
        background-color: cornflowerblue;
        color:white;
        border: 4px solid black;
        display:flex;
        justify-content: space-between;
        width:40%;
    }
</style>
{#await showScores()}
    <h1>fetching scores</h1>
{:then scores}
    {#each scores as score}
        <div class="score">
            <h4>{score.username}</h4>
            <h4>pogingen:{score.tries}</h4>
        </div>
    {:else }
        <h1>geen spelers</h1>
    {/each}
{/await}
