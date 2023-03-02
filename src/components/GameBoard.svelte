<script>
    import { tick, onMount } from "svelte";
    import Cell from "./Cell.svelte";

    const SIZE = 20;
    const DIRECTIONS = {
        UP: { code: "ArrowUp", x: 0, y: -1, disallow: "ArrowDown" },
        DOWN: { code: "ArrowDown", x: 0, y: 1, disallow: "ArrowUp" },
        LEFT: { code: "ArrowLeft", x: -1, y: 0, disallow: "ArrowRight" },
        RIGHT: { code: "ArrowRight", x: 1, y: 0, disallow: "ArrowLeft" },
    };
    const KEYS = {
        ArrowUp: "UP",
        ArrowDown: "DOWN",
        ArrowLeft: "LEFT",
        ArrowRight: "RIGHT",
    };
    const STATUS = {
        STOPPED: "stopped",
        RUNNING: "running",
        GAME_OVER: "game over",
    };

    const cells = [...new Array(SIZE).keys()];
    $: currentDirection = DIRECTIONS.UP;
    $: currentStatus = STATUS.STOPPED;

    $: snakeCoordinates = [
        { x: 10, y: 10 },
        { x: 10, y: 11 },
        { x: 10, y: 12 },
    ];

    $: fruitCoordinates = { x: 4, y: 4 };

    function sleep(ms) {
        return new Promise((resolve) => setTimeout(resolve, ms));
    }

    function startGame() {
        currentStatus = STATUS.RUNNING;
        next();
    }

    function restartGame() {
        snakeCoordinates = [
            { x: 10, y: 10 },
            { x: 10, y: 11 },
            { x: 10, y: 12 },
        ];

        fruitCoordinates = { x: 4, y: 4 };
        currentDirection = DIRECTIONS.UP;
        startGame();
    }

    function stopGame(status = STATUS.STOPPED) {
        console.log("setting status:", status);
        currentStatus = status;
    }

    async function next() {
        if (currentStatus === STATUS.RUNNING) {
            moveSnake();
            await sleep(100);
            next();
        }
    }

    function isIntersectingSnake({ x, y }) {
        return snakeCoordinates.some((coord) => {
            return x === coord.x && y === coord.y;
        });
    }

    function getNextSnakeCoordinates() {
        let [head] = snakeCoordinates;

        let newHead = {
            x: head.x + currentDirection.x,
            y: head.y + currentDirection.y,
        };

        // allow looping to other side of board
        if (newHead.x === -1) {
            newHead.x = 19;
        }
        if (newHead.y === -1) {
            newHead.y = 19;
        }
        if (newHead.x === 20) {
            newHead.x = 0;
        }
        if (newHead.y === 20) {
            newHead.y = 0;
        }

        // if we hit the snake, we are done.
        if (isIntersectingSnake(newHead)) {
            return null;
        }

        // if we hit the fruit, move the fruit and add one cell to our snake
        if (
            newHead.x === fruitCoordinates.x &&
            newHead.y === fruitCoordinates.y
        ) {
            moveFruit();
            return [newHead, ...snakeCoordinates];
        }

        // otherwise, just move every item up one position
        return [newHead, ...snakeCoordinates.slice(0, -1)];
    }

    async function moveSnake() {
        let newSnake = getNextSnakeCoordinates();
        if (!newSnake) {
            stopGame(STATUS.GAME_OVER);
        } else {
            snakeCoordinates = [...newSnake];
        }
    }

    function moveFruit() {
        let newCoordinates = {};
        let valid = false;
        while (!valid) {
            newCoordinates = {
                x: Math.floor(Math.random() * SIZE),
                y: Math.floor(Math.random() * SIZE),
            };

            valid =
                !isIntersectingSnake(newCoordinates) &&
                newCoordinates.x !== fruitCoordinates.x &&
                newCoordinates.y !== fruitCoordinates.y;
        }
        fruitCoordinates = newCoordinates;
    }

    function handleKeypress(event) {
        if (currentStatus === STATUS.RUNNING) {
            if (event.code !== currentDirection.disallow) {
                currentDirection = DIRECTIONS[KEYS[event.code]];
            }
        }
    }
</script>

<svelte:window on:keydown={handleKeypress} />

<h1 class="text-xl md:text-3xl font-bold mt-20 text-center">🐍 Snake 🐍</h1>

<div class="mt-24 relative flex flex-col items-center justify-center">
    {#each cells as y}
        <div class="flex items-center">
            {#each cells as x}
                <Cell
                    coordinate={{ x, y }}
                    snakeCoords={snakeCoordinates}
                    fruitCoords={fruitCoordinates}
                />
            {/each}
        </div>
    {/each}
    {#if currentStatus === "game over"}
        <div
            class="absolute top-1/2 transform -translate-y-1/2 -rotate-6 bg-red-600 text-white font-bold text-2xl px-6 py-2 uppercase"
        >
            Game Over
        </div>
    {/if}
</div>

<div class="mt-12 mx-auto flex items-center justify-center">
    {#if currentStatus === "stopped"}
        <button
            type="button"
            on:click={() => startGame()}
            class="inline-flex items-center rounded-md border border-transparent bg-emerald-100 px-3 py-2 text-sm font-medium leading-4 text-emerald-700 hover:bg-emerald-200 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:ring-offset-2"
            >Start Game</button
        >
    {/if}
    {#if currentStatus === "running"}
        <button
            type="button"
            on:click={() => stopGame()}
            class="inline-flex items-center rounded-md border border-transparent bg-red-100 px-3 py-2 text-sm font-medium leading-4 text-red-700 hover:bg-red-200 focus:outline-none focus:ring-2 focus:ring-red-500 focus:ring-offset-2"
            >Stop Game</button
        >
    {/if}
    {#if currentStatus === "game over"}
        <button
            type="button"
            on:click={() => restartGame()}
            class="inline-flex items-center rounded-md border border-transparent bg-emerald-100 px-3 py-2 text-sm font-medium leading-4 text-emerald-700 hover:bg-emerald-200 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:ring-offset-2"
            >New Game</button
        >
    {/if}
</div>
