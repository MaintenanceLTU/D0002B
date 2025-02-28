# Maintenance Cost and Availability Analysis in MATLAB

## **Task A: Basic Calculation of Maintenance Costs and Availability**
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
