<script>
import IconX from './IconX.svelte';
import IconO from './IconO.svelte';
export let xCoordinate, yCoordinate, value, color, currentPlayer;

</script>

<span class="border border-gray-700 w-24 h-24 md:w-40 md:h-40 flex items-center justify-center"
    class:border-l-0={xCoordinate === 0}
    class:border-t-0={yCoordinate === 0}
    class:border-r-0={xCoordinate === 2}
    class:border-b-0={yCoordinate === 2}
    class:text-emerald-500={color === 'text-emerald-500'}
>
    {#if value === "x"}
        <IconX />
    {:else if value === "o"}
        <IconO />
    {:else}
        <button class="focus:outline-none h-full w-full bg-transparent text-transparent hover:text-gray-300" on:click>
            {#if currentPlayer === "x"}
                <IconX />
            {:else if currentPlayer === "o"}
                <IconO />
            {/if}
        </button>
    {/if}
</span>
