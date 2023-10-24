<script>
  import Chart from "$components/Chart.svelte";
  import Steps from "$components/Steps.svelte";
  import TextContent_1 from "$components/TextContent-1.svelte";
  import TextContent_2 from "./components/TextContent-2.svelte";
  import data from "$data/data.js";
  console.log(data);
  import { scaleLinear } from "d3-scale";
  import { max } from 'd3-array';



  const margin = {
    top: 25,
    right: 25,
    bottom: 30,
    left: 10
  };

  const radius = 10;

  let width = 600;
  $: console.log({width})
  $: innerWidth = width - margin.left - margin.right
  $: xScale = scaleLinear()
  .domain([0,100])
  .range([0, innerWidth])

  let height = 400;
  $: innerHeight = height - margin.top - margin.bottom
  $: yScale = scaleLinear()
  .domain([0, max(data, d=> d.hours)])
  .range([innerHeight, 0])

  let hoveredData;
  $: console.log(hoveredData);
  let currentStep;
  console.log(currentStep);
  let initialData = data.sort((a, b) => a.grade - b.grade);
  let renderedData = initialData; // ensure we have useable data on first load

  // midpoints of our domains to set initial positions of each circle to the center of the chart
    $: X_MIDPOINT = (xScale.domain()[0] + xScale.domain()[1]) / 2;
    $: Y_MIDPOINT = (yScale.domain()[0] + yScale.domain()[1]) / 2;

  $: {
    if (currentStep === 0 ) {
      // set X and Y positions of all circles to 0
      renderedData = initialData.map(d => ({ ...d, grade: X_MIDPOINT, hours: Y_MIDPOINT }));
      hoveredData = null;
    } else if (currentStep === 1) {
      renderedData = initialData.map(d => ({ ...d, hours: Y_MIDPOINT }));
      hoveredData = null;
    } else if (currentStep === 2) {
      renderedData = initialData;
      // TODO: setting hoveredData here overrides on mouseover behavior
      // hoveredData = renderedData.find(d => d.hours >= 40)
    }
  }
</script>
<main>
  <TextContent_1 />
  <section>
      <Chart
        bind:width
        bind:height
        bind:hoveredData
        {renderedData}
        {margin}
        {currentStep}
        {xScale}
        {yScale}
        {radius}
      />

    <Steps bind:currentStep />
  </section>
  <TextContent_2 />
</main>
<style>
  main {
    text-align: center;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    max-width: 769px;
    padding: 0.5rem;
  }
  section {
    position: relative;
  }
</style>
