---
title: "Lab 2 - Parte 1"
date: 2017-11-24T22:36:59-03:00
---

<div class="container">
<div class="row">
  <h2>Mês de período chuvoso ou não. </h2>
</div>
<div class="row mychart" id="chart">
</div>
</div>

<style>
.mychart rect {
  fill: steelblue;
}

.mychart rect:hover {
  fill: goldenrod;
}

.mychart text {
  font: 12px sans-serif;
  text-anchor: left;
}
</style>

<script src="https://d3js.org/d3.v4.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">

<script type="text/javascript">
"use strict"

function desenhaBarras(dados) {
	var alturaSVG = 400, larguraSVG = 900;
	var margin = {top: 10, right: 20, bottom:30, left: 45}, // para descolar a vis das bordas do grafico
	  larguraVis = larguraSVG - margin.left - margin.right,
	  alturaVis = alturaSVG - margin.top - margin.bottom;

	var grafico = d3.select('#chart') // cria elemento <svg> com um <g> dentro
	.append('svg')
	  .attr('width', larguraVis + margin.left + margin.right)
	  .attr('height', alturaVis + margin.top + margin.bottom)
	.append('g') // para entender o <g> vá em x03-detalhes-svg.html
	  .attr('transform', 'translate(' +  margin.left + ',' + margin.top + ')');

	  var x = d3.scaleBand()
	            .domain(dados.map(function(d, i) { return d.noventa_percentil;}))
	            .rangeRound([0, larguraVis])
	            .padding(0.05); // Configure essa escala com domain, range e padding
	  var y = d3.scaleLinear()
	            .domain([d3.min(dados, (d, i) => d.dez_percentil), d3.max(dados, (d, i) => d.dez_percentil)])
	            .rangeRound([alturaVis, 0]); // Configure essa escala com domain e range
	                           // Lembre que uma escala pode converter de 1..10 -> 100..1

	grafico.selectAll('g')
	      .data(dados)
	      .enter()
	        .append('circle')
	        .attr("r", 	10)
	        .attr('cx', d => x(d.noventa_percentil))
	        .attr('cy', d => y(d.dez_percentil))
  			.style("fill", function(d) {
  			if(d.mediana > 80) {
  				return "blue";
  			} else {
  				return "lightblue"
  			}
  			});

	grafico.selectAll('text')
		.data(dados)
		.enter()
		.append("text")
		.attr("x", d => x(d.noventa_percentil)- 20)
		.attr("y", d => y(d.dez_percentil) + 30)
		.text(d => "Mês: " + d.mes);


}

d3.csv('../dados_boqueirao.csv', function(dados) {
	desenhaBarras(dados);
});


</script>	