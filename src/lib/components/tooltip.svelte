<script>
    export let data;
    export let cScale;
    export let width;

    import { fly, fade } from "svelte/transition"
    import { readable } from 'svelte/store';

    let xPos;
    let yPos;

    function mouseCoordinates(event){

      xPos= event.pageX;
      yPos= event.pageY;

    }

    window.addEventListener('mousemove', mouseCoordinates);


    let tooltipWidth;

    const xNudge = 2;
    const yNudge = 20;

    // If the x position + the tooltip width exceeds the chart width, offset backward to prevent overflow
    $: xPosition =
      data.parent.x0 + tooltipWidth + xNudge > width
      ? data.parent.x0 - tooltipWidth - xNudge
      : data.parent.x0;

    $: yPosition = data.parent.y0 + yNudge;



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
    z-index: 2;
  }

  h1 {
      margin: 0;
      font-size: 1rem;
      font-weight: 500;
      margin-bottom: 3px;
  }

  .info {
    display: flex;
    justify-content: space-between;
    column-gap: 8px;
  }

  .resource {
    font-size: 0.8rem;
  }

  .unit {
    font-size: 0.65rem;
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
  style="left:{xPos/2}px; top:{yPos/2}px;"
  bind:clientWidth={tooltipWidth}>
  <!-- Country name -->
  <h1>
    {data.data.Name}
  </h1>
  <!-- Additional info under the country name -->
  <div class='info'>
      <span class="resource">
          {data.parent.data.name}
      </span>
      <span class="unit" style="background: {cScale(data.parent.parent.data.name)}">
          {"$"+data.data.Value.toLocaleString("en-US")}
      </span>
  </div>

</div>
