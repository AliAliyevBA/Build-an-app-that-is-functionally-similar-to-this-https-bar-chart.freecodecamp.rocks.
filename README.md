# Build-an-app-that-is-functionally-similar-to-this-https-bar-chart.freecodecamp.rocks.
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Bar Chart</title>
  <script src="https://d3js.org/d3.v6.min.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
    }

    .bar {
      fill: steelblue;
    }

    .bar:hover {
      fill: orange;
    }

    #tooltip {
      position: absolute;
      background-color: white;
      border: 1px solid black;
      padding: 5px;
      display: none;
    }
  </style>
</head>
<body>

  <h1 id="title">United States GDP</h1>
  
  <div id="chart-container"></div>
  <div id="tooltip"></div>

  <script>
    const width = 800;
    const height = 400;
    const padding = 60;

    const url = "https://raw.githubusercontent.com/freeCodeCamp/ProjectReferenceData/master/GDP-data.json";

    fetch(url)
      .then(response => response.json())
      .then(data => {
        const dataset = data.data;

        const svg = d3.select("#chart-container")
                      .append("svg")
                      .attr("width", width)
                      .attr("height", height);

        const xScale = d3.scaleBand()
                         .domain(dataset.map(d => new Date(d[0])))
                         .range([padding, width - padding])
                         .padding(0.1);

        const yScale = d3.scaleLinear()
                         .domain([0, d3.max(dataset, d => d[1])])
                         .range([height - padding, padding]);

        const xAxis = d3.axisBottom(xScale).tickFormat(d3.timeFormat("%Y"));
        const yAxis = d3.axisLeft(yScale);

        svg.append("g")
           .attr("id", "x-axis")
           .attr("transform", `translate(0, ${height - padding})`)
           .call(xAxis);

        svg.append("g")
           .attr("id", "y-axis")
           .attr("transform", `translate(${padding}, 0)`)
           .call(yAxis);

        svg.selectAll(".bar")
           .data(dataset)
           .enter()
           .append("rect")
           .attr("class", "bar")
           .attr("x", d => xScale(new Date(d[0])))
           .attr("y", d => yScale(d[1]))
           .attr("width", xScale.bandwidth())
           .attr("height", d => height - padding - yScale(d[1]))
           .attr("data-date", d => d[0])
           .attr("data-gdp", d => d[1])
           .on("mouseover", (event, d) => {
              const tooltip = d3.select("#tooltip");
              tooltip.style("display", "block")
                     .style("left", `${event.pageX + 5}px`)
                     .style("top", `${event.pageY - 28}px`)
                     .attr("data-date", d[0])
                     .html(`Date: ${d[0]}<br>GDP: ${d[1]} Billion`);
           })
           .on("mouseout", () => {
              d3.select("#tooltip").style("display", "none");
           });
      });
  </script>
</body>
</html>
