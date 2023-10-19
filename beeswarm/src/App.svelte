<script>
  import data from "$data/data.js";
  import AxisX from "$components/AxisX.svelte";
  import AxisY from "$components/axisY.svelte";
  import Legend from "$components/Legend.svelte";
  import Tooltip from "$components/Tooltip.svelte";
  import { forceSimulation, forceY, forceX, forceCollide } from "d3-force";
  import { scaleLinear, scaleBand, scaleOrdinal, scaleSqrt } from "d3-scale";
  import { group, mean, rollups } from "d3-array";
  import { fade } from "svelte/transition";
  console.log(data);

  let width = 400;
  let height = 400;
  const margin = {
    top: 0,
    right: 0,
    bottom: 30,
    left: 0
  }

    // serves to average the happiness scores of each continent
    const continents = rollups(
      data,
      v => mean(v, d => d.happiness),
      d => d.continent
    )
    .sort((a, b) => a[1] - b[1]) // sorts the above based on value
    .map(d => d[0]) // then gets the continent name

  $: innerWidth = width - margin.left - margin.right;
  let innerHeight = height - margin.top - margin.bottom;
  const RADIUS = 5
  $: xScale = scaleLinear().domain([0, 10]).range([0, innerWidth])
  let yScale = scaleBand().domain(continents).range([innerHeight, 0]).paddingOuter(0.5)

  const countrySim = forceSimulation(data);
  // have only the simulation forces be reactive
  $: {
    countrySim.force("x", forceX().x(d => xScale(d.happiness)).strength(0.05))
    .force("y", forceY().y((d) => (groupByContinent ? yScale(d.continent) : innerHeight / 2)))
    .force("collide", forceCollide().radius(d => circleSize(d.happiness)))
    .alpha(0.8)
    .alphaDecay(0.005)
    .restart();
  }

  let nodes = [];
  $: countrySim.on('tick', () => {
    nodes = countrySim.nodes();
  });

  // Generate colors for each continent
  const colorScale = scaleOrdinal()
      .domain(continents)
      .range(['#dda0dd', '#fe7f2d', '#fcca46', '#a1c181', '#619b8a', '#9f9979'])

  $: circleSize = scaleSqrt()
      .domain([1, 9]) // same as the xScale value in this case
      .range(width < 568 ? [2, 6] : [3, 8]);

  let hovered;
  let hoveredContinent;
  let groupByContinent = false;
  $: console.log(groupByContinent)
</script>

<h1>Happiness Index by Country</h1>
<Legend {colorScale} bind:hoveredContinent/>
<div class='chart-container' bind:clientWidth={width}
  on:click={() => {
    groupByContinent = !groupByContinent;
    hovered = null;
  }}
  on:keypress={() => {
    groupByContinent = !groupByContinent;
    hovered = null;
  }}
>
  <svg {width} {height}
    on:mouseleave={() => {hovered = null;} }
  >
    <AxisX {xScale} height={innerHeight} width={innerWidth}/>
    <AxisY {yScale} {groupByContinent} />
    {#if hovered}
      <line
        transition:fade
        x1={hovered.x}
        x2={hovered.x}
        y1={innerHeight}
        y2={hovered.y}
        stroke={colorScale(hovered.continent)}
        stroke-width="2"
      />
    {/if}
    <g class='inner-chart' transform="translate({ margin.left}, {margin.top})">
      {#each nodes as node, index}
        <!-- svelte-ignore a11y-no-noninteractive-tabindex -->
        <circle
          in:fade={{ delay: 200 + index * 7 }}
          cx={node.x}
          cy={node.y}
          r={circleSize(node.happiness)}
          fill={colorScale(node.continent)}
          stroke={hovered || hoveredContinent ?
            hovered === node || hoveredContinent === node.continent
            ? "black"
            : "transparent"
          : "#00000090"}
          opacity={hovered || hoveredContinent ?
            hovered === node || hoveredContinent === node.continent
            ? 1
            : 0.3
          : 1 }
          on:mouseover={() => {
            hovered = node;
          }}
          on:focus={() => {
            hovered = node
          }}
          tabindex="0"
          on:click={(event) => {
            event.stopPropagation();
          }}
          on:keypress={(event) => {
            event.stopPropagation();
          }}
        />
      {/each}
    </g>
  </svg>
  {#if hovered}
  <Tooltip data={hovered} {colorScale} width={innerWidth} />
  {/if}
</div>


<style>
  :global(.tick text, .axis-title) {
    font-size: 0.8rem;
    font-weight: 400;
    fill: hsla(215, 10%, 53%, 1);
    user-select: none;
  }
  :global(.axis-title) {
    font-size: 1rem;
    font-weight: 400;
  }
  circle {
    transition: stroke 300ms ease, opacity 300ms ease;
    cursor: pointer;
  }
  line {
    transition: all 300ms ease;
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
    font-size: 2rem;
    margin-bottom: 1rem;
    font-weight: 700;
    color: hsla(215, 10%, 25%, 1);
    text-align: center;
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
