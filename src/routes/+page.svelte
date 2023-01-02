<script>
    const themes = [
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
    ]

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
        let selectedThemes = []
        let selected = []
        while (selected.length <3){
            let rndm = Math.floor(Math.random()* 5);
            if(!selected.includes(rndm)){
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


    let selected = []
    let elements = []
    $: count = 0;
    function select(id, i) {
        let e = document.getElementById(i)
        e.style.backgroundColor = "crimson"
        elements.push([i,id])
        selected.push(id)
        if (selected.length === 4) {
            if(id*4 === selected.reduce((a,b)=> a+b,0)){
                elements.forEach(
                    (element)=>{
                        document.getElementById(element[0]).style.backgroundColor="green";
                    }
                )
            }
            else{
                elements.forEach(
                    (element)=>{
                        document.getElementById(element[0]).style.backgroundColor="#416bb8";
                    }
                )
            }
            selected = []
            elements= []
            count++;
        }
    }
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

    .selected{
        background-color: crimson !important;
    }
</style>

{#await fetchKeywords()}
    <h1>loadng</h1>
{:then k}
    <div class="grid">
        {#each k as kw, i}
            <button on:click={()=>select(kw[0], i) } id={i} class="cell">{kw[1]}</button>
        {/each}
    </div>

    tries:{count}
{/await}
