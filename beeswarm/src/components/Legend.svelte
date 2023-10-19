<script>
    export let colorScale;
    export let hoveredContinent;

</script>

<div class='legend' on:mouseleave={() => { hoveredContinent = null; }}>
    {#each colorScale.domain() as continent}
        <!-- svelte-ignore a11y-mouse-events-have-key-events -->
        <p
            on:mouseover={() => {
                hoveredContinent = continent;
            }}
            class:unhovered={hoveredContinent && hoveredContinent !== continent}
        >
            <span style="background-color: {colorScale(continent)}" />
            {continent}
        </p>
    {/each}
</div>

<style>
    .unhovered {
        opacity: 0.3;
    }
    .legend {
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;
        flex: 1 auto;
        justify-content: center;
        gap: 0.25rem 0.75rem;
        margin-bottom: 0.25rem;
    }
    .legend p {
        /* margin: 0 0.5rem; */
        color: hsla(212, 10%, 40%, 1);
        text-transform: uppercase;
        font-size: 0.8rem;
        align-items: center;
        display: flex;
        column-gap: 0.1rem;
        transition: opacity 300ms ease;
        cursor: pointer;
    }
    .legend p span {
        width: 10px;
        height: 10px;
        display: inline-block;
        border-radius: 50%;
        margin-right: 0.3rem;
        border: 1px solid rgba(0,0,0,.5);
    }

</style>