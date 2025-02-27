# Road Profile Texture Features Calculation: MATLAB & Python Tutorial

## **Introduction**
This tutorial guides you through the process of calculating **road profile macrotexture and megatexture features**. The tutorial includes filtering road profile data using a Butterworth filter in **MATLAB** and **Python** and covers:

- **Reading road profile data** from a CSV file.
- **Designing and applying Butterworth filters** to extract  wavelength of interest.
- **Computing Mean Segment Depth (MSD)** for macrotexture.
- **Computing Root Mean Square (RMS)** for megatexture.

For specifications of wavelengths and more details see ISO 13473-1 [1] and ISO/FDIS 13473-5 [2] for macrotexture and megatexture, respectively. 
### **References**  

[1] EN ISO 13473-1: Characterization of pavement texture by use of surface profiles - Part 1: Determination of Mean Profile Depth.  

[2] Pr EN ISO/DIS 13473-5: Characterization of pavement texture by use of surface profiles - Part 5: Determination of Megatexture.  


## Tutorials  

- [MATLAB Tutorial](./MATLAB/Road_Profile_Filtering_MATLAB.md)  
- [Python Tutorial](./Python/Road_Profile_Filtering_Python.md)  

Each tutorial provides a structured implementation of filtering and road texture feature calculations.  

---
## **Data File**  

The road profile data is provided in a **CSV file** located in the repository:  

**[ProfileData.csv](./data/ProfileData.csv)**  

This file contains the raw road profile measurements required for texture feature calculations. Both the **MATLAB** and **Python** tutorials include instructions on how to read and process this data.  

### **CSV File Format:**  
| Column | Description |
|--------|------------|
| Distance_m | Longitudinal position along the road profile in meters |
| Profile_mm | Surface profile at each measurement point in millimeters|

Make sure to download this file when running the provided tutorials.  

---
## Instructions  

1. **MATLAB Users**  
   - Open `Road_Profile_Filtering_MATLAB.md` for step-by-step guidance.  
   - Copy and run the MATLAB code snippets in your workspace.  

2. **Python Users**  
   - Open `Road_Profile_Filtering_Python.md` for step-by-step guidance.  
   - Run the provided Python scripts in **Jupyter Notebook** or a **Python IDE** (e.g., Spyder, VS Code, PyCharm).  
   

## Additional Notes  

Python users need:
- **NumPy** and **Scipy** for calculations and filtering.  
- **matplotlib** for plotting
- **pandas** for reading CSV files (or another suitable CSV-handling library). 