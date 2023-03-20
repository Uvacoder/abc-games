<script>
import GameCard from '../components/GameCard.svelte'
</script>

<main class="w-full mx-auto">
    <div class="grid grid-cols-2 gap-4 sm:gap-8 mt-12 px-8 text-2xl md:text-3xl">
        <GameCard href="/snake" title="Snake" />
        <GameCard href="/tic-tac-toe" title="Tic Tac Toe" />
    </div>
</main>
