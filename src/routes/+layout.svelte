<script>
    let { children } = $props();
</script>

<nav>
    <a href="/">Home</a>
    <a href="/quiz">Quiz</a>
</nav>

{@render children()}