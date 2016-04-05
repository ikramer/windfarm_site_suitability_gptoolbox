#Wind Farm Site Suitability – Weighted Linear Combination Model

This repository contains an ArcGIS toolbox for determining site suitability for wind farms. A weighted linear combination (WLC) model was used to determine areas that are suitable for wind farms. This model used the following data sets as inputs: slope, proximity to migratory bird paths, mean wind speed, proximity to transmission lines, proximity to populated areas, and proximity to ridges.   

Please keep in mind this is a simplistic model for finding ideal locations for wind farms.  This toolbox was developed for a school project and is meant to be an example of how to perform a site suitability analysis using the weighted linear combination approach.

You can find a more detailed description of the methodology behind the tool at this blog post.

The WindFarm toolbox (.tbx file) contains three models, which include: Pass/Fail Model, Ridge Locator, and Wind Farm Locator.  

The data used for this model can be downloaded from Amazon S3: [WindFarm File Geodatabase] (http://githubik.s3-website-us-west-2.amazonaws.com/WindFarm.gdb.zip)

##How to Install
1. Copy the WindFarm.tbx and WindFarm.gdb to a folder. 
2. Create a folder called “Scratch” in the same location.
3. For each model, set the Scratch variable to point to the new Scratch location.
4. For each model, re-point the input data variables to the location of the layers in the WindFarm.gdb geodatabase.

##Ridge Locator
Ridge locations were derived using the ArcGIS platform’s hydrologic processes.   The ridge identification process used a digital elevation model (DEM).  The sinks in the DEM was corrected using the FILL tool.  The flow direction was then calculated from the resulting filled raster.  The FLOW ACCUMULATION tool was then used to derive the tops of each watershed.  The resulting raster was then reclassified to identify each cell with a value of zero, which is the highest elevation in the flow accumulation raster.  The Euclidean distance was then calculated for the ridge raster and later used in the WLC model.

![Ridge Locator Model]( http://githubik.s3-website-us-west-2.amazonaws.com/wfwlc_images/RidgeLocatorModel.png)

##Pass/Fail Model

The Pass/Fail model was used to generate the factor constraints (i.e. exclusion) layer for the WLC method.  The factor constraints were composed of several layers that represented areas that are unsuitable for wind farms.  These layers were composed of features represented political, natural, residential, and potential nuisance boundaries. The current model uses the following data layers: 
- National Forests
- State Gamelands
- Public Airports
- Wild Natural Areas
- Urban Areas
- State Forest Lands
- State Parks
- Land Use
- National Wetland Inventory

The main components of the Pass/Fail model converted the vector features to a raster data set and then reclassify the cells as either suitable or unsuitable.  The model also used the RECLASSIFY tool to separate out unsuitable land uses which included: residential, industrial, airport, roads, active mines, and water.  All of the remaining land uses were deem potentially suitable to host a wind farm.  The Pass/Fail model also calculated a sound buffer layer around the residential land use.  The model extracted out the residential land use and calculated Euclidean distance from each residential cell.  The buffer was than reclassified to set any cell with 350m of residential land use as unsuitable.  After each of the layers were reclassified, all of the layers were multiplied together to create the exclusion layer.

![Pass/Fail Model]( http://githubik.s3-website-us-west-2.amazonaws.com/wfwlc_images/PassFailModel.png)

An example of an exclusion layer:

![Exclusion Area Example]( http://githubik.s3-website-us-west-2.amazonaws.com/wfwlc_images/ExclusionAreaExample.png)

##Wind Farm WLC Model

The WLC model performs the reclassification and multiplication for the six input layers.  The model starts by generating a Euclidean distance raster based on the transmission lines and migratory bird paths.  The remaining four layers were already in a raster-based format.  The six layers are then reclassified based on the factor ratings.  Each of the factor rating rasters are then multiplied by the factor constraint layer from the Pass/Fail model.  The calculated weights are multiplied by the five layers (excluding wind) that participate in the WLC method.  Once each layers’ weights are calculated, the six layers are added together to generate the wind farm site suitability raster.  This resulting raster contains the overall scores that resulted from the modified WLC model.  The model then removes all of the zero values from the site suitability raster.      

![Wind Farm WLC Model]( http://githubik.s3-website-us-west-2.amazonaws.com/wfwlc_images/WLCModel.png)

The output from the WLC model is then reclassified to show 4 ranges of scores.  Here is an example of the output.

![WLC Model Example]( http://githubik.s3-website-us-west-2.amazonaws.com/wfwlc_images/WLCFinalOutputExample.png)


