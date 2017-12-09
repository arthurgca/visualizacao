---
title: "Um dia ao redor do açude velho"
date: 2017-12-09T17:47:11-03:00
---

<div class="container">
<div class="row">
  <h2>Mês de período chuvoso ou não. </h2>
</div>
<div class="row mychart" id="chart"></div>
</div>

<script src="https://d3js.org/d3.v4.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">

<script>

function desenha(dados) {
	var color = d3.scaleQuantize()
		.domain([0, 280])
		.range([ "#99d8c9","#66c2a4","#2ca25f","#006d2c"]);

	var alturaSVG = 200, larguraSVG = 1200;
	var margin = {top: 10, right: 20, bottom:30, left: 45}, // para descolar a vis das bordas do grafico
		larguraVis = larguraSVG - margin.left - margin.right,
		alturaVis = alturaSVG - margin.top - margin.bottom;

	var grafico = d3.select('#chart') // cria elemento <svg> com um <g> dentro
	.append('svg')
	  .attr('width', larguraVis + margin.left + margin.right)
	  .attr('height', alturaVis + margin.top + margin.bottom)
	.append('g') // para entender o <g> vá em x03-detalhes-svg.html
	  .attr('transform', 'translate(' +  margin.left + ',' + margin.top + ')');
	
	var	parseDate = d3.timeParse("%H:%M");

	var x = d3.scaleTime().range([0, larguraVis]).domain(d3.extent(dados, function(d) { return parseDate(d.horario_inicial); })).interpolate(d3.interpolateRound);

	var totalciclistas = {};

	dados.forEach(function (d) {
		if (typeof(totalciclistas[d.horario_inicial]) == "undefined") {
			totalciclistas[d.horario_inicial] = parseInt(d.total_ciclistas);
		} else {
			totalciclistas[d.horario_inicial] += parseInt(d.total_ciclistas);
		}
	})

	grafico.selectAll('g')
			.data(dados)
			.enter()
			.append('circle')
			.attr("r", 	d => totalciclistas[d.horario_inicial] * 0.10 )
			.attr('cy', alturaSVG/2)
      		.attr("cx", d => x(parseDate(d.horario_inicial)))
      		.attr("fill", d => color(totalciclistas[d.horario_inicial])); 


	grafico.append("g")
		.attr("class", "x axis")
		.attr("transform", "translate(0," + alturaVis + ")")
		.call(d3.axisBottom(x).tickFormat(d3.timeFormat("%H:%M")));



			            
}


d3.csv('https://raw.githubusercontent.com/luizaugustomm/pessoas-no-acude/master/dados/processados/dados.csv', function(dados) {
  desenha(dados);
});

</script>
