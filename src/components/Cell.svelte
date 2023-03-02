<script>
export let coordinate, snakeCoords, fruitCoords;

$: isFruitCell = fruitCoords.x === coordinate.x && fruitCoords.y === coordinate.y
$: isSnakeCell = snakeCoords.some(c => {
    return c.x === coordinate.x && c.y === coordinate.y
})
</script>

<span class="inline-flex items-center justify-center h-6 w-6 border border-gray-300 -mr-px -mb-px">
    <span class="h-4 w-4 rounded-full" class:bg-emerald-600={isSnakeCell} class:bg-red-500={isFruitCell}></span>
</span>
