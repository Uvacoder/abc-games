<script>
    import { createEventDispatcher } from "svelte";

    export let keyCode;

    const dispatch = createEventDispatcher();

    let symbol = getSymbolFromKeyCode(keyCode);

    function getSymbolFromKeyCode(code) {
        switch (code) {
            case "ArrowUp":
                return "▲";
            case "ArrowDown":
                return "▼";
            case "ArrowLeft":
                return "◀";
            case "ArrowRight":
            default:
                return "▶";
        }
    }

    function handleClick() {
        dispatch("click", {
            code: keyCode,
        });
    }
</script>

<button
    type="button"
    on:click={handleClick}
    class="rounded-sm h-12 w-12 text-lg border border-gray-300 shadow text-gray-600"
    >{symbol}</button
>
