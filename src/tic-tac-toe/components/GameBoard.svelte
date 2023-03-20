<script>
    import Cell from "./Cell.svelte";

    // global stuff
    let totalWinsX = 0;
    let totalWinsO = 0;
    let showNewGameButton = false;
    let showTieBanner = false;

    // game setup
    let cells = [
        [null, null, null],
        [null, null, null],
        [null, null, null],
    ];
    let currentPlayer = "x";

    // logic
    function incrementWinsX() {
        totalWinsX++;
    }

    function incrementWinsO() {
        totalWinsO++;
    }

    function setNextTurn() {
        if (currentPlayer === "x") {
            currentPlayer = "o";
        } else {
            currentPlayer = "x";
        }
    }

    function resetGame() {
        cells = [
            [null, null, null],
            [null, null, null],
            [null, null, null],
        ];
        showNewGameButton = false;
        showTieBanner = false;
    }

    function setCellValue(x, y) {
        cells[x][y] = currentPlayer;
    }

    function checkForWinner(x, y) {
        let col = y;
        let row = x;

        // check columns
        // lock the row
        if (
            cells[row][0] === currentPlayer &&
            cells[row][1] === currentPlayer &&
            cells[row][2] === currentPlayer
        ) {
            return true;
        }

        // check rows
        // lock the column
        if (
            cells[0][col] === currentPlayer &&
            cells[1][col] === currentPlayer &&
            cells[2][col] === currentPlayer
        ) {
            return true;
        }

        // check diagonals
        // but only if the player moved in the X spots
        if ((row === 1 || col === 1) && !(row === 1 && col === 1)) {
            return false;
        }
        // check top left to bottom right
        if (
            cells[0][0] === currentPlayer &&
            cells[1][1] === currentPlayer &&
            cells[2][2] === currentPlayer
        ) {
            return true;
        }

        // check top right to bottom left
        if (
            cells[0][2] === currentPlayer &&
            cells[1][1] === currentPlayer &&
            cells[2][0] === currentPlayer
        ) {
            return true;
        }

        return false;
    }

    function checkForTie() {
        let remainingCells = cells
            .flatMap((c) => c)
            .filter((c) => c === null).length;
        if (remainingCells === 0) {
            showTieBanner = true;
            showNewGameButton = true;
        }
    }

    // handlers
    function handleClick(x, y) {
        // ignore events if the game is over
        if (showNewGameButton === true) return;

        setCellValue(x, y);
        let won = checkForWinner(x, y);
        if (!won) {
            // check if tie
            checkForTie();
        } else {
            if (currentPlayer === "x") {
                incrementWinsX();
            } else {
                incrementWinsO();
            }
            showNewGameButton = true;
        }
        // always set next turn, so the loser gets to start the next game.
        setNextTurn();
    }
</script>

<h1 class="text-xl md:text-3xl font-bold mt-8 sm:mt-20 text-center">
    Tic Tac Toe
</h1>

<div class="mt-8 sm:mt-16 relative flex flex-col items-center justify-center">
    <div class="my-20">
        {#each cells as y, yIndex}
            <div class="flex items-center justify-center">
                {#each y as _, xIndex}
                    <Cell
                        xCoordinate={xIndex}
                        yCoordinate={yIndex}
                        value={cells[xIndex][yIndex]}
                        on:click={() => handleClick(xIndex, yIndex)}
                    />
                {/each}
            </div>
        {/each}
    </div>

    {#if showNewGameButton === true}
        <div
            class="absolute top-1/2 transform -translate-y-1/2 -rotate-6 bg-blue-600 text-white font-bold text-2xl px-6 py-2 uppercase"
        >
            {#if showTieBanner === true}
                <p>It's a tie!</p>
            {:else}
                <p><span class="uppercase">{currentPlayer}</span> Wins!</p>
            {/if}
        </div>
    {/if}

    <p class="text-xl font-bold px-3 py-1 text-white bg-red-600">
        Current player: <span class="uppercase">{currentPlayer}</span>
    </p>
</div>

<div class="mt-6 sm:mt-8 mx-auto flex flex-col items-center">
    <h2 class="text-lg font-bold mb-2">Scores</h2>
    <div
        class="flex items-center border border-gray-300 divide-x divide-gray-300"
    >
        <div class="px-3 py-1">X wins: {totalWinsX}</div>
        <div class="px-3 py-1">O wins: {totalWinsO}</div>
    </div>
</div>

<div class="mt-6 sm:mt-8 mx-auto flex flex-col items-center">
    {#if showNewGameButton}
        <button
            type="button"
            on:click={() => resetGame()}
            class="inline-flex items-center rounded-md border border-transparent bg-emerald-100 px-3 py-2 text-sm font-medium leading-4 text-emerald-700 hover:bg-emerald-200 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:ring-offset-2"
            >New Game</button
        >
    {/if}
</div>
