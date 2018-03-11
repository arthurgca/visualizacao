---
title: "Oitavo Post"
date: 2018-03-10T21:06:34-03:00
---
<script src="https://d3js.org/d3.v4.min.js"></script>

<style>
    .node {
        fill: #ccc;
        stroke: #fff;
        stroke-width: 2px;
    }

    .link {
        stroke: #999;
        stroke-opacity: 0.3;
    }
  </style>

<body>

<div class="container">

  <div class="header"></div>
<header>
<h1>Grafos sobre Trevor</h1>
</header>

<div id="chart"></div>

<div class="section">
<div class="row">
  <div class="col-md-8">
    <h3>O que é?</h3>
    <p>Para esse exemplo, usamos dados de quais artistas são semelhantes a quais da API do Spotify. A partir de um artista inicial, usamos um programa que pega artistas semelhantes a ele, e semelhantes a estes, até atingir um limite.</p>
    <h3>Topologia da rede</h3>
    <p>Os nós se agrupam com base no estilo de cada artista.</p>
  </div>
</div>
</div>


<script>
  var width = 1000,
      height = 1000;

  var svg = d3.select("#chart")
      .append("svg")
      .attr('version', '1.1')
      .attr('viewBox', '0 0 '+width+' '+height)
      .attr('width', '100%');

  var simulation = d3.forceSimulation()
      .force("link", d3.forceLink().id(function(d) { return d.id; }))
      .force("charge", d3.forceManyBody().strength(-10))
      .force("center", d3.forceCenter(width / 2, height / 2));

  d3.json("../trevor.json", function(error, graph) {
    if (error) throw error;

    const size_threshold = 30;
    filter_out = graph.nodes.filter(x => x.size < size_threshold);
    for (var index in filter_out) {
      graph.edges = graph.edges.filter(x => x.source != filter_out[index].id);
      graph.edges = graph.edges.filter(x => x.target != filter_out[index].id);
    }
    graph.nodes = graph.nodes.filter(x => x.size >= size_threshold);

    const min = d3.min(graph.nodes,  function(d) { return d.size; });
    const max = d3.max(graph.nodes,  function(d) { return d.size; });

    const color = d3.scaleThreshold().domain([min, (max*0.25), (max*0.50), (max*0.75), max]).range(["#fee5d9","#fcae91","#fb6a4a","#de2d26","#a50f15"]);

    var link = svg.append("g")
        .attr("class", "link")
      .selectAll("line")
        .data(graph.edges)
      .enter().append("line");

    var node = svg.append("g")
        .attr("class", "nodes")
      .selectAll("circle")
        .data(graph.nodes)
      .enter().append("circle")
        .attr("r", function(d) { return d.size/6; })
        .attr("fill", function(d) { return color(d.size); })
        .call(d3.drag()
            .on("start", dragstarted)
            .on("drag", dragged)
            .on("end", dragended));

    node._groups[0][0].setAttribute("fill", "#000");

    node.append("title")
        .text(function(d) { return d.label; });

    simulation
        .nodes(graph.nodes)
        .on("tick", ticked);

    simulation.force("link")
        .links(graph.edges);

    function ticked() {
      link
          .attr("x1", function(d) { return d.source.x; })
          .attr("y1", function(d) { return d.source.y; })
          .attr("x2", function(d) { return d.target.x; })
          .attr("y2", function(d) { return d.target.y; });

      node
          .attr("cx", function(d) { return d.x; })
          .attr("cy", function(d) { return d.y; });
    }
  });

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

</script>

</body>