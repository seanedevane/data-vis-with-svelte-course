<script>
import { fly } from "svelte/transition";
export let data;
export let xScale;
export let yScale;
export let width;

let tooltipWidth;

$: x = xScale(data.grade);
$: y = yScale(data.hours);

$: console.log({ x, y })

const xNudge = 15;
const yNudge = 40;

$: xPosition = tooltipWidth + x > width ? x - tooltipWidth - xNudge : x + xNudge;
$: yPosition = y + yNudge;
</script>

<div class="tooltip"
    style="left: {xPosition}px; top: {yPosition}px"
    bind:clientWidth={tooltipWidth}
    in:fly={{ y: -20 }}
    out:fly={{ y: 20 }}
>
  <h1>{data.name} <span>{data.grade}%</span></h1>
  <h2>{data.hours} hours studied</h2>
</div>

<style>
.tooltip {
    position: absolute;
    background-color: white;
    padding: 8px 6px;
    box-shadow: rgba(0,0,0,0.15) 2px 3px 8px;
    border-radius: 3px;
    pointer-events: none;
    transition: top 300ms ease, left 300ms ease;;
}

h1 {
    font-size: 1rem;
    font-weight: 500;
    margin-bottom: 0.3rem;
    width: 100%;
}
h2 {
    font-size: 0.8rem;
    font-weight: 300;
    text-transform: uppercase;
}
span {
    font-size: 80%;
    padding: 3px 4px;
    background: #dda0dd50;
    margin-left: 2px;
    display: inline-block;
    vertical-align: middle;
    border-radius: 3px;
    color: rgba(0,0,0,0.7);
}
</style>