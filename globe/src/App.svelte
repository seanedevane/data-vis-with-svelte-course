<script>
  import data from "$data/data.json";
  import world from "$data/world-110m.json";
  import Tooltip from "$components/Tooltip.svelte";
  import Legend from "$components/Legend.svelte";
  import { geoOrthographic, geoPath } from "d3-geo";
  import * as topojson from "topojson-client";
  import Glow from "$components/Glow.svelte";
  import { scaleLinear } from "d3-scale";
  import { max } from "d3-array";
  import { timer } from 'd3-timer';
  import { drag } from 'd3-drag';
  import { onMount } from "svelte";
  import { select } from 'd3-selection';
  import { spring } from 'svelte/motion';
  import { geoCentroid } from "d3-geo";
  import { draw } from 'svelte/transition';
  import { onClickOutside, writable } from '@svelte-use/core';
  let countries = topojson.feature(world, world.objects.countries).features;
  let borders = topojson.mesh(world, world.objects.countries, (a, b) => a !== b);
  
  // Rebuild the countries array to include the population info in data.JSON
  countries.forEach(country => {
    const metadata = data?.find(d => d.id === country.id );
    // console.log(metadata);
    if (metadata) {
      country.population = metadata.population;
      country.country = metadata.country;
      // console.log(metadata);
    }
  });
  // console.log(data, countries);
  
  let width = 400;
  $: height = width;

  $: projection = geoOrthographic()
    .scale(width / 2) // size of projection
    .rotate([$xRotation, $yRotation, 0]) // the position of the projection's display (3D globe)
    .translate([width / 2, height / 2]); // influences where the projection is centered

  let xRotation = spring(0, { stiffness: 0.08, damping: 0.5});
  let yRotation = spring(0, { stiffness: 0.1, damping: 0.7});


  let degreesPerFrame = 1;
  const DRAG_SENSITIVITY = 0.5;

  const t = timer((elapsed) => {
    if (dragging || tooltipData) { return }
    $xRotation += degreesPerFrame;
  }, 0);
  $: path = geoPath(projection);

  // get our SVG globe element, bound in the DOM.
  let globe;
  let dragging = false;

  onMount(() => {
    const element = select(globe);
    // drag functionality
    element.call(drag().on('drag', (event) => {

      $xRotation = $xRotation + event.dx * DRAG_SENSITIVITY;
      $yRotation = $yRotation - event.dy * DRAG_SENSITIVITY;
      dragging = true
  }
  ).on('end', () => {
    dragging = false;
  }));
});

  const colorScale = scaleLinear()
    .domain([0, max(data, d => d.population)])
    .range(['#26362e', '#0dcc6c' ])

    let tooltipData;
  // reactive property to center the globe on the currently clicked/hovered tooltip country. geoCentroid needs a negative value for each axis to work here.
  $: if (tooltipData) {
    const center = geoCentroid(tooltipData);
    // projection.rotate([-center[0], -center[1]])
    $xRotation = -center[0];
    $yRotation = -center[1];
  }

  // handling clicks outside of the chart container to hide the tooltip with https://svelte-use.vercel.app/core/onClickOutside/
  const target = writable()

  onClickOutside(target, (event) => {
    tooltipData = null;
  })
</script>

<!-- allowing clicks outside of the chart container to clear the tooltip with a listener on body-->
<!-- <svelte:body on:click={() => {
  tooltipData = null;
}}
/> -->
<div class='title-container'>
  <h1>
    World Population at a Glance
  </h1>
  <h2>
    By Country, 2021
  </h2>
</div>
  <div class='chart-container' bind:clientWidth={width} bind:this={$target}>
    <svg {width} {height} bind:this={globe}
      class:dragging
    >
      <Glow />
      <!-- svelte-ignore a11y-click-events-have-key-events -->
      <circle
        cx={width / 2}
        cy={height / 2}
        r={width / 2}
        fill="#05003e"
        filter="url(#glow)"
        on:click={() => {
          tooltipData = null;
        }}
        on:focus={() => {
          tooltipData = null;
        }}
      />

      {#each countries as country}
        <!-- svelte-ignore a11y-no-noninteractive-tabindex -->
        <!-- svelte-ignore a11y-click-events-have-key-events -->
        <path d={path(country)} fill={colorScale(country?.population || 0)} stroke="none"
          on:click={(event) => {
          tooltipData = country;
        }}
          on:focus={(event) => {
            tooltipData = country;
            event.stopPropagation();
          }}
          tabindex="0"
        />
      {/each}
      <path d={path(borders)} fill="none" stroke="black"/>
      {#if tooltipData}
          {#key tooltipData.id}
          <path 
            d={path(tooltipData)}
            in:draw
            stroke="white"
            fill="none"
            stroke-width="2"
          />
          {/key}
      {/if}
    </svg>
    <Tooltip data={tooltipData }/>
    <Legend {colorScale} data={tooltipData}/>
  </div>

<style>
  :global(body) {
    background: rgb(1,0,19);
  }
  .chart-container {
    margin: 0 auto;
    max-width: 468px;
  }
  svg {
    overflow: visible;
  }
  svg:hover, svg:focus {
    cursor: grab;
  }
  path {
    cursor: pointer;
  }
  path:focus {
    outline: none; /* non-standard thing to do for accessibility, however here this focused element already displays an outline */
  }
  .dragging, .dragging:hover, .dragging:focus {
    cursor: grabbing;
  }
  main {
    text-align: center;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    background: #f0f0f0;
  }
  h1, h2 {
    text-align: center;
  }
  .title-container {
    margin-top: 3rem;
  }
  h1 {
    font-size: 3rem;
    margin-bottom: 1rem;
    font-weight: 700;
    color: white;
  }

  h2 {
    font-size: 1.5rem;
    color: #aaa;
    margin-bottom: 2rem;
    line-height: 1.5;
  }

  pre {
    padding: 1px 6px;
    display: inline;
    margin: 0;
    background: #ffb7a0;
    border-radius: 3px;
  }

  a {
    color: #ff3e00;
    text-decoration: inherit;
  }

  footer {
    font-size: 1rem;
    color: #333;
  }
</style>
