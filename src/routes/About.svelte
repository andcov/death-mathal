<script>
    import Katex from "../Katex.svelte";
</script>
<svelte:head>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Abhaya+Libre:wght@600&family=New+Rocker&display=swap" rel="stylesheet">
</svelte:head>
<span class="metal">Death </span><span class="math">Math</span><span class="metal">al</span>

<p>...is a place where I create tools that help me teach math.</p>

<p>More often than not, kids aren't afraid of math due to a lack of talent or intelligence, but due to a lack of exposure.
As such, it can sometimes be helpful to simply churn through a bunch of simple exercises in order to create a solid base, 
and a computer can create millions of these exercises.</p>


<p>I have also found that the kids that benefit from these exercises often perceive math as something terrible, akin to death, hence the name.</p>

<a href="https://github.com/andcov" target="_blank" rel="noopener noreferrer">@andcov</a>

<style>
    .metal {
        font-family: 'New Rocker', cursive;
        font-size: 5em;
    }
    .math {
        font-family: 'Abhaya Libre', serif;
        font-size: 5em;
    }
</style>