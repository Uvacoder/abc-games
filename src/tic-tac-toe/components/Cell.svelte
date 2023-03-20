<script>
import IconX from './IconX.svelte';
import IconO from './IconO.svelte';
export let xCoordinate, yCoordinate, value;

</script>

<span class="border border-gray-700 p-1 w-24 h-24 md:w-40 md:h-40 flex items-center justify-center"
    class:border-l-0={xCoordinate === 0}
    class:border-t-0={yCoordinate === 0}
    class:border-r-0={xCoordinate === 2}
    class:border-b-0={yCoordinate === 2}>
    {#if value === "x"}
        <IconX />
    {:else if value === "o"}
        <IconO />
    {:else}
        <button class="focus:outline-none h-full w-full bg-transparent" on:click></button>
    {/if}
</span>
