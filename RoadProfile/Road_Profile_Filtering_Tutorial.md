# Road Profile Filtering: MATLAB & Python Tutorial

## **Introduction**
This tutorial guides students through the process of filtering road profile data using a Butterworth filter in **MATLAB** and **Python**. It covers:

- **Reading road profile data** from a CSV file.
- **Designing and applying Butterworth filters** to extract  wavelength of interest.
- **Computing Maximum Profile Slope (MPS)** for macrotexture.
- **Computing Root Mean Square (RMS)** for megatexture.

---

## **MATLAB Tutorial**

### **Step 1: Load the Road Profile Data**
The road profile data is stored in a CSV file. We first need to load and visualize it.

```matlab
% Define the file path
p = 'C:\Users\YourUsername\';
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
To remove unwanted frequency components, we apply **high-pass** and **low-pass** filters.

```matlab
% Define cutoff frequencies
limitMacroHigh = ______; % High-pass cutoff, wavelength (m)
limitMacroLow = ______; % Low-pass cutoff, wavelength (m)
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
mpdProfile = filtfilt(Bl, Al, filtfilt(Bh, Ah, ProfileData.Profile_mm));

% Plot filtered data for segment (100 mm)
figure;
i_segment = ProfileData.Distance_m >= 0 & ProfileData.Distance_m < 100e-3)
plot(ProfileData.Distance_m(i_segment), mpdProfile(i_segment));
xlabel('Distance (m)');
ylabel('Filtered Profile (mm)');
title('Filtered Road Profile (Macrotexture)');
grid on;
```

---

### **Step 4: Compute MPS (Max Profile Slope)**

```matlab
i_segment_1 = ProfileData.Distance_m >= 0 & ProfileData.Distance_m < 50e-3
i_segment_2 = ProfileData.Distance_m >= 50e-3 & ProfileData.Distance_m < 100e-3
max_1 = max(mpdProfile(i_segment_1));
max_2 = max(mpdProfile(i_segment_2));
mps = (max_1 + max_2) / 2;
disp(['MPS Value: ', num2str(mps)]);
```

---

### **Step 5: Compute RMS for Mega Texture**
We use a **band-pass filter** between 50 mm and 500 mm.

```matlab
limitMegaHigh = _____; %Wavelength (m)
limitMegaLow = _____; %Wavelength (m)
limitMega = [limitMegaHigh, limitMegaLow]; %Wavelength (m)
fMega = 1 ./ limitMega;
[B, A] = butter(3, fMega * (2 * dx), 'bandpass');
megaProfile = filtfilt(B, A, ProfileData.Profile_mm);

# Compute rms value
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

# Load CSV data
p = r'C:\Users\YourUsername\ProfileData.csv'
ProfileData = pd.read_csv(p)

# Extract distance and profile
distance = ProfileData['Distance_m']
profile = ProfileData['Profile_mm']
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
limit_high = 174.2e-3
limit_low = 2.4e-3
f_high = 1 / limit_high
f_low = 1 / limit_low

Bh, Ah = signal.butter(2, f_high * (2 * dx), btype='high', analog=False)
Bl, Al = signal.butter(2, f_low * (2 * dx), btype='low', analog=False)
```

---

### **Step 3: Apply the Filter**

```python
mpd_profile = signal.filtfilt(Bl, Al, signal.filtfilt(Bh, Ah, profile))

plt.figure()
plt.plot(distance, mpd_profile)
plt.xlabel('Distance (m)')
plt.ylabel('Filtered Profile (mm)')
plt.title('Filtered Road Profile (Macrotexture)')
plt.grid()
plt.show()
```

---

### **Step 4: Compute MPS**

```python
m1 = np.max(mpd_profile[(distance >= 0) & (distance < 50e-3)])
m2 = np.max(mpd_profile[(distance >= 50e-3) & (distance < 100e-3)])
mps = (m1 + m2) / 2
print(f"MPS Value: {mps}")
```

---

### **Step 5: Compute RMS for Mega Texture**

```python
limit_mega = [500e-3, 50e-3]
f_mega = [1 / limit_mega[1], 1 / limit_mega[0]]

B, A = signal.butter(3, np.array(f_mega) * (2 * dx), btype='bandpass')
mega_profile = signal.filtfilt(B, A, profile)

rms_mega = np.sqrt(np.mean(mega_profile[(distance >= 0) & (distance < 1)] ** 2))
print(f"RMS of Mega Texture: {rms_mega}")
```

---

## **Conclusion**
This tutorial covered:
✅ **Loading data**  
✅ **Filtering with Butterworth filters**  
✅ **Computing MPS & RMS**  

Students should now be able to **implement their own filters** and analyze road profile data. 🚀
