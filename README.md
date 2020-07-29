# CalcificationImaging
Image analysis code for in vitro calcification profiling

# Experimental setup
At t = 48 h after seeding, RPTEC were treated with ProxUp basal medium spiked with first, various concentrations of phosphate (final concentration between 1 and 7 mM) and second, calcium (final concentration between 1 and 7 mM), which was directly added to each well.

For the comparison of selected IP6 analogues, ProxUp basal medium was prepared with the selected inhibitor and added to each well. Then, CaP precipitation was induced by direct addition of first, phosphate and second, calcium. Final concentrations of 7 mM phosphate and 5 mM calcium were used. Cells were incubated for 24 h at 37°C and 5% CO2. Spiked medium was removed, and cells were washed twice with PBS before staining. 
For the assessment of CaP-induced CaOx crystallization, after 24 h of incubation, Ca/P spiked medium was removed, and cells were washed twice with PBS. Subsequently, medium containing 1.2 mM oxalate and compound was added to each well. After 4 h of incubation at 37°C and 5% CO2 treatment was removed, and cells washed once with PBS before staining.
For staining, ProxUp basal medium was mixed with calcein (500 nM final concentration), EthD (6 µM final concentration), CellMaskTM stain (5 µg/µL) and Hoechst 33342. Staining mixture was added to RPTEC and cells were incubated for 30 min in the dark at 37°C and 5% CO2. Staining solution was removed, cells were washed with PBS once and ProxUp basal medium was added. Cells were immediately imaged after staining. Images were obtained by epifluorescence microscopy at 37°C, using a Leica CTR6000 microscope. For quantification 3 wells/condition were prepared and 3 images/well were taken with a 20x objective 

# Image analysis scripts
Multichannel images were saved as individual channel images in 8-bit tiff format for further analysis. 

## Segmentation and features of channel images were extracted using the following scripts for CaP dose range and compound treated samples, respectively:
20200324_CaPimaging_featureExtraction-CaPrange;
20200324_CaPimaging_featureExtraction-INS3001

## Visualization of segmentation results was performed using the following scripts:
20200401_CaPimaging_segmentationVisual-CaPrange;
20200401_CaPimaging_segmentationVisual-INS3001

## Triplicate results were summarized and graphs prepared using the following script:
/Users/anna/Documents/PhD/CalcificationImaging/ImageAnalysis/analysis_vF;
20200324_CaPimaging_featureVisualization

# Image analysis brief explanation
For analysis Hoechst channel images were thresholded using the triangle threshold. Touching nuclei of the binary image were further segmented using the watershed algorithm with the distance transform of the binary image as input and local maxima thereof as seeds.
For EthD channel images, high level of background noise was observed, potentially as a result of fluorescence of the CellMaskTM stain bleeding through. Thus, it was found that using the 99 percentile of all intensity values per image as a threshold resulted in a good detection of foreground pixels. A fixed minimum threshold was set at 60. Watershed segmentation similar to the Hoechst channel was performed to segment touching objects. For the calcein channel images adaptive thresholding by triangle thresholding achieved good detection of CaP deposits, which was validated by visual comparison to brightfield channel images. For compound experiments, an additional upper threshold was set to the minimum threshold of the pos.ctrl images for the respective experiment. Thereby an overly insensitive threshold in images with a high amount of CaP deposition was circumvented. The sum of foreground pixels of the binary image was used to calculate the total area of CaP deposits. The skimage label algorithm was used to label touching foreground regions. 
To segment images to single cells the CellMaskTM channel image was first binarized by adaptive thresholding, using a blocksize of 35, followed by median filtering. Binary erosion was performed to shrink the outlines. A version of watershed algorithm was used to segment the whole image into single cells. To this end, the distance transform of the binary erosion image was used as input image and seeds were set to the local maxima of the Hoechst channel image. While this protocol allowed for an approximation of single cell morphology, further improvements of both analytical and experimental staining procedure would be necessary to achieve more accurate results. 
Additionally, texture features of calcein and CellMaskTM channel images were extracted. Grey level co-occurrence matrices were calculated using one set offset of 5 pixel and an angle of 90. Here, the use of larger offsets could improve the detection of changes in large-scale features, e.g. CaP clustering sites. Texture properties of the matrices extracted were contrast, dissimilarity, energy, ASM, homogeneity and correlation. Overlap between calcein and CellMaskTM channel images was measured by computing the structural similarity index matrix (SSIM). 

Features extracted included both single cell or single calcein patch features, as well as whole image features. Whole image features included a total cell count based on the Hoechst image, a total dead cell count based on the ethd channel image, SSIM, texture features of both calcein and CellMaskTM channel images and maximum, minimum, mean and standard deviation, total area and cluster count of calcein intensities. Single cell, single nuclei and single calcein patch features included shape and, only for calcein and cell, intensity properties and were summarized to median values per image. Properties included median area, extent, eccentricity, perimeter, solidity, major and minor axis length, as well as maximum, minimum and mean intensity per cell or CaP patch. Features were scaled ranging from 0 to 1 using the MinMaxScaler from the sklearn preprocessing package. The mean value of each image feature over 9 images (3 wells * 3 images/ well for each condition) per condition was calculated for each experiment and three independent experiments used for the final analysis. Analysis was performed in Python 3 using the numpy, pandas, skimage, sklearn and seaborn packages.

