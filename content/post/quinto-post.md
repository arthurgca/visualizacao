---
title: "Quinto Post"
date: 2018-02-19T15:28:12-03:00
---



<img src="../pb-indi.svg" width="960" height="auto">
<svg id="svg1" width="400" height="100"></svg>

<img src="../pb-coleta.svg" width="960" height="auto">
<svg id="svg2" width="400" height="100"></svg>

<h3> O que podemos perceber relacionando os dois gráficos é que nas áreas com mais indigenas não existe uma coleta de lixo e no geral a coleta de lixo em maiores porcentagens se concentra
nas grandes cidades.</h3>

<script src="https://d3js.org/d3.v4.min.js"></script>
<script src="https://d3js.org/d3-scale-chromatic.v1.min.js"></script>
<script src="https://d3js.org/topojson.v2.min.js"></script>
<script>

var svg = d3.select("#svg1"),
    width = +svg.attr("width"),
    height = +svg.attr("height");

var x = d3.scaleLinear()
    .domain([0,100])
    .rangeRound([100, 360]);

var color = d3.scaleThreshold()
    .domain([0, 5, 10, 25,50, 100, 200])
    .range(d3.schemeOrRd[8]);

var g = svg.append("g")
    .attr("class", "key")
    .attr("transform", "translate(0,40)");

g.selectAll("rect")
  .data(color.range().map(function(d) {
      d = color.invertExtent(d);
      if (d[0] == null) d[0] = x.domain()[0];
      if (d[1] == null) d[1] = x.domain()[1];
      return d;
    }))
  .enter().append("rect")
    .attr("height", 8)
    .attr("x", function(d) { return x(d[0]); })
    .attr("width", function(d) { return x(d[1]) - x(d[0]); })
    .attr("fill", function(d) { return color(d[0]); });

g.append("text")
    .attr("class", "caption")
    .attr("x", x.range()[0])
    .attr("y", -6)
    .attr("fill", "#000")
    .attr("text-anchor", "start")
    .attr("font-weight", "bold")
    .text("Número de indigenas no município");

g.call(d3.axisBottom(x)
    .tickSize(13)
    .tickFormat(function(x, i) { return i ? x : x + "%"; })
    .tickValues(color.domain()))
  .select(".domain")
    .remove();
</script>
 
<script>
var svg2 = d3.select("#svg2"),
    width = +svg2.attr("width"),
    height = +svg2.attr("height");

var x = d3.scaleLinear()
    .domain([0,100])
    .rangeRound([100, 360]);

var color = d3.scaleThreshold()
    .domain([0, 20, 40, 60, 80, 100])
    .range(d3.schemeGnBu[7]);

var g = svg2.append("g")
    .attr("class", "key")
    .attr("transform", "translate(0,40)");

g.selectAll("rect")
  .data(color.range().map(function(d) {
      d = color.invertExtent(d);
      if (d[0] == null) d[0] = x.domain()[0];
      if (d[1] == null) d[1] = x.domain()[1];
      return d;
    }))
  .enter().append("rect")
    .attr("height", 8)
    .attr("x", function(d) { return x(d[0]); })
    .attr("width", function(d) { return x(d[1]) - x(d[0]); })
    .attr("fill", function(d) { return color(d[0]); });

g.append("text")
    .attr("class", "caption")
    .attr("x", x.range()[0])
    .attr("y", -6)
    .attr("fill", "#000")
    .attr("text-anchor", "start")
    .attr("font-weight", "bold")
    .text("Porcentagem de coleta de lixo");

g.call(d3.axisBottom(x)
    .tickSize(13)
    .tickFormat(function(x, i) { return i ? x : x + "%"; })
    .tickValues(color.domain()))
  .select(".domain")
    .remove();
</script>