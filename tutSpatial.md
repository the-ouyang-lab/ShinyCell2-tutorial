---
title: | 
  | Tutorial for creating a ShinyCell2 app for spatial data
author: |
  | Yi Yang Lim, Beijun Chen, John F. Ouyang
date: "Apr 2025"
output:
  html_document: 
    toc: true
    toc_depth: 2
    toc_float: 
      collapsed: false
  pdf_document: default
fontsize: 12pt
pagetitle: "tutSpatial"
---


# Introduction
This tutorial guides users on how to create a ShinyCell2 app from spatial data 
processed using the Seurat pipeline. The eventual output will be a sharable web 
app that allows for dynamic data exploration.

In this tutorial, we will be using the publicly available sagital mouse brain 
slices generated using the 10x Visium v1 chemistry, processed using the Seurat 
spatial pipeline (https://satijalab.org/seurat/articles/spatial_vignette). The 
spatial dataset is available as a Seurat object that can be downloaded as 
`spatial_brain.rds` in this [Zenodo repository](https://zenodo.org/records/15162323).

# Importing Seurat-based spatial data into ShinyCell2
To begin, we will load the required packages and the processed Seurat object: 

``` r
library(Seurat)
library(ShinyCell2)

seu <- readRDS("spatial_brain.rds")
```

## Configuring ShinyCell2 for optimal visualisation of spatial scRNA-seq data
Next, we will use the `createConfig()` function to generate a ShinyCell2 config 
object named `scConf`. This object is a `data.table` that specifies: (i) which 
single-cell metadata to display in the Shiny app, (ii) the ordering of levels 
for categorical metadata (e.g., library or cluster), and (iii) the color palette 
assigned to each metadata variable. Essentially, `scConf` serves as an 
"instruction manual" for defining the aesthetics of the eventual ShinyCell2 app, 
without altering the original single-cell data. Here, we removed some metadata 
that we deem redundant using the `delMeta()` function. For more details on how 
to customise the ShinyCell2 config, refer to this [Tutorial for customising ShinyCell aesthetics](
https://htmlpreview.github.io/?https://github.com/the-ouyang-lab/ShinyCell2-tutorial/master/docs/aesthetics.html)

``` r
scConf <- createConfig(seu)
scConf <- delMeta(scConf, c("orig.ident", "slice", "region"))
```

# Create Shiny Files
To generate the ShinyCell2 app, we first run the `makeShinyFiles()` function. 
This function generates the necessary data files (including the single-cell 
metadata and assay etc). The main inputs are the Seurat object `seu`, the 
ShinyCell2 config `scConf`, an unique prefix for the dataset specified by 
`shiny.prefix = "sc1"` and the output directory `shiny.dir`. Users can also 
specify the reduced dimensions to include as well as plotting defaults. Note 
that this step can take a substantial amount of time to run as the whole 
dataset is being written in an efficient format for the ShinyCell2 app.

``` r
makeShinyFiles(seu, scConf = scConf, 
    shiny.prefix = "sc1", shiny.dir = "shinyApp_spatial/")
```

## Generate code for Shiny app
Finally, we will generate the code for the ShinyCell2 app using the 
`makeShinyCodes()` function. Here, we specify the same unique prefix 
`shiny.prefix = "sc1"` and output directory `shiny.dir`. This step should run 
quickly as only the R code scripts are being written.

``` r
makeShinyCodes(shiny.prefix = "sc1", 
               shiny.dir = "shinyApp_spatial/", 
               shiny.title = "mouse_brain") 
```

The generated shiny app can then be found in the `shinyApp_spatial/` folder. 
To run the app locally, use RStudio to open either 
`server.R` or `ui.R` in the shiny app folder and click on "Run App" in the top 
right corner. The shiny app can also be deployed online via online platforms 
e.g. [shinyapps.io](https://www.shinyapps.io/) and Amazon Web Services (AWS) 
or be hosted via Shiny Server. For further details, refer to 
[Instructions on how to deploy ShinyCell apps online](
https://htmlpreview.github.io/?https://github.com/the-ouyang-lab/ShinyCell2-tutorial/master/docs/cloud.html).

More details on the various visualisations in the `ShinyCell2` can be found in
[Additional information on new visualisations tailored for spatial / scATAC-seq / multiomics](
https://htmlpreview.github.io/?https://github.com/the-ouyang-lab/ShinyCell2-tutorial/master/docs/addNewVis.html)
and [Additional information on enhanced visualisation features](
https://htmlpreview.github.io/?https://github.com/the-ouyang-lab/ShinyCell2-tutorial/master/docs/addEnhanVis.html)

![](images/tutSpatial_interface.png)


