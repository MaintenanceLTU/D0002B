# Road Profile Filtering: MATLAB & Python Tutorial

## **Introduction**
This tutorial guides students through the process of filtering road profile data using a Butterworth filter in **MATLAB** and **Python**. It covers:

- **Reading road profile data** from a CSV file.
- **Designing and applying Butterworth filters** to extract  wavelength of interest.
- **Computing Mean Segment Depth (MSD)** for macrotexture.
- **Computing Root Mean Square (RMS)** for megatexture.

---

## **MATLAB Tutorial**

### **Step 1: Load the Road Profile Data**
The road profile data is stored in a CSV file. We first need to load and visualize it.

```matlab
% Define the file path (you will need to modify path to where you file is located)
p = '<ENTER_FILE_PATH_HERE>'; 
filename = fullfile(p, 'ProfileData.csv');

% Read the CSV file
ProfileData = readtable(filename);

% Extract sampling interval (assuming uniform spacing)
dx = ProfileData.Distance_m(2); 

% Plot raw profile data
figure;
plot(ProfileData.Distance_m, ProfileData.Profile_mm);
xlabel('Distance (m)');
ylabel('Profile (mm)');
title('Raw Road Profile');
grid on;
```

---

### **Step 2: Design a Butterworth Filter**
To extract wavelength of interest, we apply **high-pass** and **low-pass** filters.

```matlab
% Define cutoff frequencies
% High-pass removes wavelengths longer than the defined limit
limitMacroHigh = <ENTER_HIGH_PASS_WAVELENGTH_HERE>; % High-pass cutoff, wavelength (m)
% Low-pass removes wavelengths shorter than the defined limit
limitMacroLow = <ENTER_LOW_PASS_WAVELENGTH_HERE>; % Low-pass cutoff, wavelength (m)
fHigh = 1 / limitMacroHigh;
fLow = 1 / limitMacroLow;

% Design Butterworth filters
[Bh, Ah] = butter(2, fHigh * (2 * dx), 'high');
[Bl, Al] = butter(2, fLow * (2 * dx), 'low');
```

---

### **Step 3: Apply the Filter**
Using `filtfilt()`, we apply **zero-phase filtering** to the profile data.

```matlab
% Applying high-pass followed by low-pass filtering to extract macrotexture
mpdProfile = filtfilt(Bl, Al, filtfilt(Bh, Ah, ProfileData.Profile_mm));

% Plot filtered data for segment (100 mm)
figure;
i_segment = ProfileData.Distance_m >= 0 & ProfileData.Distance_m < 100e-3
plot(ProfileData.Distance_m(i_segment), mpdProfile(i_segment));
xlabel('Distance (m)');
ylabel('Filtered Profile (mm)');
title('Filtered Road Profile (Macrotexture)');
grid on;
```

---

### **Step 4: Compute MSD (Mean Segment Depth)**

```matlab
% Extracting maximum profile slope (MPS) from two consecutive 50mm segments
i_segment_1 = ProfileData.Distance_m >= 0 & ProfileData.Distance_m < 50e-3
i_segment_2 = ProfileData.Distance_m >= 50e-3 & ProfileData.Distance_m < 100e-3
max_1 = max(mpdProfile(i_segment_1));
max_2 = max(mpdProfile(i_segment_2));
msd = (max_1 + max_2) / 2;
disp(['MSD Value: ', num2str(msd)]);
```

---

### **Step 5: Compute RMS for Mega Texture**
We use a **band-pass filter** between relevant wavelengths (i.e. 50 mm and 500 mm).

```matlab
limitMegaHigh = <ENTER_HIGH_PASS_WAVELENGTH_HERE>; %Wavelength (m)
limitMegaLow = <ENTER_LOW_PASS_WAVELENGTH_HERE>; %Wavelength (m)
limitMega = [limitMegaHigh, limitMegaLow]; %Wavelength (m)
fMega = 1 ./ limitMega;
[B, A] = butter(3, fMega * (2 * dx), 'bandpass');
megaProfile = filtfilt(B, A, ProfileData.Profile_mm);

% Compute rms value
i_segment_1m = ProfileData.Distance_m >= 0 & ProfileData.Distance_m < 1
rms_mega = rms(megaProfile(i_segment_1m));
disp(['RMS of Mega Texture: ', num2str(rms_mega)]);
```

---

## **Python Tutorial**

### **Step 1: Load the Road Profile Data**

```python
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

### **Step 2: Design a Butterworth Filter**

```python
# High-pass removes wavelengths longer than this limit
limit_macro_high = <ENTER_HIGH_PASS_WAVELENGTH_HERE> # High-pass cutoff, wavelength (m)
# Low-pass removes wavelengths shorter than this limit
limit_macro_low = <ENTER_LOW_PASS_WAVELENGTH_HERE> #Wavelength (m)
f_high = 1 / limit_macro_high
f_low = 1 / limit_macro_low

Bh, Ah = signal.butter(2, f_high * (2 * dx), btype='high')
Bl, Al = signal.butter(2, f_low * (2 * dx), btype='low')
```

---

### **Step 3: Apply the Filter**

```python
# Applying high-pass followed by low-pass filtering to extract macrotexture
mpd_profile = signal.filtfilt(Bl, Al, signal.filtfilt(Bh, Ah, profile))

i_segment = (distance >= 0) & (distance < 100e-3)
plt.figure()
plt.plot(distance[i_segment], profile[i_segment])
plt.xlabel('Distance (m)')
plt.ylabel('Profile (mm)')
plt.title('Filtered Road Profile (Macrotexture)')
plt.grid()
plt.show()

```

---

### **Step 4: Compute MSD**

```python
i_segment_1 = (distance >= 0) & (distance < 50e-3)
i_segment_2 = (distance >= 50e-3) & (distance < 100e-3)
# Extracting mean segment depth (msd) from two consecutive 50mm segments
max_1 = np.max(mpd_profile[i_segment_1])
max_2 = np.max(mpd_profile[i_segment_2])
msd = (max_1 + max_2) / 2
print(f"msd Value: {msd}")
```

---

### **Step 5: Compute RMS for Mega Texture**
We use a band-pass filter between 50 mm and 500 mm.
```python
# Define wavelength limits for megatexture filtering (i.e. 50mm to 500mm)
limit_mega_high = <ENTER_HIGH_PASS_WAVELENGTH_HERE>
limit_mega_low = <ENTER_LOW_PASS_WAVELENGTH_HERE>
f_mega = [1 / limit_mega_high, 1 / limit_mega_low]
B, A = signal.butter(3, np.array(f_mega) * (2 * dx), btype='bandpass')
mega_profile = signal.filtfilt(B, A, profile)

i_segment_1m = (distance >= 0) & (distance < 1)
rms_mega = np.sqrt(np.mean(mega_profile[i_segment_1m] ** 2))
print(f"RMS of Mega Texture: {rms_mega}")
```

---


