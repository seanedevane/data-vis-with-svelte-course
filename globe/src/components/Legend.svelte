<script>
import { format } from 'd3-format';
export let colorScale;
export let data;


const minColor = colorScale.range()[0];
const maxColor = colorScale.range()[1];

const minValue = colorScale.domain()[0];
const maxValue = colorScale.domain()[1];

$: percentOfMax = (data?.population / maxValue) * 100;

// some re-use here, would normally make this a utility class, but it's a demo app, so we're here.
const suffixFormat = (d) => format(".2s")(d).replace("G", "B")
</script>

<div class='legend'>
    <div class='bar-container'>
    <span class='label'>
        {minValue}
    </span>
    <div class='bar'
        style:background="linear-gradient(to right, {minColor} 0%, {maxColor} 100% )"
    >

    {#if percentOfMax}
    <span class='line' style:left="{percentOfMax + '%'}"/>
    {/if}
    </div>
    <span class='label'>
        {suffixFormat(maxValue)}
    </span>
    </div>
    <p>Population (in billions)</p>
</div>

<style>
.legend {
    margin-top: 1rem;
}
.bar-container {
    width: 100%;
    display: flex;
    gap: 0.5rem;
}
.bar {
    position: relative;
    height: 15px;
    width: 100%;
    background: white;
}
.label {
    color: white;
    font-size: 0.85rem;
    user-select: none;
}
.line {
    position: absolute;
    top: 0;
    height: 15px;
    width: 2px;
    background: white;
    transition: left 500ms cubic-bezier(1, 0, 0, 1);
}
p {
    color: rgba(255,255,255,0.4);
    text-align: center;
    text-transform: uppercase;
    font-size: 0.8rem;
    font-weight: 400;
    margin-top: 0.25rem;

}
</style>