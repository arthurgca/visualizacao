---
title: "Laboratório 5 - Interação"
date: 2017-12-22T14:32:22-03:00
draft: true
---
<style type="text/css">
    div.tooltip {   
    position: absolute;         
    text-align: center;         
    width: 100px;                    
    height: 28px;                   
    padding: 2px;               
    font: 12px sans-serif;      
    background: white; 
    border: 0px;        
    border-radius: 8px;         
    pointer-events: none;           
}
</style>

<div class="container"> 
<div class="row">
</div>
<h2>Os locais de coleta e os seus meios de transportes mais usados.</h2>
<h3>Com filtragem por horários</h3>
<h3>Passe o mouse em cima do círculo para ver a quantidade de ciclistas por sexo naquele horário</h3>

<select id="turno" onchange="atualiza(this)">
    <option value="tudo">Tudo</option>
    <option value="noite">Noite (acima de 16:00)</option>
    <option value="dia">Dia (entre 12:00 e 16:00)</option>
    <option value="manha">Manhã (abaixo de 12:00)</option>
</select>   
<div class="row mychart2" id="chart2"></div>
</div>

<script src="https://d3js.org/d3.v4.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">
<script>

var parseTime = d3.timeParse("%H:%M");


function atualiza(sel) {
    d3.select("svg").remove(); 

    d3.csv('https://raw.githubusercontent.com/luizaugustomm/pessoas-no-acude/master/dados/processados/dados.csv', function(dados) {
      if (sel.value == "noite") {
        dados = dados.filter(function(d) { 
            horario = parseInt(d["horario_final"].slice(0,2));
            if (horario > 16) {
                return d;
            }  
        })
      }
      if (sel.value == "manha") {
        dados = dados.filter(function(d) { 
            horario = parseInt(d["horario_final"].slice(0,2));
            if (horario < 12) {
                return d;
            }  
        })
      }
      if (sel.value == "dia") {
        dados = dados.filter(function(d) { 
            horario = parseInt(d["horario_final"].slice(0,2));
            if (horario > 12 && horario <= 16) {
                return d;
            }  
        })
      }
      desenhaGrafico3(dados) 

    });
}

function desenhaGrafico3(dados) {
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

    var x = d3.scaleTime().range([0, larguraVis]);
    var y = d3.scaleLinear().range([alturaVis, 0]);

    var totalciclistas = {};
    var div = d3.select("body").append("div")   
    .attr("class", "tooltip")               
    .style("opacity", 0);

    dados.forEach(function (d) {
        if (typeof(totalciclistas[d.horario_final]) == "undefined") {
            totalciclistas[d.horario_final] = {
                                                "mulheres": parseInt(d.mulheres_ciclistas), 
                                                "homens": parseInt(d.homens_ciclistas)
                                                }
        } else {
            totalciclistas[d.horario_final].mulheres += parseInt(d.mulheres_ciclistas);
            totalciclistas[d.horario_final].homens += parseInt(d.homens_ciclistas);
        }
    })


    var valueline = d3.line()
        .x(function(d) { return x(parseTime(d.horario_final)); })
        .y(function(d) { return y(totalciclistas[d.horario_final].mulheres);});

    // define the 2nd line
    var valueline2 = d3.line()
        .x(function(d) { return x(parseTime(d.horario_final)); })
        .y(function(d) { return y(totalciclistas[d.horario_final].homens);});


    x.domain(d3.extent(dados, function(d) { return parseTime(d.horario_final); }));
    y.domain([0, d3.max(dados, function(d) {
    return Math.max(totalciclistas[d.horario_final].mulheres, totalciclistas[d.horario_final].homens); })]);



    grafico.select("body").append("div")   
    .attr("class", "tooltip")               
    .style("opacity", 0);

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

    grafico.selectAll("dot")    
        .data(dados)         
    .enter().append("circle")                               
        .attr("r", 4.5)       
        .attr("fill", "#5ab4ac")
        .attr("cx", function(d) { return x(parseTime(d.horario_final)); })       
        .attr("cy", function(d) { return y(totalciclistas[d.horario_final].mulheres); })     
        .on("mouseover", function(d) {      
            div.transition()        
                .duration(200)      
                .style("opacity", .9);      
            div .html(d.horario_final + "<br/>  Ciclistas: "  + totalciclistas[d.horario_final].mulheres)  
                .style("left", (d3.event.pageX) + "px")     
                .style("top", (d3.event.pageY - 28) + "px");    
            })                  
        .on("mouseout", function(d) {       
            div.transition()        
                .duration(500)      
                .style("opacity", 0);   
        });

    grafico.selectAll("dot")    
        .data(dados)         
    .enter().append("circle")                               
        .attr("r", 4.5)       
        .attr("fill", "#d8b365")
        .attr("cx", function(d) { return x(parseTime(d.horario_final)); })       
        .attr("cy", function(d) { return y(totalciclistas[d.horario_final].homens); })     
        .on("mouseover", function(d) {      
            div.transition()        
                .duration(200)      
                .style("opacity", .9);      
            div .html(d.horario_final + "<br/>  Ciclistas: "  + totalciclistas[d.horario_final].homens)  
                .style("left", (d3.event.pageX) + "px")     
                .style("top", (d3.event.pageY - 28) + "px");    
            })                  
        .on("mouseout", function(d) {       
            div.transition()        
                .duration(500)      
                .style("opacity", 0);   
        });


    grafico.append("text")
        .attr("transform", "translate(" + (larguraVis/2) + "," + (1) + ")")
        .attr("dy", ".35em")
        .attr("text-anchor", "start")
        .style("fill", "#d8b365")
        .text("Homens");

    grafico.append("text")
        .attr("transform", "translate(" + (larguraVis/2) + "," + (20) + ")")
        .attr("dy", ".35em")
        .attr("text-anchor", "start")
        .style("fill", "#5ab4ac")
        .text("Mulheres"); 

}

d3.csv('https://raw.githubusercontent.com/luizaugustomm/pessoas-no-acude/master/dados/processados/dados.csv', function(dados) {
  desenhaGrafico3(dados);
});


</script>
