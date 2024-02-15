<script lang="ts">
  import Minesweeper from "./lib/components/minesweeper/minesweeper.svelte";
  let x:number = 10;
  let y:number = 10;
  let mines:number = 10;

  let restart = {};
</script>

열
<input type="number" bind:value={x}>
행
<input type="number" bind:value={y}>
지뢰
<input type="number" bind:value={mines}>
<button on:click={() => {restart = {}}}>재시작</button>
{#key restart}
<Minesweeper {x} {y} {mines}/>
{/key}