<script>
  import * as d3 from 'd3';
  import { scaleBand, scaleLinear, scaleLog } from "d3-scale";
  import { max } from "d3-array";

  import Data from '$lib/data/Mineral_Minining_Africa_Data.json';
  import Tdata from '$lib/data/test.json';
  import Tooltip from "$lib/components/tooltip.svelte";
  import Infotip from "$lib/components/info.svelte";
  import Filter from "$lib/components/filter.svelte";

  // set the dimensions and margins of the graph
  var margin = {top: 20, right: 20, bottom: 20, left: 20},
  width = 900 - margin.left - margin.right,
  height = 800 - margin.top - margin.bottom;

  let hoverData;
  let res=[];
  $: trial = filteredata(selectregion, selectcountry, selectres);
  $: filter_cntry = filtercountry(selectregion);
  $: filter_res = filterresources(selectregion, selectcountry);

  //set regions for colour scale
  var regions = ["Central Africa", "Western Africa", "Eastern Africa", "Southern Africa", "Northern Africa"];

  let selectregion = ["Central Africa", "Western Africa", "Eastern Africa", "Southern Africa", "Northern Africa"];

  let selectcountry = ["Algeria","Angola","Benin","Botswanna","Burkina Faso","Burundi","Cameroon","Cape Verde",
  "Central African Republic","Chad","Congo D.R.","Congo Rep","Cote D'Ivoire","Egypt","Eritrea","Eswatini","Ethiopia",
  "Gabon","Ghana","Guinea","Kenya","Lesotho","Liberia","Libya","Madagascar","Malawi","Mali","Mauritania","Mauritius",
  "Morocco","Mozambique","Namibia","Niger","Nigeria","Papua New Guinea","Rwanda","Senegal","Sierra Leone","South Africa",
  "South Sudan","Sudan","Tanzania","Togo","Tunisia","Uganda","Zambia","Zimbabwe"];

  let selectres = ["Aluminium","Arsenic","Baryte","Bauxite","Bentonite","Beryllium","Chromium","Cobalt","Coking Coal",
  "Copper","Diam. (Gem)","Diam. (Ind)","Diatomite","Feldspar","Fluorspar","Gold","Graphite","Gypsum","Iron","Kaolin",
  "Lead","Lithium","Magnesite","Manganese","Mercury","Nat. Gas","Nickel","Niobium","Palladium", "Perlite","Petroleum",
  "Phosphates","Platinum","Rare Earths","Rhodium","Salt","Silver","Steam Coal","Sulfur","Talc","Tantalum","Tin",
  "Titanium","Tungsten","Uranium","Vanadium","Vermiculite","Zinc","Zircon"];

  //filteres data for the upload to d3 treemap and returns final tree map data
  function filteredata(region,country,resource){

  let tri = {"children":[], "name":"Africa"}

  Tdata.children.forEach(function(element) {
    if (region.includes(element.Name) === true) {
      if(!tri.children.includes(element.Name)) {
        tri.children.push({"name":element.Name, "children":[]})
      }

      element.children.forEach(function(ctry) {
        tri.children.forEach(function(ele) {
          if(ele.name === element.Name && country.includes(ctry.Name) === true && !ele.children.includes(ctry)) {
            ele.children.push({"name":ctry.Name, "children":[]})
          }

          ctry.children.forEach(function(res) {
            ele.children.forEach(function(e) {
              if(e.name === ctry.Name && resource.includes(res.Name) === true && !e.children.includes(res)) {
                e.children.push(res)
              }
            })
          })
        })
      })
    }
  })

  let finaldata = treedata(tri)

  return finaldata
}

//filters country list based on selected regions
function filtercountry(region) {
  let tri2 = [];

  Tdata.children.forEach(function(element) {
    if (region.includes(element.Name) === true) {
      element.children.forEach(function(ctry) {
        tri2.push(ctry.Name)
      })
    }
  })

  return tri2;
};

//filters resources list based on countries selected

function filterresources(region, country) {
  let tri3 = [];

  Tdata.children.forEach(function(element) {
    if(region.includes(element.Name) === true) {
      element.children.forEach(function(cntry) {
        if(country.includes(cntry.Name) === true) {
          cntry.children.forEach(function(res) {
            if(!tri3.includes(res.Name)) {
              tri3.push(res.Name)
            }
          })
        }
      })
    }
  })

  return tri3;
}

//sets the data recieved within a treemap readable format
function treedata(data) {
  // Here the size of each leave is given in the 'value' field in input data
  var treeroot = d3.hierarchy(data).sum(function(d) { return d.Value})
//here we use the d3 treemap function to prep the re-organized data for a treemap
  let ftree = d3.treemap()
  .size([800, 500])
  .paddingInner(2)
  .paddingTop(5)
  .paddingRight(2)
  (treeroot);

  return ftree;
}


  //create scales
  $: xScale = scaleLinear().domain([0,600]).range([0, width]);
  $: yScale = scaleLinear().domain([0,600]).range([0, height]);
  $: oScale = scaleLinear().domain([0,10000000]).range([0.3,1]);
  $: cScale = d3.scaleOrdinal().domain(regions).range([ "#402D54", "#D18975", "#8FD175","#b22234","#012169"])


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
    font-size: 1.35rem; /* How big our text is */
    margin: 0 0 0.5rem 0; /* Adds a bottom margin */
    font-weight: 600; /* How bold our text is */
    text-align: center;
    font-family: sans-serif;
  }

  h3 {
    font-family: sans-serif;
  }

  p {
    font-size: 1.5em;
    margin: 20%;
    text-align: center;

    font-family: sans-serif;
  }

  svg {
    display: block;
    margin: auto;
  }

  .vis {
    display: flex;
    margin: auto;
    align-items: center;
    justify-content: center;
    padding: 0;
  }

  .chart-container {


  }
  rect {
    transition:
      width 200ms cubic-bezier(0.76, 0, 0.24, 1),
      height 200ms cubic-bezier(0.76, 0, 0.24, 1),
      opacity 300ms ease,
      fill 300ms ease;
    cursor: pointer;
  }

  button::before {
    position: absolute;
    right: 0;
    top: 0;
    height: inherit;
    aspect-ratio: 1;
    background: black;
    display: flex;
    align-items: center;
    padding-right: 1rem;
    content: "";
    clip-path: polygon(50% 25%, 20% 80%, 80% 80%);
    transform: rotate(180deg) scale(0.75);
  }

  .drop-menu {
    display: flex;
    flex-direction: row;
    justify-content: space-evenly;
    margin-bottom: 5%;
  }

  .drop-select {
    text-align: center;
  }

  .dropbtn {
    background-color: #A0522D;
    color: white;
    padding: 16px;
    font-size: 16px;
    border: none;
    cursor: pointer;
    font-family: sans-serif;
    border-radius: 25px;
    padding-left: 4rem;
    padding-right: 4rem;
  }

  .dropdown {
    position: relative;
    display: inline-block;
  }

  .dropdown-content {
    display: none;
    padding-left: 0;
    padding-top: 1rem;
    margin-top: 0;
    list-style: none;
    overflow: hidden;
    text-align: right;
    text-align: center;
    transition: all 0.4s ease-out;
    width: 100%;
    position: absolute;
    z-index: 1;
    height: 25vh;
    overflow: auto;
  }

  .dropdown-content li {
    border-radius: 25px;
    position: relative;
    display: inline-block;
    line-height: 1.5;
    width: 100%;
    margin: 0 0 0.25rem 0;
    background: #DEB887;
    transition: background 3s;
    box-shadow: 2px 2px 10px -2px rgba(0, 0, 0, 0.35);
  }

  .dropdown-content li a {
    display: block;
    color: inherit;
    font-family: sans-serif;
    text-transform: lowercase;
    font-weight: 200;
    text-decoration: none;
    border-radius: 25px;
  }

  .dropdown-content a:hover {background-color: #f1f1f1}

  .dropdown:hover .dropdown-content {
    display: block;
  }

  .dropdown:hover .dropbtn {
    background-color: #8B4513;
  }
</style>
<div class="drop-menu">
  <div class="drop-select">
    <h3>Select Your Region</h3>
    <div class="dropdown">
      <button class="dropbtn">Dropdown</button>
      <div class="dropdown-content">
      {#each regions as r}
        <li><a>
          <input type="checkbox" bind:group={selectregion} value={r} />
          {r}
        </a></li>
      {/each}
      </div>
    </div>
  </div>

  <div class="drop-select">
  <h3>Select Your Country</h3>
    <div class="dropdown">
      <button class="dropbtn">Dropdown</button>
      <div class="dropdown-content">
      {#each filter_cntry as c}
        <li><a>
          <input type="checkbox" bind:group={selectcountry} value={c} />
          {c}
        </a></li>
      {/each}
      </div>
    </div>
  </div>

  <div class="drop-select">
  <h3>Select Your Resources</h3>
    <div class="dropdown">
      <button class="dropbtn">Dropdown</button>
      <div class="dropdown-content">
      {#each filter_res as res}
        <li><a>
          <input type="checkbox" bind:group={selectres} value={res} />
          {res}
        </a></li>
      {/each}
      </div>
    </div>
  </div>
</div>


{#if selectregion.length < 1}
  <h1> Please select a region</h1>
  {:else}
  <div class="vis">
    <div class="chart-container"
      bind:clientWidth={width}
      on:mouseleave={() => {
        hoverData = null;
        }}>
      <svg {width} {height}>
        {#each trial.leaves() as d}
          <rect
            class="resource"
            y={yScale(d.y0)}
            x={xScale(d.x0)}
            width={xScale(d.x1)-xScale(d.x0)}
            height={yScale(d.y1)-yScale(d.y0)}
            fill={cScale(d.parent.parent.data.name)}
            opacity={oScale(d.data.Value)}
            stroke="black"
            on:mouseover={() => {
            hoverData = d
            }}
            on:focus={() => {
            hoverData = d
            }}
            tabIndex="0"
          />

          <text class="chart-txt"
              text-anchor="middle"
              font-family="Sans-serif"
              x={xScale(d.parent.parent.x0)+50}
              dy="0.3em"
              y={yScale(d.parent.parent.y0)+5}
              fill={cScale(d.parent.parent.data.name)}>
              {d.parent.parent.data.name}
            </text>
        {/each}
      </svg>
      {#if hoverData}
      <Tooltip data={hoverData} {cScale} {width}/>
      <Infotip data={hoverData} {cScale} {width}/>
      {/if}
    </div>
    <!-- <div class="info_container">
      <h1> check something</h1>
      {#if hoverData}
        <h2>{hoverData.parent.data.Name}</h2>
      {/if}
    </div> -->
  </div>
  {/if}
