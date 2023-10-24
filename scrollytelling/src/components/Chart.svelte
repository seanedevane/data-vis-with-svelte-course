<script>
    import AxisX from "$components/AxisX.svelte";
    import AxisY from "$components/AxisY.svelte";
    import Tooltip from "$components/Tooltip.svelte";
    import { fly } from "svelte/transition";
    export let width;
    export let height;
    export let hoveredData;
    export let renderedData;
    export let margin;
    export let currentStep;
    export let xScale;
    export let yScale;
    export let radius;
</script>
<div class='sticky'>
    <div class='chart-container' bind:clientWidth={width} bind:clientHeight={height} on:mouseleave={() => {hoveredData = null; }}>
        <svg {width} {height}>
        <g class='inner-chart' transform="translate({margin.left} {margin.top})">
            {#if currentStep > 1 }
            <AxisY {yScale} width={innerWidth} />
            {/if}
            {#if currentStep > 0}
            <AxisX {xScale} height={innerHeight} width={innerWidth}/>
            {/if}
        {#each renderedData as d}
            <!-- svelte-ignore a11y-no-noninteractive-tabindex -->
            <circle 
            in:fly={{ x: -20, opacity: 0, duration: 500 }}
            cx={xScale(d.grade)}
            cy={yScale(d.hours)}
            r={hoveredData == d ? radius * 2 : radius}
            opacity={hoveredData ? (hoveredData == d ? 1 : 0.45): 1}
            fill="purple"
            stroke="black"
            stroke-width={1}
            on:mouseover={() => {
            (currentStep >= 2 ? (hoveredData = d) : null )}}
            on:focus={() => { (currentStep >= 2 ? (hoveredData = d) : null )}}
            on:mouseleave={() => hoveredData = null}
            tabindex="0"
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
</div>
<style>
    :global(.tick text, .axis-title) {
        font-weight: 400;
        font-size: 0.9rem;
        fill: hsla(212, 10%, 53%, 1);
    }
    .sticky {
        position: sticky;
        top: 5vh; /* centering baesd on the 90vh value */
        z-index: 1;
        height: 90vh;
        margin-bottom: 1rem;
        display: flex;
        justify-content: center;
        align-items: center;
  }
 
    .chart-container {
        height: 100%;
        width: 100%;
        max-width: 800px;
        max-height: 450px;
        background: white;
        border: 1px solid plum;
        border-radius: 5px;
        box-shadow: 1px 1px 30px rgba(252, 220, 252, 0.6);
    }
    circle {
        transition: r 300ms ease, opacity 500ms ease, cx 500ms cubic-bezier(0.76, 0, 0.24, 1), cy 500ms cubic-bezier(0.76, 0, 0.24, 1);
        cursor: pointer;
      }
</style>