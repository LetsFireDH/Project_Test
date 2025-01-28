<script>
  import Quizcard from "./Quiz_Card.svelte";
  import Titlescreen from "./Titlescreen.svelte";
  import Endscreen from "./Endcard.svelte";
  import Music from "./Music.svelte";
  import { showscreen } from "./shared.svelte.js";
</script>

<main>
  <h1>Who's the Quizzlr?</h1>
  {#if showscreen.titlescreen === true}
    <Titlescreen />
  {:else if showscreen.titlescreen === false && showscreen.endscreen === false}
    <Quizcard />
  {:else}
    <Endscreen />
  {/if}
  <p style="margin-bottom: 50px"></p>
  <Music />
</main>

<style>
  main {
    background-color: #0f0f0f;
  }

  h1 {
    /* von https://www.geeksforgeeks.org/how-to-create-linear-gradient-text-using-html-and-css/ */
    color: white;
    line-height: normal;
    background: linear-gradient(to right, #16d861, #0eeeab,#df0adf, #350ca7);
    -webkit-text-fill-color: transparent;
    background-clip: text;
    -moz-background-clip: text;
    -webkit-background-clip: text;
  }
</style>