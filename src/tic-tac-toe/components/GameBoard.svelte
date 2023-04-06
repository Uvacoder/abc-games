<script>
    import Cell from "./Cell.svelte";

    // global stuff
    let totalWinsX = 0;
    let totalWinsO = 0;
    let showNewGameButton = false;
    let showTieBanner = false;
    $: gameOver = showNewGameButton === true && showTieBanner === false;

    // game setup
    let cells = [
        [
            { value: null, color: "text-gray-700" },
            { value: null, color: "text-gray-700" },
            { value: null, color: "text-gray-700" },
        ],
        [
            { value: null, color: "text-gray-700" },
            { value: null, color: "text-gray-700" },
            { value: null, color: "text-gray-700" },
        ],
        [
            { value: null, color: "text-gray-700" },
            { value: null, color: "text-gray-700" },
            { value: null, color: "text-gray-700" },
        ],
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
            [
                { value: null, color: "text-gray-700" },
                { value: null, color: "text-gray-700" },
                { value: null, color: "text-gray-700" },
            ],
            [
                { value: null, color: "text-gray-700" },
                { value: null, color: "text-gray-700" },
                { value: null, color: "text-gray-700" },
            ],
            [
                { value: null, color: "text-gray-700" },
                { value: null, color: "text-gray-700" },
                { value: null, color: "text-gray-700" },
            ],
        ];
        showNewGameButton = false;
        showTieBanner = false;
        setNextTurn();
    }

    function setCellValue(x, y) {
        cells[x][y] = { value: currentPlayer, color: "text-gray-700" };
    }

    function checkForWinner(x, y) {
        let col = y;
        let row = x;

        // check columns
        // lock the row
        if (
            cells[row][0].value === currentPlayer &&
            cells[row][1].value === currentPlayer &&
            cells[row][2].value === currentPlayer
        ) {
            cells[row][0].color = "text-emerald-500";
            cells[row][1].color = "text-emerald-500";
            cells[row][2].color = "text-emerald-500";
            return true;
        }

        // check rows
        // lock the column
        if (
            cells[0][col].value === currentPlayer &&
            cells[1][col].value === currentPlayer &&
            cells[2][col].value === currentPlayer
        ) {
            cells[0][col].color = "text-emerald-500";
            cells[1][col].color = "text-emerald-500";
            cells[2][col].color = "text-emerald-500";
            return true;
        }

        // check diagonals
        // but only if the player moved in the X spots
        if ((row === 1 || col === 1) && !(row === 1 && col === 1)) {
            return false;
        }
        // check top left to bottom right
        if (
            cells[0][0].value === currentPlayer &&
            cells[1][1].value === currentPlayer &&
            cells[2][2].value === currentPlayer
        ) {
            cells[0][0].color = "text-emerald-500";
            cells[1][1].color = "text-emerald-500";
            cells[2][2].color = "text-emerald-500";
            return true;
        }

        // check top right to bottom left
        if (
            cells[0][2].value === currentPlayer &&
            cells[1][1].value === currentPlayer &&
            cells[2][0].value === currentPlayer
        ) {
            cells[0][2].color = "text-emerald-500";
            cells[1][1].color = "text-emerald-500";
            cells[2][0].color = "text-emerald-500";
            return true;
        }

        return false;
    }

    function checkForTie() {
        let remainingCells = cells
            .flatMap((c) => c)
            .filter((c) => c.value === null).length;
        if (remainingCells === 0) {
            showTieBanner = true;
            showNewGameButton = true;
            return true;
        }
        return false;
    }

    // handlers
    function handleClick(x, y) {
        // ignore events if the game is over
        if (showNewGameButton === true) return;

        setCellValue(x, y);
        let won = checkForWinner(x, y);
        if (!won) {
            // check if tie
            const didTie = checkForTie();
            if (!didTie) {
                setNextTurn();
            }
        } else {
            if (currentPlayer === "x") {
                incrementWinsX();
            } else {
                incrementWinsO();
            }
            showNewGameButton = true;
        }
        // always set next turn, so the loser gets to start the next game.
    }
</script>

<h1 class="text-xl md:text-3xl font-bold mt-8 lg:mt-20 text-center">
    Tic Tac Toe
</h1>

<div class="relative flex flex-col items-center justify-center">
    <div class="my-8 sm:my-20">
        {#each cells as y, yIndex}
            <div class="flex items-center justify-center">
                {#each y as _, xIndex}
                    <Cell
                        xCoordinate={xIndex}
                        yCoordinate={yIndex}
                        value={cells[xIndex][yIndex].value}
                        color={cells[xIndex][yIndex].color}
                        {currentPlayer}
                        on:click={() => handleClick(xIndex, yIndex)}
                    />
                {/each}
            </div>
        {/each}
    </div>

    {#if showNewGameButton === true}
        <p class="bg-blue-600 text-white font-bold text-xl px-3 py-1 uppercase">
            {#if showTieBanner === true}
                It's a tie!
            {:else}
                <span class="uppercase">{currentPlayer}</span> Wins!
            {/if}
        </p>
    {:else}
        <p class="text-xl font-bold px-3 py-1 text-white bg-red-600">
            Current player: <span class="uppercase">{currentPlayer}</span>
        </p>
    {/if}
</div>

<div class="mt-6 lg:mt-8 mx-auto flex flex-col items-center">
    <h2 class="text-lg font-bold mb-2">Scores</h2>
    <div
        class="flex items-center border border-gray-300 divide-x divide-gray-300"
    >
        <div class="px-3 py-1">X wins: {totalWinsX}</div>
        <div class="px-3 py-1">O wins: {totalWinsO}</div>
    </div>
</div>

<div class="mt-6 lg:mt-8 mx-auto flex flex-col items-center">
    {#if showNewGameButton}
        <button
            type="button"
            on:click={() => resetGame()}
            class="inline-flex items-center rounded-md border border-transparent bg-emerald-100 px-3 py-2 text-sm font-medium leading-4 text-emerald-700 hover:bg-emerald-200 focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:ring-offset-2"
            >New Game</button
        >
    {/if}
</div>
