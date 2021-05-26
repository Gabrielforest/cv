---
title: "Gabriel F. Pereira's CV"
author: Gabriel F. Pereira
date: "2021-05-26"
params:
  pdf_mode:
    value: true
output:
  pagedown::html_resume:
    css: ['dd_cv.css', 'resume']
    self_contained: true
---





<style>
:root{
  --decorator-outer-offset-left: -6.5px;
}
</style>


Aside
================================================================================

<script id = "data_for_network" type = "application/json">{"nodes":[{"section":"Education","title":"B.S., Forest Engineering","loc":"Federal University of São Carlos","institution":"Sorocaba, SP","start":"2018","end":"current","description_bullets":"- Heavily interested in environmental data science.","start_year":"2018","end_year":"2031","no_start":false,"has_start":true,"no_end":false,"has_end":true,"id":1},{"section":"Research Positions","title":"Director Human Resources","loc":"CAEF","institution":"Sorocaba, SP","start":"2020","end":"current","description_bullets":"- Assists the Academic Center of Forest\n Engineering in the relationships\nbetween team mebers and in the \nnetworking process.\n- Connected with several \ninternational researchers \nto spread different knowledges.\n- Subtitled videos to make \nthese video posts inclusive @caef.ufscar","start_year":"2020","end_year":"2031","no_start":false,"has_start":true,"no_end":false,"has_end":true,"id":2},{"section":"Research Positions","title":"Scientific Initiation Fellow","loc":"FAPESP","institution":"Sorocaba, SP","start":"2020","end":"2021","description_bullets":"- Applied suitability modeling to predict \nthe priority areas for conservation of Amazon nut trees.\n- Worked with national and international colaborators from [Bioversity International](https://www.bioversityinternational.org/) and \nEmbrapa\n- Learned shiny, github and statistics methods\nfor modeling.","start_year":"2020","end_year":"2021","no_start":false,"has_start":true,"no_end":false,"has_end":true,"id":3},{"section":"Data Science Writings","title":"Predicting Missing Values","loc":"My Blog","institution":"Sorocaba, SP","start":"N/A","end":"2021","description_bullets":"- Analising forest inventory data \nand predicting NAs through \nleast squares regression model\n- Provided advice for dealing with outliers.","start_year":"2031","end_year":"2021","no_start":true,"has_start":false,"no_end":false,"has_end":true,"id":4},{"section":"Data Science Writings","title":"Visualizing Maps Interactively","loc":"My Blog","institution":"Sorocaba, SP","start":"N/A","end":"2021","description_bullets":"- My first shiny app! Using the results \nfrom ensemble models of my scientific \ninitiation to define the priority areas \nfor conservation of Brazil nut.","start_year":"2031","end_year":"2021","no_start":true,"has_start":false,"no_end":false,"has_end":true,"id":5},{"section":"Research Positions","title":"Research Assistant","loc":"Hidrolef","institution":"Sorocaba, SP","start":"2018","end":"2018","description_bullets":"- Developed work on field making ecophysiological measurements to help a doctoral student.","start_year":"2018","end_year":"2018","no_start":false,"has_start":true,"no_end":false,"has_end":true,"id":6}],"edges":[{"year":2018,"source":1,"target":6},{"year":2020,"source":1,"target":2},{"year":2020,"source":1,"target":3},{"year":2020,"source":2,"target":3},{"year":2021,"source":1,"target":2},{"year":2021,"source":1,"target":3},{"year":2021,"source":1,"target":4},{"year":2021,"source":1,"target":5},{"year":2021,"source":2,"target":3},{"year":2021,"source":2,"target":4},{"year":2021,"source":2,"target":5},{"year":2021,"source":3,"target":4},{"year":2021,"source":3,"target":5},{"year":2021,"source":4,"target":5}]}</script><script src="https://cdnjs.cloudflare.com/ajax/libs/d3/5.16.0/d3.min.js"></script><svg style = "width: 100%; height:320px; margin-top: -125px;" id = "cv_network_viz"></svg><script>const data_json = document.querySelector("script[type='application/json']").textContent;
const {edges, nodes} = JSON.parse(data_json);

class MyHandler extends Paged.Handler {
	constructor(chunker, polisher, caller) {
		super(chunker, polisher, caller);
	}

	afterRendered (){
		plot_network();
	}
}
Paged.registerHandlers(MyHandler);

function plot_network(){
  const {width, height} = document.querySelector("svg#cv_network_viz").getBoundingClientRect();

  const svg = d3.select("svg#cv_network_viz")
    .attr("width", width)
    .attr("height", height);

  const unique_sections = [...new Set(nodes.map(d => d.section))];
  const color_scale = d3.scaleOrdinal()
    .domain(unique_sections)
    .range(d3.schemeSet2);

  const edge_color = d3.scaleLinear()
    .domain(d3.extent(edges, d => d.year));

  const simulation = d3.forceSimulation(nodes)
    .force("link", d3.forceLink(edges).id(d => d.id))
    .force("charge", d3.forceManyBody())
    .force("center", d3.forceCenter(width / 2, height / 2))
    .on("tick", ticked);

  const g = svg.append("g");

  const link = g
    .selectAll("line")
    .data(edges)
    .enter().append("line")
      .attr("stroke", d => d3.interpolateGreys(edge_color(d.year)))
      .attr("stroke-width", 0.5);

  const node = g
    .attr("stroke", "#fff")
    .attr("stroke-width", 1.5)
    .selectAll("circle")
    .data(nodes)
    .enter().append("circle")
      .attr("r", 5)
      .attr("fill", d => color_scale(d.section))
      .call(drag(simulation));

  node.append("title")
      .text(d => `${d.section}\n${d.title}`);

  svg.call(d3.zoom()
      .extent([[0, 0], [width, height]])
      .scaleExtent([1, 8])
      .on("zoom", zoomed));

  function ticked() {
    link
        .attr("x1", d => d.source.x)
        .attr("y1", d => d.source.y)
        .attr("x2", d => d.target.x)
        .attr("y2", d => d.target.y);

    node
        .attr("cx", d => d.x)
        .attr("cy", d => d.y);
  }

  function zoomed() {
    g.attr("transform", d3.event.transform);
  }

  function drag(simulation){

    function dragstarted(d) {
      if (!d3.event.active) simulation.alphaTarget(0.3).restart();
      d.fx = d.x;
      d.fy = d.y;
    }

    function dragged(d) {
      d.fx = d3.event.x;
      d.fy = d3.event.y;
    }

    function dragended(d) {
      if (!d3.event.active) simulation.alphaTarget(0);
      d.fx = null;
      d.fy = null;
    }

    return d3.drag()
        .on("start", dragstarted)
        .on("drag", dragged)
        .on("end", dragended);
  }

}
</script>


View this CV online with links at _https://github.com/gabrielforest/cv/docs/freitas_cv.html_

Contact {#contact}
--------------------------------------------------------------------------------

- <i class='fa fa-envelope'></i> gabrielfreitaspereira10@gmail.com
- <i class='fa fa-github'></i> https://github.com/Gabrielforest
- <i class='fa fa-link'></i> https://gabrielforest.github.io/portfolio/
- <i class='fa fa-linkedin'></i> https://www.linkedin.com/in/gabriel-de-freitas-pereira-925260205



Language Skills {#skills}
--------------------------------------------------------------------------------

<div
  class = 'skill-bar'
  style = "background:linear-gradient(to right,
                                      #969696 100%,
                                      #d9d9d9 100% 100%)"
>R</div>
<div
  class = 'skill-bar'
  style = "background:linear-gradient(to right,
                                      #969696 100%,
                                      #d9d9d9 100% 100%)"
>Portuguese</div>
<div
  class = 'skill-bar'
  style = "background:linear-gradient(to right,
                                      #969696 80%,
                                      #d9d9d9 80% 100%)"
>English</div>



Disclaimer {#disclaimer}
--------------------------------------------------------------------------------

Made with the R package [**pagedown**](https://github.com/rstudio/pagedown). 

The source code is available [on github.com/gabrielforest/cv](https://github.com/gabrielforest/cv).

Last updated on 2021-05-26.



Main
================================================================================

Gabriel F. Pereira {#title}
--------------------------------------------------------------------------------

I am a passionate person about environmental data science and visualization, mainly through R software.



Education {data-icon=graduation-cap data-concise=true}
--------------------------------------------------------------------------------

### B.S., Forest Engineering

Federal University of São Carlos

Sorocaba, SP

current - 2018

- Heavily interested in environmental data science.

<br/>
<br/>
<br/>
<br/>



Research Experience {data-icon=laptop}
--------------------------------------------------------------------------------

### Director Human Resources

CAEF

Sorocaba, SP

current - 2020

- Assists the Academic Center of Forest
 Engineering in the relationships
between team mebers and in the 
networking process.
- Connected with several 
international researchers 
to spread different knowledges.
- Subtitled videos to make 
these video posts inclusive @caef.ufscar



### Scientific Initiation Fellow

FAPESP

Sorocaba, SP

2021 - 2020

- Applied suitability modeling to predict 
the priority areas for conservation of Amazon nut trees.
- Worked with national and international colaborators from Bioversity International<sup>1</sup> and 
Embrapa
- Learned shiny, github and statistics methods
for modeling.



### Research Assistant

Hidrolef

Sorocaba, SP

2018 - 2018

- Developed work on field making ecophysiological measurements to help a doctoral student.

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>




Data Science Writing {data-icon=chart-line}
--------------------------------------------------------------------------------

::: aside
I regularly blog about data science and visualization on my blog Gabriel's Portfolio<sup>2</sup>
:::

### Predicting Missing Values<sup>3</sup>

My Blog

Sorocaba, SP

2021

- Analising forest inventory data 
and predicting NAs through 
least squares regression model
- Provided advice for dealing with outliers.



### Visualizing Maps Interactively<sup>4</sup>

My Blog

Sorocaba, SP

2021

- My first shiny app! Using the results 
from ensemble models of my scientific 
initiation to define the priority areas 
for conservation of Brazil nut.

