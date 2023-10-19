<script>
    import { fly, fade } from "svelte/transition";
    export let width;
    export let data;
    export let colorScale;
    let tooltipWidth;

    const yNudge = 10;
    const xNudge = 10;
    $: xPosition = data.x + tooltipWidth > width ? data.x - tooltipWidth - xNudge : data.x + xNudge;
    $: yPosition = data.y + yNudge;
</script>

<div
    class='tooltip'
    in:fly={{ y: 10, duration: 200, delay: 50}}
    out:fade
    bind:clientWidth={tooltipWidth}
    style="
        top: {yPosition}px;
        left: {xPosition}px;
    "
    >
    <h2>{data.country}</h2>
    <div class='info'>
        <p class='score'><span style="font-weight: 600;">Score:</span> {data.happiness}/10</p>
        <p class='continent'
            style="background: {colorScale(data.continent)}"
        >{data.continent}</p>
        <span class='bar background'></span>
        <span class='bar foreground'
            style="
                background: {colorScale(data.continent)};
                width: {data.happiness * 10}%;
            "
        ></span>
    </div>
</div>

<style>
.tooltip {
    position: absolute;
    background: white;
    box-shadow: 2px 3px 8px rgba(0,0,0,0.15);
    padding: 0.7rem 0.5rem;
    border-radius: 0.1rem;
    pointer-events: none;
    font-size: 0.7rem;
    transition: top 300ms ease, left 300ms ease;
}
h2 {
    font-size: 0.9rem;
    font-weight: 600;
    margin-bottom: 0.25rem;
}
.info {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    column-gap: 0.5rem;
    align-items: center;
}
.continent {
    font-size: 0.6rem;
    padding: 0.2rem;
    color: white;
    text-transform: uppercase;
}
.bar {
    position: absolute;
    bottom: 0;
    left: 0;
    height: 3px;
    width: 100%;
}
.background {
    background: #eee;
}
</style>
