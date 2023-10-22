<script>
  import AxisX from "$components/AxisX.svelte";
  import AxisY from "$components/AxisY.svelte";
  import Tooltip from "$components/Tooltip.svelte";
  import { fly } from "svelte/transition";
  import data from "$data/data.js";
  console.log(data);
  import { scaleLinear } from "d3-scale";
  import { max } from 'd3-array';
  
  const margin = {
    top: 20,
    right: 15,
    bottom: 20,
    left: 0
  };

  let width = 400;
  $: console.log({width})
  $: innerWidth = width - margin.left - margin.right
  $: xScale = scaleLinear()
    .domain([0,100])
    .range([0, innerWidth])

  let height = 400;
  let innerHeight = height - margin.top - margin.bottom
  let yScale = scaleLinear()
    .domain([0, max(data, d=> d.hours)])
    .range([innerHeight, 0])

  let hoveredData;
  $: console.log(hoveredData);
</script>
  <h1>Hours studied vs. Final grade</h1>
  <div class='chart-container' bind:clientWidth={width}>
    <svg {width} {height}>
      <g class='inner-chart' transform="translate({margin.left} {margin.top})">
        <AxisY {yScale} width={innerWidth} />
        <AxisX {xScale} height={innerHeight} width={innerWidth}/>
      {#each data.sort((a, b) => a.grade - b.grade) as d}
        <circle 
        in:fly={{ x: -20, opacity: 0, duration: 500 }}
        cx={xScale(d.grade)}
        cy={yScale(d.hours)}
        r={hoveredData == d ? 20 : 10}
        opacity={hoveredData ? (hoveredData == d ? 1 : 0.45): 1}
        fill="purple"
        stroke="black"
        stroke-width={1}
        on:mouseover={() => {hoveredData = d}}
        on:focus={() => { hoveredData = d}}
        on:mouseleave={() => hoveredData = null}

        />
      {/each}
      </g>
    </svg>
    {#if hoveredData}
    <Tooltip 
      data={hoveredData} 
      {xScale}
      {yScale}
      width={innerWidth}
    />
    {/if}
  </div>

<style>
  circle {
    transition: r 300ms ease, opacity 500ms ease;
    cursor: pointer;
  }
  :global(.tick text, .axis-title) {
    font-weight: 400;
    font-size: 0.9rem;
    fill: hsla(212, 10%, 53%, 1);
  }
  .y {
    height: 100%;
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

  h1 {
    font-size: 2.5rem;
    margin-bottom: 1rem;
    font-weight: 600;
  }

  h2 {
    font-size: 1.5rem;
    color: #333;
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
