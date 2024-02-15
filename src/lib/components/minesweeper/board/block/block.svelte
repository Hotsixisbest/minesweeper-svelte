<script lang="ts">
    import { getContext } from "svelte";
    import { BlockData } from "../board.svelte";

    export let hasMine: boolean;
    export let neighborMines: number;
    export let opened: boolean;
    export let xCoordinate: number;
    export let yCoordinate: number;
    const open: (x: number, y: number) => null | boolean = getContext("open");

    let flagged = false;
</script>

<div class="block">
    {#if opened}
        {#if hasMine}
            💣
        {:else}
            {#if neighborMines > 0}
                {neighborMines}
            {/if}
        {/if}
    {:else}
        <button
            class="cover"
            on:click={() => {
                if(!flagged){
                    let openResult = open(xCoordinate, yCoordinate);
                    if(openResult === false){
                        alert('못하시네요');
                    }
                }
            }}
            on:contextmenu|preventDefault={
                () => {
                    if(flagged){
                        flagged = false;
                    }
                    else{
                        flagged = true;
                    }
                }
            }
        >
        {#if flagged}
            🚩
        {/if}
        </button>
    {/if}
</div>

<style>
    .block{
        width:20px;
        height:20px;

        display:flex;
        justify-content: center;
        align-items: center;
    }

    .cover{
        width:100%;
        height:100%;

        display:flex;
        justify-content: center;
        align-items: center;
    }
</style>