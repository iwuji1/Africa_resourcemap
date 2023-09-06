<script>
  import Tree from "$lib/components/tree_map.svelte";

</script>

<style>

  :global(.tick text, .axis-title) {
      font-size: 12px; /* How big our text is */
      font-family:sans-serif;
      font-weight: 400; /* How bold our text is */
      fill: hsla(212, 10%, 53%, 1); /* The color of our text */
      user-select: none; /* Prevents text from being selected */
  }

    h1 {
    font-size: 5rem; /* How big our text is */
    margin: 0 0 0.5rem 0; /* Adds a bottom margin */
    font-weight: 600; /* How bold our text is */
    text-align: center;
    font-family: sans-serif;
  }

  p {
    font-size: 1.5em;
    margin-left: 20%;
    margin-right: 20%;
    text-align: center;
    font-family: sans-serif;
  }
</style>

<div>
  <div class="Title-container">
    <h1>Africa Resource Map</h1>
    <p>This is text, talking about mineral resource in africa. Africa is actually really resource rich according to research which I shall show. Want to learn more looking at different regions? Scroll around the tree mpa below and try out the filters.</p>
  </div>
  <div class="viz-container">
  <Tree />
  </div>
</div>
