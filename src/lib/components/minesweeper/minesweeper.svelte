<script lang="ts" context="module">

</script>

<script lang="ts">
    import Board from "./board/board.svelte";

    export let x:number;
    export let y:number;
    export let mines:number;

</script>

<Board {x} {y} {mines}/>