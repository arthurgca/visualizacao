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
<h3>Percebemos que não há muita diferença entre a quantidade de homens e mulheres nos horários considerados "perigosos".</h3>
<div class="row mychart2" id="chart2"></div>
<h2>Os locais de coleta e os seus meios de transportes mais usados.</h2>
<div class="row mychart2" id="chart3"></div>
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
	.style("stroke", "#5ab4ac")
	.attr("d", valueline);

	grafico.append("path")
	.data([dados])
	.attr("fill", "none")
	.attr("class", "line")
	.style("stroke", "#d8b365")
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
		.style("fill", "#d8b365")
		.text("Homens");

	grafico.append("text")
		.attr("transform", "translate(" + (larguraVis-100) + "," + y(totalciclistas["20:45"].mulheres) + ")")
		.attr("dy", ".35em")
		.attr("text-anchor", "start")
		.style("fill", "#5ab4ac")
		.text("Mulheres");
}

function desenhaGrafico3(data) {
	var alturaSVG = 400, larguraSVG = 1200;
	var margin = {top: 10, right: 20, bottom:30, left: 45}, // para descolar a vis das bordas do grafico
		larguraVis = larguraSVG - margin.left - margin.right,
		alturaVis = alturaSVG - margin.top - margin.bottom;

	var grafico = d3.select('#chart3') // cria elemento <svg> com um <g> dentro
	.append('svg')
	  .attr('width', larguraVis + margin.left + margin.right)
	  .attr('height', alturaVis + margin.top + margin.bottom)
	.append('g') // para entender o <g> vá em x03-detalhes-svg.html
	  .attr('transform', 'translate(' +  margin.left + ',' + margin.top + ')');

var x0 = d3.scaleBand()
    .rangeRound([0, larguraVis])
    .paddingInner(0.1);

var x1 = d3.scaleBand()
    .padding(0.05);

var y = d3.scaleLinear()
    .rangeRound([alturaVis, 0]);

var z = d3.scaleOrdinal()
    .range(["#98abc5", "#8a89a6", "#7b6888", "#6b486b", "#a05d56", "#d0743c", "#ff8c00"]);

dados = []
data.forEach(function (d) {
		objeto = {
		"local": d.local,
		"carros": parseInt(d.carros), 
		"motos": parseInt(d.motos),
		"onibus": parseInt(d.onibus),
		"caminhoes": parseInt(d.caminhoes),
		"total_ciclistas": parseInt(d.total_ciclistas),
		"total_pedestres": parseInt(d.total_pedestres)
	}
	dados.push(objeto);
});
	var keys = d3.keys(dados[0]).filter(function(key) { return key !== "local";});

  x0.domain(dados.map(function(d) { console.log(d.local); return d.local; }));
  x1.domain(keys).rangeRound([0, x0.bandwidth()]);
  y.domain([0, d3.max(dados, function(d) { return d3.max(keys, function(key) { return d[key]; }); })]).nice();

  grafico.append("g")
    .selectAll("g")
    .data(dados)
    .enter().append("g")
      .attr("transform", function(d) { return "translate(" + x0(d.local) + ",0)"; })
    .selectAll("rect")
    .data(function(d) { return keys.map(function(key) { return {key: key, value: d[key]}; }); })
    .enter().append("rect")
      .attr("x", function(d) { return x1(d.key); })
      .attr("y", function(d) { return y(d.value); })
      .attr("width", x1.bandwidth())
      .attr("height", function(d) { return alturaVis - y(d.value); })
      .attr("fill", function(d) { return z(d.key); });

  grafico.append("g")
      .attr("class", "axis")
      .attr("transform", "translate(0," + alturaVis + ")")
      .call(d3.axisBottom(x0));

  grafico.append("g")
      .attr("class", "axis")
      .call(d3.axisLeft(y).ticks(null, "s"))
    .append("text")
      .attr("x", 2)
      .attr("y", y(y.ticks().pop()) + 0.5)
      .attr("dy", "0.32em")
      .attr("fill", "#000")
      .attr("font-weight", "bold")
      .attr("text-anchor", "start")
      .text("Population");

  var legend = grafico.append("g")
      .attr("font-family", "sans-serif")
      .attr("font-size", 10)
      .attr("text-anchor", "end")
    .selectAll("g")
    .data(keys.slice().reverse())
    .enter().append("g")
      .attr("transform", function(d, i) { return "translate(0," + i * 20 + ")"; });

  legend.append("rect")
      .attr("x", larguraVis - 19)
      .attr("width", 19)
      .attr("height", 19)
      .attr("fill", z);

  legend.append("text")
      .attr("x", larguraVis - 24)
      .attr("y", 9.5)
      .attr("dy", "0.32em")
      .text(function(d) { return d; });


}

d3.csv('https://raw.githubusercontent.com/luizaugustomm/pessoas-no-acude/master/dados/processados/dados.csv', function(dados) {
  desenhaGrafico1(dados);
  desenhaGrafico2(dados);
  desenhaGrafico3(dados);
});


</script>
