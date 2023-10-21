<script>
export let data;

import { format } from 'd3-format';
import { fade } from 'svelte/transition';

const suffixFormat = (d) => format(".2s")(d).replace("G", "B")
</script>

{#if data?.country}
    <div class='tooltip' transition:fade >
        <h2>{data.country}</h2>
        <h3>Population: {suffixFormat(data.population)}</h3>
    </div>
{/if}

<style>
.tooltip {
    color: white;
    position: absolute;
    bottom: 50px;
    right: 0;
    text-align: right;
}
h2 {
    font-size: 1.5rem;
    font-weight: 700;
    margin-bottom: 0.25rem;
}
h3 {
    font-size: 1.15rem;
    font-weight: 200;
    color: rgba(255,255,255, 0.7);
}
</style>