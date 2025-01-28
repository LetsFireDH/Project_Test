<script>
    import { score, questionamount, showscreen } from "./shared.svelte.js";
    function restartquiz() {
        score.score = 0;
        showscreen.titlescreen = true;
        showscreen.endscreen = false;
    }
</script>

<main>
    <p>Du hast {score.score} von {questionamount.amount} Fragen richtig beantwortet!</p>
    <p>Das entspricht {Math.round(100*(score.score* 100)/questionamount.amount)/100} Prozent aller Fragen.</p>
    <button onclick={() => {restartquiz()}}>Neue Runde spielen!</button>
</main>

<style>
button {
    background-color: #535353;
    color: white;
    width: 500px;
    margin-bottom: 8px;
}
p {
    font-size: 22px;
    color: #d6d6d6
}
</style>