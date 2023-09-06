<script>
    export let data;
    export let cScale;
    export let width;

    import { fly, fade } from "svelte/transition"

    let tooltipWidth;

</script>


<style>
  .tooltip {
    position: absolute;
    padding: 8px 6px;
    background: white;
    box-shadow: rgba(0, 0, 0, 0.15) 2px 3px 8px;
    border-radius: 3px;
    pointer-events: none;
    min-width: 130px;
    transition: top 300ms ease, left 300ms ease;
  }

  h1 {
      margin: 0;
      font-size: 1rem;
      font-weight: 500;
      margin-bottom: 3px;
  }

  h2 {
    font-size: 0.8rem;
  }

  .info {
    display: flex;
    justify-content: space-between;
    column-gap: 8px;
    flex-direction: column;
  }

  .resource {
    font-size: 0.8rem;
  }

  .unit {
    font-size: 1.65rem;
    color: #ffffff;
    padding: 3px 4px 2px 4px;
    border-radius: 3px;
    text-transform: uppercase;
    white-space: nowrap;
  }
</style>

<div
  class="tooltip"
  in:fly={{ y: 10, duration: 200, delay: 200 }}
  out:fade
  style="left:800px; top:40px;"
  bind:clientWidth={tooltipWidth}>
  <!-- Country name -->
  <!-- Additional info under the country name -->
  <div class='info'>
    <h1>Information:</h1>
    <h2>Region:</h2>{data.parent.parent.data.name}
    <h2>Country:</h2>{data.parent.data.name}
    <h2>Resource:</h2>{data.data.Name}
    <h2>Value of Resource:</h2>{"$"+data.data.Value.toLocaleString("en-US")}
  </div>

</div>
