---
title: "Um dia ao redor do açude velho"
date: 2017-12-09T17:47:11-03:00
---

<div class="container">
<div class="row">
</div>
<h2>Horários de quantidades de pessoas pedalando no açude velho.</h2>
<div class="row mychart1" id="chart1"></div>
<h2>Relação entre homens e mulheres pedalando ao redor do açude velho.</h2>
<div class="row mychart2" id="chart2"></div>
</div>

<script src="https://d3js.org/d3.v4.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">
<script>

function desenhaGrafico1(dados) {
	var color = d3.scaleQuantize()
		.domain([0, 280])
		.range([ "#99d8c9","#66c2a4","#2ca25f","#006d2c"]);

	var alturaSVG = 200, larguraSVG = 1200;
	var margin = {top: 10, right: 20, bottom:30, left: 45}, // para descolar a vis das bordas do grafico
		larguraVis = larguraSVG - margin.left - margin.right,
		alturaVis = alturaSVG - margin.top - margin.bottom;

	var grafico = d3.select('#chart1') // cria elemento <svg> com um <g> dentro
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

function desenhaGrafico2(dados) {

	var alturaSVG = 400, larguraSVG = 1200;
	var margin = {top: 10, right: 20, bottom:30, left: 45}, // para descolar a vis das bordas do grafico
		larguraVis = larguraSVG - margin.left - margin.right,
		alturaVis = alturaSVG - margin.top - margin.bottom;

	var grafico = d3.select('#chart2') // cria elemento <svg> com um <g> dentro
	.append('svg')
	  .attr('width', larguraVis + margin.left + margin.right)
	  .attr('height', alturaVis + margin.top + margin.bottom)
	.append('g') // para entender o <g> vá em x03-detalhes-svg.html
	  .attr('transform', 'translate(' +  margin.left + ',' + margin.top + ')');

	var parseTime = d3.timeParse("%H:%M");
	var x = d3.scaleTime().range([0, larguraVis]);
	var y = d3.scaleLinear().range([alturaVis, 0]);

	var totalciclistas = {};

	dados.forEach(function (d) {
		if (typeof(totalciclistas[d.horario_inicial]) == "undefined") {
			totalciclistas[d.horario_inicial] = {
												"mulheres": parseInt(d.mulheres_ciclistas), 
												"homens": parseInt(d.homens_ciclistas)
												}
		} else {
			totalciclistas[d.horario_inicial].mulheres += parseInt(d.mulheres_ciclistas);
			totalciclistas[d.horario_inicial].homens += parseInt(d.homens_ciclistas);
		}
	})


	var valueline = d3.line()
		.x(function(d) { return x(parseTime(d.horario_inicial)); })
		.y(function(d) { return y(totalciclistas[d.horario_inicial].mulheres);});

	// define the 2nd line
	var valueline2 = d3.line()
		.x(function(d) { return x(parseTime(d.horario_inicial)); })
		.y(function(d) { return y(totalciclistas[d.horario_inicial].homens);});


	x.domain(d3.extent(dados, function(d) { return parseTime(d.horario_inicial); }));
	y.domain([0, d3.max(dados, function(d) {
	return Math.max(totalciclistas[d.horario_inicial].mulheres, totalciclistas[d.horario_inicial].homens); })]);


	grafico.append("path")
	.data([dados])
	.attr("class", "line")
	.attr("fill", "none")
	.style("stroke", "red")
	.attr("d", valueline);

	grafico.append("path")
	.data([dados])
	.attr("fill", "none")
	.attr("class", "line")
	.style("stroke", "blue")
	.attr("d", valueline2);

	grafico.append("g")
	.attr("transform", "translate(0," + alturaVis + ")")
	.call(d3.axisBottom(x).tickFormat(d3.timeFormat("%H:%M")));

	grafico.append("g")
	.call(d3.axisLeft(y));


	grafico.append("text")
		.attr("transform", "translate(" + (larguraVis-100) + "," + y(totalciclistas["20:45"].homens) + ")")
		.attr("dy", ".35em")
		.attr("text-anchor", "start")
		.style("fill", "steelblue")
		.text("Homens");

	grafico.append("text")
		.attr("transform", "translate(" + (larguraVis-100) + "," + y(totalciclistas["20:45"].mulheres) + ")")
		.attr("dy", ".35em")
		.attr("text-anchor", "start")
		.style("fill", "red")
		.text("Mulheres");
}

d3.csv('https://raw.githubusercontent.com/luizaugustomm/pessoas-no-acude/master/dados/processados/dados.csv', function(dados) {
  desenhaGrafico1(dados);
});

d3.csv('https://raw.githubusercontent.com/luizaugustomm/pessoas-no-acude/master/dados/processados/dados.csv', function(dados) {
  desenhaGrafico2(dados);
});


</script>
