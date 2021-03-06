
<!-- README.md is generated from README.Rmd. Please edit that file -->
based off of [Chris Given](https://twitter.com/cmgiven) [`d3.js`](http://d3js.org) zoomable interactive [treemap bar](http://bl.ocks.org/cmgiven/4541f6de7b6fbef482aaa43f3a71f8d4)

### basic example

``` r
#devtools::install_github("timelyportfolio/treebar")

library(stringr)
library(treebar)
library(jsonlite)

## make it a more generic hierarchy
##  normally this step is not necessary
json <- str_replace_all(
  readLines(system.file("example/data.json",package="treebar")),
  "(country)|(continent)|(year)|(type)",
  "id"
)

data <- fromJSON(json, simplifyDataFrame=FALSE)

treebar(data)
```

![](README-unnamed-chunk-2-1.png)

### explore tiling options

``` r
# also allows different treemap tiling options
library(htmltools)

browsable(
  tagList(
    lapply(
      c("Squarify", "Binary", "SliceDice", "Slice", "Dice"),
      function(tile){
        tags$div(
          style = "float:left; display:inline;",
          tags$h3(tile),
          treebar(
            data,
            tile = tile, 
            height = 250,
            width = 400
          )
        )
      }
    )   
  )
)
```

![](README-unnamed-chunk-4-1.png)


### exploring id and value options

``` r
# use different key for id and value
json <- str_replace_all(
  readLines("./inst/example/data.json"),
  "(country)|(continent)|(year)|(type)",
  "name"
)
#> Warning in readLines("./inst/example/data.json"): incomplete final line
#> found on './inst/example/data.json'

json <- str_replace_all(
  json,
  "(value)",
  "size"
)

data <- fromJSON(json, simplifyDataFrame=FALSE)

treebar(data, value="size", id="name")
```

### shiny

```
#devtools::install_github("timelyportfolio/treebar")

library(stringr)
library(treebar)
library(jsonlite)
library(shiny)

## make it a more generic hierarchy
##  normally this step is not necessary
json <- str_replace_all(
  readLines(system.file("example/data.json",package="treebar")),
  "(country)|(continent)|(year)|(type)",
  "id"
)

data <- fromJSON(json, simplifyDataFrame=FALSE)

shinyApp(
  ui = htmlwidgets::onRender(
    treebar(data),
    htmlwidgets::JS(
'
function(el, x){
  var chart = HTMLWidgets.getInstance(el).instance.treebar;
  chart.on("nodeMouseover", function(d,i){
    Shiny.onInputChange("treebar_mouseover", d.data);
  });
}
'    
    )
  ),
  server = function(input, output, session){
    observeEvent(input$treebar_mouseover,{
      print(input$treebar_mouseover)
    })
  }
)
```
