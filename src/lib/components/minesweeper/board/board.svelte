<script lang="ts" context="module">
    function createBoard(x: number, y: number, mines: number) {
        const boardCoordinateSystem: Array<Array<BlockData>> = [];

        for (let i = 0; i < x; i++) {
            boardCoordinateSystem.push([]);
        }

        boardCoordinateSystem.forEach((e) => {
            for (let i = 0; i < y; i++) {
                e.push(new BlockData());
            }
        });

        const mineBlocks: Coordinate[] = [];
        for (let i = 0; i < mines; i++) {
            let random = createRandomCoordinate(x, y);
            while (
                mineBlocks.find((e) => e.x === random.x && e.y === random.y)
            ) {
                random = createRandomCoordinate(x, y);
            }
            mineBlocks.push(random);
        }

        mineBlocks.forEach((e) => {
            boardCoordinateSystem[e.x][e.y].putMine();

            let hasLeft = false;
            let hasRight = false;
            let hasUp = false;
            let hasDown = false;
            //왼쪽에 공간 있어요
            if (e.x > 0) {
                hasLeft = true;
            }
            //오른쪽에 공간 있어요
            if (e.x < x - 1) {
                hasRight = true;
            }
            //아래에 공간 있어요
            if (e.y > 0) {
                hasDown = true;
            }
            //위에 공간 있어요
            if (e.y < y - 1) {
                hasUp = true;
            }

            if (
                hasLeft &&
                boardCoordinateSystem[e.x - 1][e.y].hasMine === false
            ) {
                boardCoordinateSystem[e.x - 1][e.y].addNeightborMinesCount();
            }
            if (
                hasRight &&
                boardCoordinateSystem[e.x + 1][e.y].hasMine === false
            ) {
                boardCoordinateSystem[e.x + 1][e.y].addNeightborMinesCount();
            }
            if (
                hasUp &&
                boardCoordinateSystem[e.x][e.y + 1].hasMine === false
            ) {
                boardCoordinateSystem[e.x][e.y + 1].addNeightborMinesCount();
            }
            if (
                hasDown &&
                boardCoordinateSystem[e.x][e.y - 1].hasMine === false
            ) {
                boardCoordinateSystem[e.x][e.y - 1].addNeightborMinesCount();
            }

            if (
                hasLeft &&
                hasUp &&
                boardCoordinateSystem[e.x - 1][e.y + 1].hasMine === false
            ) {
                boardCoordinateSystem[e.x - 1][
                    e.y + 1
                ].addNeightborMinesCount();
            }
            if (
                hasLeft &&
                hasDown &&
                boardCoordinateSystem[e.x - 1][e.y - 1].hasMine === false
            ) {
                boardCoordinateSystem[e.x - 1][
                    e.y - 1
                ].addNeightborMinesCount();
            }
            if (
                hasRight &&
                hasUp &&
                boardCoordinateSystem[e.x + 1][e.y + 1].hasMine === false
            ) {
                boardCoordinateSystem[e.x + 1][
                    e.y + 1
                ].addNeightborMinesCount();
            }
            if (
                hasRight &&
                hasDown &&
                boardCoordinateSystem[e.x + 1][e.y - 1].hasMine === false
            ) {
                boardCoordinateSystem[e.x + 1][
                    e.y - 1
                ].addNeightborMinesCount();
            }
        });

        return {
            boardCoordinateSystem,
            mineBlocks,
        };
    }

    function createRandomCoordinate(maxX: number, maxY: number) {
        return {
            x: Math.floor(Math.random() * maxX),
            y: Math.floor(Math.random() * maxY),
        };
    }

    export class BlockData {
        neighborMines: number = 0;
        hasMine: boolean = false;
        opened: boolean = false;

        addNeightborMinesCount() {
            this.neighborMines++;
        }

        putMine() {
            this.hasMine = true;
            this.neighborMines = 0;
        }

        open() {
            this.opened = true;
        }
    }

    interface Coordinate {
        x: number;
        y: number;
    }
</script>

<script lang="ts">
    import { setContext } from "svelte";

    import BlockColumn from "./block/blockColumn.svelte";

    export let x: number;
    export let y: number;
    export let mines: number;

    let {boardCoordinateSystem, mineBlocks} = createBoard(x, y, mines);
    let openedBlocks = boardCoordinateSystem.length * boardCoordinateSystem[0].length;

    const open = (
        xCoordinate: number,
        yCoordinate: number,
    ) => {
        let column = boardCoordinateSystem[xCoordinate];
        if (!column || !column[yCoordinate]) {
            return null;
        }

        let block = boardCoordinateSystem[xCoordinate][yCoordinate];
        if (block.hasMine) {
            block.open();
            boardCoordinateSystem = boardCoordinateSystem;
            return false;
        }
        if (block.neighborMines === 0) {
            block.open();
            boardCoordinateSystem = boardCoordinateSystem;
            
            let maxX = boardCoordinateSystem.length - 1;
            let maxY = boardCoordinateSystem[0].length - 1;

            let hasLeft = false;
            let hasRight = false;
            let hasUp = false;
            let hasDown = false;
            if (
                xCoordinate > 0
            ) {
                hasLeft = true;
            }
            if (
                xCoordinate < maxX
            ) {
                hasRight = true
            }
            if (
                yCoordinate > 0
            ) {
                hasDown = true;
            }
            if (
                yCoordinate < maxY
            ) {
                hasUp = true;
            }

            if(hasLeft && boardCoordinateSystem[xCoordinate - 1][yCoordinate].opened === false && boardCoordinateSystem[xCoordinate - 1][yCoordinate].hasMine === false){
                open(xCoordinate-1,yCoordinate)
            }
            if(hasRight && boardCoordinateSystem[xCoordinate + 1][yCoordinate].opened === false && boardCoordinateSystem[xCoordinate + 1][yCoordinate].hasMine === false){
                open(xCoordinate+1, yCoordinate)
            }
            if(hasDown && boardCoordinateSystem[xCoordinate][yCoordinate - 1].opened === false && boardCoordinateSystem[xCoordinate][yCoordinate - 1].hasMine === false){
                open(xCoordinate, yCoordinate - 1)
            }
            if(hasUp && boardCoordinateSystem[xCoordinate][yCoordinate + 1].opened === false && boardCoordinateSystem[xCoordinate][yCoordinate + 1].hasMine === false){
                open(xCoordinate, yCoordinate + 1)
            }

            if(hasLeft && hasDown && boardCoordinateSystem[xCoordinate-1][yCoordinate-1].opened === false && boardCoordinateSystem[xCoordinate-1][yCoordinate - 1].hasMine === false){
                open(xCoordinate-1,yCoordinate-1)
            }
            if(hasLeft && hasUp && boardCoordinateSystem[xCoordinate-1][yCoordinate+1].opened === false && boardCoordinateSystem[xCoordinate-1][yCoordinate+1].hasMine === false){
                open(xCoordinate-1, yCoordinate+1)
            }
            if(hasRight&&hasDown && boardCoordinateSystem[xCoordinate+1][yCoordinate-1].opened === false && boardCoordinateSystem[xCoordinate+1][yCoordinate-1].hasMine === false){
                open(xCoordinate+1, yCoordinate-1)
            }
            if(hasRight && hasUp && boardCoordinateSystem[xCoordinate+1][yCoordinate+1].opened === false && boardCoordinateSystem[xCoordinate+1][yCoordinate+1].hasMine === false){
                open(xCoordinate+1, yCoordinate+1)
            }

            openedBlocks--;
            return true;
        } else {
            block.open();
            boardCoordinateSystem = boardCoordinateSystem;
            
            openedBlocks--;
            return true;
        }
    }
    setContext('open', open);

    $:if(openedBlocks === mineBlocks.length){
        alert('축하합니다!')
    }
</script>

{openedBlocks}
<div class="board">
    {#each boardCoordinateSystem as column, index}
        <BlockColumn {column} xCoordinate={index} />
    {/each}
</div>

<style>
    .board {
        display: flex;
        flex-direction: row;
    }
</style>
