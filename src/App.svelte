<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  onMount(() => {
    // Specify the chart’s dimensions.    
    const width = 500;
    const height = width;
    const radius = width / 6;

    // Your data variable here
    const data = {"name":"Hazards","children":[{"name":"Natural","children":[{"name":"Biological","children":[{"name":"Infectious disease (General)","value":2.0043213737826427},{"name":"Viral disease","value":3.5496874329788395}]},{"name":"Climatological","children":[{"name":"Forest fire","value":5.571622448361674},{"name":"Land fire (Brush, Bush, Pasture)","value":5.819158884822703},{"name":"Wildfire (General)","value":4.860431855556025}]},{"name":"Geophysical","children":[{"name":"Earthquake","value":4.550717669542857},{"name":"Volcanic activity","value":3.3979400086720375}]},{"name":"Hydrological","children":[{"name":"Flash flood","value":4.68728824250609},{"name":"Flood (General)","value":5.029051919806785},{"name":"Riverine flood","value":7.060060065981077}]},{"name":"Meteorological","children":[{"name":"Blizzard/Winter storm","value":11.930231903081854},{"name":"Derecho","value":1.0791812460476249},{"name":"Hail","value":4.054476177447999},{"name":"Heat wave","value":1.4913616938342726},{"name":"Lightning/Thunderstorms","value":4.162007432880231},{"name":"Severe weather","value":3.9903930401892684},{"name":"Storm (General)","value":5.315935836097904},{"name":"Tornado","value":5.421339397929589},{"name":"Tropical cyclone","value":7.051537733717228}]}]},{"name":"Technological","children":[{"name":"Industrial accident","children":[{"name":"Chemical spill","value":0.22788670461367366},{"name":"Explosion (Industrial)","value":0.9995654882259823},{"name":"Gas leak","value":3.916453974479235}]},{"name":"Miscellaneous accident","children":[{"name":"Collapse (Miscellaneous)","value":0.5237460173328501},{"name":"Explosion (Miscellaneous)","value":0.552668216112193},{"name":"Fire (Miscellaneous)","value":0.9515420900592463},{"name":"Miscellaneous accident (General)","value":0.7913912820470732}]},{"name":"Transport","children":[{"name":"Air","value":0.417606546360271},{"name":"Rail","value":3.6857417386020183},{"name":"Road","value":0.4978118983819142},{"name":"Water","value":0.3201462861110547}]}]}]}
    // const data = {"name":"Hazards","children":[{"name":"Natural","children":[{"name":"Biological","children":[{"name":"Infectious disease (General)","value":101.0},{"name":"Viral disease","value":3552.0}]},{"name":"Climatological","children":[{"name":"Forest fire","value":372582.0},{"name":"Land fire (Brush, Bush, Pasture)","value":659854.0},{"name":"Wildfire (General)","value":72837.0}]},{"name":"Geophysical","children":[{"name":"Earthquake","value":35528.0},{"name":"Volcanic activity","value":2500.0}]},{"name":"Hydrological","children":[{"name":"Flash flood","value":48745.0},{"name":"Flood (General)","value":106931.0},{"name":"Riverine flood","value":11498088.0}]},{"name":"Meterological","children":[{"name":"Blizzard/Winter storm","value":85005603.0},{"name":"Derecho","value":12.0},{"name":"Hail","value":11347.0},{"name":"Heat wave","value":31.0},{"name":"Lightning/Thunderstorms","value":14542.0},{"name":"Severe weather","value":9797.0},{"name":"Storm (General)","value":207145.0},{"name":"Tornado","value":264307.0},{"name":"Tropical cyclone","value":11281175.0}]}]},{"name":"Technological","children":[{"name":"Industrial accident","children":[{"name":"Chemical spill","value":169.0},{"name":"Explosion (Industrial)","value":999.0},{"name":"Gas leak","value":8234.0}]},{"name":"Miscellaneous accident","children":[{"name":"Collapse (Miscellaneous)","value":334.0},{"name":"Explosion (Miscellaneous)","value":357.0},{"name":"Fire (Miscellaneous)","value":894.0},{"name":"Miscellaneous accident (General)","value":619.0}]},{"name":"Transport","chldren":[{"name":"Air","value":261.0},{"name":"Rail","value":4826.0},{"name":"Road","value":315.0},{"name":"Water","value":209.0}]}]}]}
    // const data = {"name":"Natural","children":[{"name":"Biological","children":[{"name":"Infectious disease (General)","value":101.0},{"name":"Viral disease","value":3552.0}]},{"name":"Climatological","children":[{"name":"Forest fire","value":372582.0},{"name":"Land fire (Brush, Bush, Pasture)","value":659854.0},{"name":"Wildfire (General)","value":72837.0}]},{"name":"Geophysical","children":[{"name":"Earthquake","value":35528.0},{"name":"Volcanic activity","value":2500.0}]},{"name":"Hydrological","children":[{"name":"Flash flood","value":48745.0},{"name":"Flood (General)","value":106931.0},{"name":"Riverine flood","value":11498088.0}]},{"name":"Meterological","children":[{"name":"Blizzard/Winter storm","value":85005603.0},{"name":"Derecho","value":12.0},{"name":"Hail","value":11347.0},{"name":"Heat wave","value":31.0},{"name":"Lightning/Thunderstorms","value":14542.0},{"name":"Severe weather","value":9797.0},{"name":"Storm (General)","value":207145.0},{"name":"Tornado","value":264307.0},{"name":"Tropical cyclone","value":11281175.0}]}]}

    // console.log(data);

    // Create the color scale.
    // const color = d3.scaleOrdinal(d3.quantize(d3.interpolateRainbow, data.children.length + 1));
    const color = d3.scaleOrdinal(["#64c4ed", "#e46161"]);

    // Compute the layout.
    const hierarchy = d3.hierarchy(data)
        .sum(d => d.value)
        .sort((a, b) => b.value - a.value);
    const root = d3.partition()
        .size([2 * Math.PI, hierarchy.height + 1])
        (hierarchy);
    root.each(d => d.current = d);

    // Create the arc generator.
    const arc = d3.arc()
        .startAngle(d => d.x0)
        .endAngle(d => d.x1)
        .padAngle(d => Math.min((d.x1 - d.x0) / 2, 0.005))
        .padRadius(radius * 1.5)
        .innerRadius(d => d.y0 * radius)
        .outerRadius(d => Math.max(d.y0 * radius, d.y1 * radius - 1));

    // Create the SVG container.
    const svg = d3.create("svg")
        .attr("viewBox", [-width / 2, -height / 2, width, width])
        .style("font", "7px Roboto, sans-serif");

    // Append the arcs.
    const path = svg.append("g")
        .selectAll("path")
        .data(root.descendants().slice(1))
        .join("path")
        .attr("fill", d => { while (d.depth > 1) d = d.parent; return color(d.data.name); })
        .attr("fill-opacity", d => arcVisible(d.current) ? (d.children ? 0.6 : 0.4) : 0)
        .attr("pointer-events", d => arcVisible(d.current) ? "auto" : "none")
        .attr("d", d => arc(d.current));

    path

    // Make them clickable if they have children.
    path.filter(d => d.children)
        .style("cursor", "pointer")
        .on("click", clicked);

    const format = d3.format(",d");
    path.append("title")
        .text(d => `${d.ancestors().map(d => d.data.name).reverse().join("/")}\n${format(d.value)}`);

    const label = svg.append("g")
        .attr("pointer-events", "none")
        .attr("text-anchor", "middle")
        .style("user-select", "none")
        .selectAll("text")
        .data(root.descendants().slice(1))
        .join("text")
        .attr("dy", "0.35em")
        .attr("fill-opacity", d => +labelVisible(d.current))
        .attr("transform", d => labelTransform(d.current))
        .text(d => d.data.name);

    const parent = svg.append("circle")
        .datum(root)
        .attr("r", radius)
        .attr("fill", "none")
        .attr("pointer-events", "all")
        .on("click", clicked);

    // Handle zoom on click.
    function clicked(event, p) {
        parent.datum(p.parent || root);

        root.each(d => d.target = {
            x0: Math.max(0, Math.min(1, (d.x0 - p.x0) / (p.x1 - p.x0))) * 2 * Math.PI,
            x1: Math.max(0, Math.min(1, (d.x1 - p.x0) / (p.x1 - p.x0))) * 2 * Math.PI,
            y0: Math.max(0, d.y0 - p.depth),
            y1: Math.max(0, d.y1 - p.depth)
        });

        const t = svg.transition().duration(750);

        path.transition(t)
            .tween("data", d => {
                const i = d3.interpolate(d.current, d.target);
                return t => d.current = i(t);
            })
            .filter(function (d) {
                return +this.getAttribute("fill-opacity") || arcVisible(d.target);
            })
            .attr("fill-opacity", d => arcVisible(d.target) ? (d.children ? 0.6 : 0.4) : 0)
            .attr("pointer-events", d => arcVisible(d.target) ? "auto" : "none")
            .attrTween("d", d => () => arc(d.current));

        label.filter(function (d) {
            return +this.getAttribute("fill-opacity") || labelVisible(d.target);
        }).transition(t)
            .attr("fill-opacity", d => +labelVisible(d.target))
            .attrTween("transform", d => () => labelTransform(d.current));
    }

    function arcVisible(d) {
        return d.y1 <= 3 && d.y0 >= 1 && d.x1 > d.x0;
    }

    function labelVisible(d) {
        return d.y1 <= 3 && d.y0 >= 1 && (d.y1 - d.y0) * (d.x1 - d.x0) > 0.03;
    }

    function labelTransform(d) {
        const x = (d.x0 + d.x1) / 2 * 180 / Math.PI;
        const y = (d.y0 + d.y1) / 2 * radius;
        return `rotate(${x - 90}) translate(${y},0) rotate(${x < 180 ? 0 : 180})`;
    }

    // Center Hover Text Label    
    const hoverLabel = svg
    .append("text")
    .attr("text-anchor", "middle")
    .attr("fill", "#888");

    hoverLabel
    .append("tspan")
    .attr("class", "hazard_name")
    .attr("x", 0)
    .attr("y", 0)
    .attr("dy", "-0.1em")
    .attr("font-size", "3em")
    .text("");

    hoverLabel
    .append("tspan")
    .attr("x", 0)
    .attr("y", -2)
    .attr("dy", "1.5em")
    .text("hazards impacted");

    hoverLabel
    .append("tspan")
    .attr("class", "percentage")
    .attr("x", 0)
    .attr("y", 0)
    .attr("dy", "-0.1em")
    .attr("font-size", "3em")
    .text("");

    hoverLabel
    .append("tspan")
    .attr("x", 0)
    .attr("y", 5)
    .attr("dy", "1.5em")
    .text("people between 2000-2022");

    // Add mouseover and mouseout event listeners to show/hide tooltips
    path.on("mouseover", function(event, d) {
      hoverLabel.transition()
        .duration(200)
        .style("opacity", .9);
    })
    .on("mouseout", function(d) {
      hoverLabel.transition()
        .duration(500)
        .style("opacity", 0);
    });

    document.body.appendChild(svg.node());
  });
</script>

<main>
    <h1>What Are the Most Deadly Natural and Technological Disasters from 2000-2022?</h1>
    <p>Click on a slice to zoom in, and the middle to zoom out.</p>
</main>


<style>
    /* add style */
    h1 {
        font-family: 'Roboto', sans-serif;
    }
</style>