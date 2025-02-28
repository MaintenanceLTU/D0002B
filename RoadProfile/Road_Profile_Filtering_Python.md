# **Python Tutorial*
Road Profile Texture Features Calculation
## **Step 1: Load the Road Profile Data**
The road profile data is stored in a CSV file. We first need to load and visualize it.
```python
# Include libraries
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import scipy.signal as signal
import os

# Load CSV data (you will need to modify path to where you file is located)
p = '<ENTER_FILE_PATH_HERE>';
filename = os.path.join(p, 'ProfileData.csv')
ProfileData = pd.read_csv(filename)

# Extract distance and profile
distance = ProfileData['Distance_m']
profile = ProfileData['Profile_mm']
# dx represents the sampling interval, assuming uniform spacing in data
dx = distance.iloc[1]
```
### **Plot the raw road profile**
```python
# Plot raw profile
plt.figure()
plt.plot(distance, profile)
plt.xlabel('Distance (m)')
plt.ylabel('Profile (mm)')
plt.title('Raw Road Profile')
plt.grid()
plt.show()
```

---
## **Step2: Macrotexture**
### **Design and Apply the Butterworth Filter**
To extract wavelength of interest, we apply high-pass and low-pass filters.
```python
# Define cutoff frequencies
# High-pass removes wavelengths longer than this limit
limit_macro_high = <ENTER_HIGH_PASS_WAVELENGTH_HERE> # High-pass cutoff, wavelength (m)
# Low-pass removes wavelengths shorter than this limit
limit_macro_low = <ENTER_LOW_PASS_WAVELENGTH_HERE> #Wavelength (m)

f_high = 1 / limit_macro_high
f_low = 1 / limit_macro_low

# Design Butterworth filters
Bh, Ah = signal.butter(2, f_high * (2 * dx), btype='high')
Bl, Al = signal.butter(2, f_low * (2 * dx), btype='low')
```
Using `filtfilt()`, we apply **zero-phase filtering** to the profile data.

```python
# Applying high-pass followed by low-pass filtering to extract macrotexture
mpd_profile = signal.filtfilt(Bl, Al, signal.filtfilt(Bh, Ah, profile))
```
### **Compute MSD (Mean Segment Depth)**
Extract 100 mm of data and compute mean segment depth.
```python
# Specify 100 mm segment
i_segment = (distance >= 0) & (distance < 100e-3)

# Plot filtered data for segment
plt.figure()
plt.plot(distance[i_segment], mpd_profile[i_segment])
plt.xlabel('Distance (m)')
plt.ylabel('Profile (mm)')
plt.title('Filtered Road Profile (Macrotexture)')
plt.grid()
plt.show()

```
From the plot, the maximum values ​​for the two consecutive 50 mm segments can be read. It can also be implemented as follows.

```python
# Extracting maximum values from two consecutive 50mm segments
i_segment_1 = (distance >= 0) & (distance < 50e-3)
i_segment_2 = (distance >= 50e-3) & (distance < 100e-3)

max_1 = np.max(mpd_profile[i_segment_1])
max_2 = np.max(mpd_profile[i_segment_2])

MSD = (max_1 + max_2) / 2
print(f"MSD Value: {MSD:.4f}")
```

---

## **Step 3: Compute RMS for Mega Texture**
We use a band-pass filter between relevant wavelength (50 mm and 500 mm).
```python
# Define wavelength limits for megatexture filtering (i.e. 50mm to 500mm)
# High-pass removes wavelengths longer than the defined limit
limit_mega_high = <ENTER_HIGH_PASS_WAVELENGTH_HERE>
# Low-pass removes wavelengths shorter than the defined limit
limit_mega_low = <ENTER_LOW_PASS_WAVELENGTH_HERE>

# Design Butterworth band-pass filter
f_mega = [1 / limit_mega_high, 1 / limit_mega_low]
B, A = signal.butter(3, np.array(f_mega) * (2 * dx), btype='bandpass')

# Applying band-pass filter using zero-phase filtering
mega_profile = signal.filtfilt(B, A, profile)

# Compute rms value of 1 m segment
i_segment_1m = (distance >= 0) & (distance < 1)
rms_mega = np.sqrt(np.mean(mega_profile[i_segment_1m] ** 2))
print(f"RMS of Mega Texture: {rms_mega:.3f}")
```
### **Optional plot for mega texture data**
```python
# Plot filtered data for 1 m segment
plt.figure()
plt.plot(distance[i_segment_1m], mega_profile[i_segment_1m])
plt.xlabel('Distance (m)')
plt.ylabel('Profile (mm)')
plt.title('Filtered Road Profile (Megatexture)')
plt.grid()
plt.show()
```
---


