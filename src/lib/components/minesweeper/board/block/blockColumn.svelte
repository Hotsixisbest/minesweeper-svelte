<script lang="ts">
    import { BlockData } from "../board.svelte";
    import Block from "./block.svelte";

    export let column: BlockData[];
    export let xCoordinate:number;
</script>

<div class="column">
    {#each column as block, index}
        <Block hasMine={block.hasMine} neighborMines={block.neighborMines} opened={block.opened} {xCoordinate} yCoordinate={index}/>
    {/each}
</div>

<style>
    .column{
        display:flex;
        flex-direction: column-reverse;
    }
</style>