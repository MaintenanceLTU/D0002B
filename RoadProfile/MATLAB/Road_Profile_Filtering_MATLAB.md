# **MATLAB Tutorial** 
Road Profile Texture Features Calculation

## **Step 1: Load the Road Profile Data**
The road profile data is stored in a CSV file. We first need to load and visualize it.

```matlab
% Define the file path (you will need to modify path to where you file is located)
p = '<ENTER_FILE_PATH_HERE>'; 
filename = fullfile(p, 'ProfileData.csv');

% Read the CSV file
ProfileData = readtable(filename);

% Extract sampling interval (assuming uniform spacing)
dx = ProfileData.Distance_m(2); 
```
### **Plot the raw road profile**
```matlab
% Plot raw profile data
figure;
plot(ProfileData.Distance_m, ProfileData.Profile_mm);
xlabel('Distance (m)');
ylabel('Profile (mm)');
title('Raw Road Profile');
grid on;
```

---
## **Step 2: Macrotexture**
### **Design and Apply the Butterworth Filter**
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
Using `filtfilt()`, we apply **zero-phase filtering** to the profile data.

```matlab
% Applying high-pass followed by low-pass filtering to extract macrotexture
mpdProfile = filtfilt(Bl, Al, filtfilt(Bh, Ah, ProfileData.Profile_mm));
```
### **Compute MSD (Mean Segment Depth)**
Extract 100 mm of data and compute mean segment depth.
```matlab
% Specify 100 mm segment
i_segment = ProfileData.Distance_m >= 0 & ProfileData.Distance_m < 100e-3;

% Plot filtered data for segment
figure;
plot(ProfileData.Distance_m(i_segment), mpdProfile(i_segment));
xlabel('Distance (m)');
ylabel('Filtered Profile (mm)');
title('Filtered Road Profile (Macrotexture)');
grid on;
```
From the plot, the maximum values ​​for the two consecutive 50 mm segments can be read. It can also be implemented as follows.

```matlab
% Extracting maximum values from two consecutive 50mm segments
i_segment_1 = ProfileData.Distance_m >= 0 & ProfileData.Distance_m < 50e-3;
i_segment_2 = ProfileData.Distance_m >= 50e-3 & ProfileData.Distance_m < 100e-3;

max_1 = max(mpdProfile(i_segment_1));
max_2 = max(mpdProfile(i_segment_2));

MSD = (max_1 + max_2) / 2;
fprintf('MSD Value: %.4f mm\n', MSD);
```

---

## **Step 3: Compute RMS for Mega Texture**
We use a **band-pass filter** between relevant wavelengths (50 mm and 500 mm).

```matlab
% Define cutoff frequencies
% High-pass removes wavelengths longer than the defined limit
limitMegaHigh = <ENTER_HIGH_PASS_WAVELENGTH_HERE>; %Wavelength (m)
% Low-pass removes wavelengths shorter than the defined limit
limitMegaLow = <ENTER_LOW_PASS_WAVELENGTH_HERE>; %Wavelength (m)

% Design Butterworth band-pass filter
limitMega = [limitMegaHigh, limitMegaLow]; %Wavelength (m)
fMega = 1 ./ limitMega;
[B, A] = butter(3, fMega * (2 * dx), 'bandpass');

% Applying band-pass filter using zero-phase filtering
megaProfile = filtfilt(B, A, ProfileData.Profile_mm);

% Compute rms value of 1 m segment
i_segment_1m = ProfileData.Distance_m >= 0 & ProfileData.Distance_m < 1;
rms_mega = rms(megaProfile(i_segment_1m));
fprintf('RMS of Mega Texture: %.3f mm\n', rms_mega);
```
### Optional plot for mega texture data 
```matlab
% Plot filtered data for 1 m segment
figure;
plot(ProfileData.Distance_m(i_segment_1m), mpdProfile(i_segment_1m));
xlabel('Distance (m)');
ylabel('Filtered Profile (mm)');
title('Filtered Road Profile (Megatexture)');
grid on;
```
---