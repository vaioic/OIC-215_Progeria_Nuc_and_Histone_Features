# OIC-#_Report

Total Hours:

## Authorship and Methods

Research supported by the Optical Imaging Core should be acknowledged and considered for authorship. Please refer to our [SharePoint page](https://vanandelinstitute.sharepoint.com/sites/optical/SitePages/Acknowledgements-and-Authorship.aspx) for guidelines.

Please include our RRID in the methods section for any research supported by the OIC. RRID:SCR_021968

### Sample Acknowledgement

We thank the Van Andel Institute Optical Imaging Core (RRID:SCR_021968), especially [staff name], for their assistance with [technique/technology]. This research was supported in part by the Van Andel Institute Optical Imaging Core (RRID:SCR_021968) (Grand Rapids, MI).

## Summary of Request

Create two workflows:

1. Analyze IF data in 3D
   1. In Python
   2. Nuclear shape descriptors
   3. IF intensity measurements for all channels (including DAPI)
      1. Explore IF distribution and texture measurements
      2. DAPI appears more punctate, want to capture that

2. Analyze DAB-stained images for binary yes/no presence of markers of interest in tissue samples
   1. In QuPath

## Brief summary of analysis pipeline

### DAB Staining Analysis

A QuPath project was generated for each stain. Each tissue had an annotation drawn on it to outline the area to analyze, within each of those regions the nuclei were detected using cellpose. From there a trained object classifier was used to classify each nuclei (detection) as positive or negative for DAB staining. Each marker has it's own specially trained model for removing false nuclei detections and classifying the cells as positive/negative for DAB.

### IF Analysis


## Data

### DAB-Stained Tissues

RGB tiffs collected on the Leica Aperio imaging system at 40X. Tissues stained for different proteins of interest using DAB-staining IHC.

Markers of interest:

- Beta Galac
- gH2AX
- H3K9me3
- H3K27me3
- LMNA
- p16
  
## Analysis Pipeline

### Whole slide IHC Analysis (DAB stained samples)

A QuPath project was generated for each stain to accommodate for different staining patterns. Each tissue had an annotation drawn on it to outline the tissue region to analyze, additionally, dermis samples had the hair follicles outlined to quantify the number of +/- cells within. A training image was generated with 1-2 2048x2048 ROIs from all tissues for each stain. These training images were used to determine detection and classification parameters for each QuPath project.

Cellpose(v.3.1) was used to detect the nuclei of the cells using a summed image of the deconvolved Hematoxylin and DAB colors with a median filter applied. Because non-nuclei pixels were segmented by cellpose, an object classifier was then trained to classify true and false detections. False detections were removed from the image. Then a classifier was trained to classify cells as positive or negative for DAB staining. These steps were compiled into a [custom script](/Scripts/CellPose_Summed_Deconvolved_Stains.groovy) to run as a batch analysis in QuPath. Note that the `Tissue` outlines were used for directing the analysis. The `Follicle` and `Training` ROIs will also contain cell and classification counts, but these counts do not need to be merged with the `Tissue` annotation counts because of the way QuPath handles hierarchal parent-child object relationships.

## Output

### Whole slide IHC data

Quantification of the IHC data were kept to a binary count of the number of cells detected and classified as +/- for DAB. These counts were exported as a csv file containing all quantification for each tissue within a given marker dataset.

| Column | Definition |
|-|-|
| Image | Image ID that the data belong to, should match the URL in the `combined_aperio_pathlog.csv` file |
| Object ID | Unique identifier for each annotation (outline) created |
| Classification | Assigned class to the annotation (Tissue = whole tissue outline; follicle = hair follicles in the dermic samples, Training = ROI used for creating training image) |
| Area $\mu$m^2^ | Area of each annotation in microns squared |
| Num Detections | Total number of cells detected in the annotation |
| Num Negative | Total number of cells detected in the annotation that are negative for DAB staining |
| Num Positive | Total number of cells detected in the annotation that are positive for DAB staining |
| Positive % | Percentage of cells positive for DAB staining (Num Positive / Num Detections)*100 |
| Num Positive per mm^2^ | Density measurement that calculates how many positive cells there are in the annotation per mm^2 (Num positive/ Area(mm^2))

## Notes

### Optional Analyses - what other information could you get from this data