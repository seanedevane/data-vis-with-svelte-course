<script>
    export let width;
    export let yScale;
    import { fade } from 'svelte/transition';
    let yTicks = yScale.ticks(4);
</script>

<g class='axis y'>
{#each yTicks as tick, index}
    <g class='tick' transform="translate(0 {yScale(tick)})" transition:fade={{ delay: index * 100 }}>
        <line x1={0} y1={0} y2={0} x2={width} stroke={index === 0 ? '#8f8f8f' : '#e5e7eb'} />
        <text y={-3}>{index === yTicks.length - 1 ? `${tick} hours studied` : tick}</text>
    </g>
{/each}
</g>

<style></style>