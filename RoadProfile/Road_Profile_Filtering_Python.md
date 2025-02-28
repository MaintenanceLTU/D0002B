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
MSD = (max_1 + max_2) / 2
print(f"MSD Value: {MSD:.4f}")
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
print(f"RMS of Mega Texture: {rms_mega:.3f}")
```

---


