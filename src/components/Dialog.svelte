<script>
    import Dialog, {Title, Content, Actions} from '@smui/dialog';
    import Button, {Label} from "@smui/button"
    import {score} from "../stores";
    import Game from "./Game.svelte";

    let open = false
    let name = ""
    let count = 0;
    score.subscribe(value => {
        count = value;
    });

    function addScore(score, user) {
        let newUser = "*p".concat(user)

        for (let i = 0; i < localStorage.length; i++) {
            const username = localStorage.key(i)
            if (username === "*p".concat(user)) {
                newUser = newUser.concat(" *")
            }
        }
        localStorage.setItem(newUser, score)
        console.log("ok")
        document.location.reload()
    }

</script>

<Dialog bind:open>
    <Title>how do you want to be seen on the scoreboard</Title>
    <Content>
        <label>
            <input bind:value={name}>
        </label>
    </Content>
    <Actions>
        <Button on:click={() => (open=false)}>
            <Label>cancel</Label>
        </Button>
        <Button on:click={() => (addScore(count,name))}>
            <Label>Ok</Label>
        </Button>
    </Actions>
</Dialog>

<Game on:message={()=>open =true}/>
