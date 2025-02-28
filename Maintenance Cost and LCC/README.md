# Maintenance Cost and Availability Analysis in MATLAB

## **Basic Calculation of Maintenance Costs and Availability**
This section calculates key maintenance parameters, including downtime, uptime, availability, and costs.

```matlab
%% Define Interest Rate and Calculation Period
InterestRate = 0.1; % Discount rate
LifeLength = 5; % Analysis period in years

%% Define Maintenance Times
TotalTime = 365*24; % Total hours per year

% Preventive Maintenance
NumberOfLongPM = 1; % One major preventive maintenance stop
NumberOfPreventiveMaintenance = NumberOfLongPM;
MPMT = 2*7*24; % Mean preventive maintenance time in hours

% Corrective Maintenance
NumberOfFailures = 28; % Number of failures per year
MTTR = 0.8*24; % Mean time to repair (hours)
MWT_CM = 0.2*24; % Mean waiting time for corrective maintenance (hours)

% Calculate Downtime and Availability
PreventiveMaintenanceTime = NumberOfPreventiveMaintenance*MPMT;
RepairTime = MTTR*NumberOfFailures;
CorrectiveMaintenanceTime = RepairTime + MWT_CM*NumberOfFailures;
Downtime = PreventiveMaintenanceTime + CorrectiveMaintenanceTime;
Uptime = TotalTime - Downtime;
Ao = Uptime / (Uptime + Downtime); % Operational availability
```

---
## **Adding Extra MTBF Purchase Option**
This section modifies existing variables to add the possibility of buying additional MTBF to reduce failures. 

```matlab
% Add this variable before computation of number of failures
PercentIncreasedMTBF = 0;
% Modify these variables, do not add new lines at the bottom of the code
NumberOfFailures = 28 / (1 + PercentIncreasedMTBF / 100); % Adjust failure count
PriceFor1PercentMTBF = 125000;
InvestmentCost = AssetBasicPrice + PriceFor1PercentMTBF * PercentIncreasedMTBF;
```
## **Including Short Preventive Maintenance**
Adding short PM events, which further lowers the number of failures. Modify the existing variables.

```matlab
% Add this variable before computation of preventive maintenance time
NumberOfShortPM = 0;
% Modify these variables, do not add new lines at the bottom of the code
NumberOfPreventiveMaintenance = NumberOfLongPM + NumberOfShortPM;
MPMT = (2*7*24*NumberOfLongPM + 24*NumberOfShortPM) / NumberOfPreventiveMaintenance;
NumberOfFailures = 28 / (1 + PercentIncreasedMTBF / 100) * 0.95^NumberOfShortPM;
PreventiveMaintenanceCost = 1.4e6 + NumberOfShortPM * 140000;
```

---
## **Implementing Loops for Analysis**
This section sows how to add loops over different variables while keeping the code structure unchanged.

```matlab
%% Define Variable Range
VariableRange = 0:1:100; % Adjust the range as needed
NPV_vector = [];

for k = VariableRange
    
    %% Modify only one variable per loop run
    PercentIncreasedMTBF = k; % Example: looping over extra MTBF
    NumberOfShortPM = 6; % Fixed for MTBF loop
    
    %% Run the Entire Calculation
    % Copy the entire calculation block here, ensuring variables are correctly reassigned
    % All existing equations remain unchanged, except for the looped variable
    
    %% Store Results
    NPV_vector = [NPV_vector NPV];
end

%% Plot the results
figure;
plot(VariableRange, NPV_vector)
xlabel('Percent extra MTBF')
ylabel('Net Present Value (NPV)')
title('Effect of Extra MTBF on NPV')
grid on;

```

## **Note on Looping and Variable Re-Assignment**
When implementing loops, remember:
- Only one variable should be looped at a time. Keep all other variables fixed.
- Reassign values inside the loop to reflect changes correctly in calculations.
- If looping over a different variable (e.g., Interest Rate, FU Stops), replace the looped variable accordingly
- Re-assigning fixed variables inside the loop is not recommended coding practice, but it is used here to maintain a consistent code structure across different tasks.
---