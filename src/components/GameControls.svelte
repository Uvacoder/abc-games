<script>
    import ArrowKey from './ArrowKey.svelte'
    import { createEventDispatcher } from 'svelte';

    const dispatch = createEventDispatcher();

    function handleClick(event) {
        // just bubble it up
        dispatch("click", event.detail)
    }
</script>

<div class="flex flex-col items-center gap-1">
    <div>
        <ArrowKey keyCode="ArrowUp" on:click={handleClick} />
    </div>
    <div class="flex items-center justify-center gap-1">
        <ArrowKey keyCode="ArrowLeft" on:click={handleClick} />
        <ArrowKey keyCode="ArrowDown" on:click={handleClick} />
        <ArrowKey keyCode="ArrowRight" on:click={handleClick} />
    </div>
</div>
