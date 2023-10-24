<script>
    export let xScale;
    export let height;
    export let width;
    import { fade } from 'svelte/transition';
    let xTicks = [0, 25, 50, 75, 100];
</script>
<g class='axis x' transform="translate(0 {height})">
    {#each xTicks as tick, index}
    <g class='tick' transform='translate({xScale(tick)} 0)' transition:fade={{ delay: index * 100 }}>
        <line x1={0} x2={0} y={0} y2={6} stroke="hsla(212, 10%, 53%, 1)" />
        <text   text-anchor={index === 0 ? "start" : "middle" }
                y={6}
                dy={9}
                dominant-baseline="middle"
                >
            {tick}%
        </text>

    </g>
    {/each}
    <text class="axis-title"
        y={-9}
        x={width}
        text-anchor="end"
        transition:fade={{ delay: xTicks.length * 100 + 100 }}
        >
    Final Grade &rarr;
    </text>
</g>

<style></style>