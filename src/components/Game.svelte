<script>
    import {createEventDispatcher} from "svelte";
    import {score} from '../stores.js';
    import Dialog from "./Dialog.svelte";

    const fetchThemes = (async () => {
        const response = await fetch('http://127.0.0.1:8080/')
        return response.json()

    })()
    let themes = []

    /*const themes = [
        {
            theme: "andrew tate",
            keywords: [[0, "topG"], [0, "hustlers university"], [0, "human trafficking"], [0, "the matrix"]]
        },
        {
            theme: "greta thunberg",
            keywords: [[1, "How dare you?"], [1, "Swedish"], [1, "Chill ** chill"], [1, "climate"]]
        },
        {
            theme: "joe rogan",
            keywords: [[2, "mma"], [2, "podcasts"], [2, "anti covid"], [2, "comedian"]]
        },
        {
            theme: "stijn meerts",
            keywords: [[3, "gestopt me school"], [3, "traag"], [3, "minecraft"], [3, "grinding"]]
        },
        {
            theme: "vlaams belang",
            keywords: [[4, "eige volk eerst"], [4, "linkse ratte"], [4, "terug naar uw eige land"], [4, "vlaamdere apart"]]
        },
        {
            theme: "snoop dogg",
            keywords: [[5, "420"], [5, "gin n juice"], [5, "straigh outta compton"], [5, "mac & devon go to highschool"]]
        },
    ]*/

    function indexThemes() {
        themes.forEach(
            (theme, i) => {
                let indexedThemes = []
                theme.keywords.forEach((kw) => {
                        indexedThemes.push([i, kw])
                    }
                )
                theme.keywords = indexedThemes
            }
        )
    }

    function shuffleArray(array) {
        for (let i = array.length - 1; i > 0; i--) {
            let j = Math.floor(Math.random() * (i + 1));
            let temp = array[i];
            array[i] = array[j];
            array[j] = temp;
        }
    }

    $: keywords = []
    const fetchKeywords = async () => {
        themes = await fetchThemes
        indexThemes()
        let selectedThemes = []
        let selected = []
        while (selected.length < 3) {
            let rndm = Math.floor(Math.random() * 4);
            if (!selected.includes(rndm)) {
                selectedThemes.push(themes[rndm])
                selected.push(rndm)
            }
        }
        selectedThemes.forEach(
            (theme) => {
                theme.keywords.forEach(
                    (kw) => {
                        keywords.push(kw)
                    }
                )
            }
        )
        await shuffleArray(keywords)
        return keywords
    }

    const dispatch = createEventDispatcher();
    let countValue;

    score.subscribe(value => {
        countValue = value;
    });

    let selected = []
    let elements = []
    $:count = 0;

    function select(id, i) {
        let e = document.getElementById(i)
        e.style.backgroundColor = "crimson"
        elements.push([i, id])
        selected.push(id)
        if (selected.length === 4) {
            if (id * 4 === selected.reduce((a, b) => a + b, 0)) {
                elements.forEach(
                    (element) => {
                        document.getElementById(element[0]).style.backgroundColor = "green";
                        switch (id) {
                            case 0:
                                visible0 = false
                                break;
                            case 1:
                                visible1 = false
                                break;
                            default:
                                visible2 = false
                                break;

                        }
                    }
                )
                count++;
            } else {
                elements.forEach(
                    (element) => {
                        document.getElementById(element[0]).style.backgroundColor = "#416bb8";
                    }
                )
                score.update(n => n + 1)

            }
            selected = []
            elements = []
            if (count === 3) {
                dispatch("message", {
                    text: "game over"
                })
            }
        }
    }

    import {fade} from 'svelte/transition';

    let visible0 = true;
    let visible1 = true;
    let visible2 = true;

</script>

<style>
    .grid {
        display: grid;
        grid-template-columns: 1fr 1fr 1fr;
        grid-column-gap: .2em;
        grid-row-gap: .2em;
    }

    .cell {
        background-color: #416bb8;
        padding: 0.5em;
        text-align: center;
        color: #ffffff;
        font-size: larger;
    }

    .cell:hover {
        background-color: chocolate;
    }

    .selected {
        background-color: crimson !important;
    }
</style>


{#await fetchKeywords()}
    <h1>loadng</h1>
{:then k}
    <div class="grid">
        {#each k as kw, i}
            {#if kw[0] === 0}
                {#if visible0}
                    <button transition:fade on:click={()=>select(kw[0], i) } id={i} class="cell">{kw[1]}0</button>
                {/if}

            {:else if kw[0] === 1}
                {#if visible1}
                    <button transition:fade on:click={()=>select(kw[0], i) } id={i} class="cell">{kw[1]}1</button>
                {/if}

            {:else}
                {#if visible2}
                    <button transition:fade on:click={()=>select(kw[0], i) } id={i} class="cell">{kw[1]}2</button>
                {/if}

            {/if}
        {/each}
    </div>

    tries:{countValue}
{/await}

