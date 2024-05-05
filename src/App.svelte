<script>
  import data from "./data/data.js";
  console.log(data)

  let width = 400;
  let height = 400;

  const margin = {top:20, right:45, left:0, bottom:20};

  import { scaleLinear } from "d3-scale";
  $: xScale= scaleLinear()
  .domain([0,100]) // unit - range of exam scores
  .range([0,width - margin.left - margin.right]); // unit - pixels

  import { max } from "d3-array";
  const yScale= scaleLinear()
  .domain([0, max(data, d => d.hours)]) // unit - range of hours studied, use max to set ylim
  .range([height - margin.top - margin.bottom, 0]); // unit - pixels

  import AxisX from "./components/AxisX.svelte";
  import AxisY from "./components/AxisY.svelte";
  import Tooltip from "./components/Tooltip.svelte";


  let hoveredData;
  $: console.log(hoveredData);
</script>

<h1> Study longer, score better </h1>
<div class='chart-container' bind:clientWidth={width}
on:mouseleave={() =>{
  hoveredData=null;
}}>

<svg {width} {height}>
<AxisX {height} {xScale} {margin}/>
<AxisY {height} {width} {yScale} {margin}/>
<g class = 'circles' transform="translate({margin.left} {margin.top})"> //moves group of circles aka data according to the margins, so we can see everything
  {#each data as student}
  <circle cx={xScale(student.grade)} 
          cy={yScale(student.hours)} 
          r={hoveredData && hoveredData==student ? "20": "10"}
          opacity={hoveredData ? hoveredData==student ? "1": ".3" : "1"}
          fill="pink"
          stroke="black"
          on:mouseover={() => {
          hoveredData = student;
          }}
          />
  {/each}
  </g>
</svg>
{#if hoveredData}
<Tooltip data={hoveredData} {xScale} {yScale}/>
{/if}
</div>

<style>
  circle {
    transition: r 300ms ease, opacity 300ms ease;
    cursor: pointer;
  }

  h1{
    font-size:1.5rem;
    font-weight:600;
    margin-bottom:0.5rem;
  }
</style>