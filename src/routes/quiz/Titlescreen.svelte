<script>
    import { showscreen, questionamount } from "./shared.svelte.js";
</script>

<main>
    <p>Wählen Sie bitte eine Anzahl Fragen:</p>
    <label>
        <!-- Style des div-Tags von GPT4o, die beiden Inputs vom Svelte Tutorial (Numeric Inputs/Bindings)-->
        <div style="display:flex; flex-direction:row; justify-content:center">
        <input type="number" bind:value={questionamount.amount} min="5" max="15" style="font-size:21px; margin-right:15px">
        <input type="range" bind:value={questionamount.amount} min="5" max="15" style="width: 200px">
        </div>
    </label>
    <br>
    <button onclick={() => {questionamount.amount >= 3 && questionamount.amount <= 15 ? showscreen.titlescreen = false : alert("Bitte eine Zahl zwischen 5 und 15 eingeben!")}}>Quiz starten!</button> <!-- Erst wenn die Fragenanzahl zwischen 3 und 15 ist, wird der Fragescreen und damit das Quiz geladen und angezeigt.-->
</main>

<style>
    button {
    background-color: #535353;
    color: white;
    width: 500px;
    margin-bottom: 8px;
  }
  p {
    color: white;
    font-size: 24px;
  }
</style>