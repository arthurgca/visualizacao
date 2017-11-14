---
title: "Primeiro Post"
date: 2017-11-13T21:52:55-03:00
---

<h2> Caminho do volume morto </h3>
<div id="vis" width=300></div>

<h2>Quais os meses em que o açude mais sangrou </h3>
<div id="vis2" width=300></div>

<h2>Comparando os anos com piores médias de volume percentual</h3>
<h5>O ano com menor média foi 2017, em seguida 2016 e logo depois 1999. No gráfico estamos comparando os valores máximos mensáis.</h2>
<div id="vis3" width=300></div>


<script src="https://cdnjs.cloudflare.com/ajax/libs/vega/3.0.7/vega.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vega-lite/2.0.1/vega-lite.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vega-embed/3.0.0-rc7/vega-embed.js"></script>
<script>
    const spec = "../volumemorto.json";
    const spec2 = "../sangrou.json";
    const spec3 = "../anosseca.json";


  	vegaEmbed('#vis', spec).catch(console.warn);
  	vegaEmbed('#vis2', spec2).catch(console.warn);
  	vegaEmbed('#vis3', spec3).catch(console.warn);
</script>
