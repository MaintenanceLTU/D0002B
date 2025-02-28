# Maintenance Cost and Availability Analysis in Python

## **Calculation of Availability**
This section calculates key maintenance parameters, including downtime, uptime, availability, and costs.

```python
# Define variables
# Interest Rate and Calculation Period
InterestRate = <ADD_VALUE_HERE>  # Discount rate
LifeLength = <ADD_VALUE_HERE>  # Analysis period in years

# Total time
TotalTime = 365 * 24  # Total hours per year

# Preventive Maintenance
NumberOfLongPM = <ADD_VALUE_HERE>  # One major preventive maintenance stop
NumberOfPreventiveMaintenance = NumberOfLongPM
MPMT = <ADD_VALUE_HERE>  # Mean preventive maintenance time in hours

# Corrective Maintenance
NumberOfFailuresBasic = <ADD_VALUE_HERE>  # Input Number of failures per year
NumberOfFailures = NumberOfFailuresBasic  # Calculated Number of failures per year
MTTR = <ADD_VALUE_HERE>  # Mean time to repair (hours)
MWT_CM = <ADD_VALUE_HERE>  # Mean waiting time for corrective maintenance (hours)

# Calculate Downtime and Availability
PreventiveMaintenanceTime = NumberOfPreventiveMaintenance * MPMT
RepairTime = MTTR * NumberOfFailures
CorrectiveMaintenanceTime = RepairTime + MWT_CM * NumberOfFailures
Downtime = PreventiveMaintenanceTime + CorrectiveMaintenanceTime
Uptime = TotalTime - Downtime
Ao = Uptime / (Uptime + Downtime)  # Operational availability
```

## **Calculation of  Life Cycle Costs**
This section calculates LCC parameters.

```python
# Maintenance Cost Calculation
PreventiveMaintenanceCost = <ADD_VALUE_HERE>  # Cost of preventive maintenance
HourlyRepairCost = <ADD_VALUE_HERE>  # Hourly repair cost (corrective maintenance)
CorrectiveMaintenanceCost = HourlyRepairCost * CorrectiveMaintenanceTime
# Annual maintenance cost
YMC = PreventiveMaintenanceCost + CorrectiveMaintenanceCost

# Annual Income Calculation
Capacity = <ADD_VALUE_HERE>  # Annual capacity (tons)
Price = <ADD_VALUE_HERE>  # Price per ton (currency)
Performance = <ADD_VALUE_HERE>  # Plant efficiency
Quality = <ADD_VALUE_HERE>  # Quality factor

OEE = Ao * Performance * Quality
# Annual income
YI = Capacity * OEE * Price

# Operating Costs Calculation
PersonellCost = <ADD_VALUE_HERE>  # Operator cost per hour (during operation)
EnergyCost = <ADD_VALUE_HERE>  # Energy cost per hour during operation at 100% performance

# Annual operating cost
YOC = Uptime * PersonellCost + Uptime * Performance * EnergyCost

# Investment Cost Calculation
AssetBasicPrice = <ADD_VALUE_HERE>  # Purchase cost
InvestmentCost = AssetBasicPrice  # Total investment cost
```

## **Calculation of Net Present Value (NPV)**
This section calculates the present value for the LCC parameters.

```python
# Net present value calculations
# Present value calculations for yearly costs
PVFA = (1 - (1 + InterestRate) ** (-LifeLength)) / InterestRate  # Present Value Factor of Annuity
PV_MC = PVFA * YMC
PV_OC = PVFA * YOC
PV_I = PVFA * YI
# Present value calculation for residual value
PVF = 1 / (1 + InterestRate) ** LifeLength  # Present Value Factor
PV_RV = PVF * RV

# Net present value
NPV = -InvestmentCost + PV_I - (PV_MC + PV_OC) + PV_RV

```
---
## **Adding Extra MTBF Purchase Option**
This section modifies existing variables to add the possibility of buying additional MTBF to reduce failures. 

```python
# Add this variable before computation of number of failures
PercentIncreasedMTBF = <ADD_VALUE_HERE>
# Modify this variable, do not add new lines at the bottom of the code
NumberOfFailures = NumberOfFailuresBasic / (1 + PercentIncreasedMTBF / 100)
# Add this variable before computation of investment cost
PriceFor1PercentMTBF = <ADD_VALUE_HERE>
# Modify this variable, do not add new lines at the bottom of the code
InvestmentCost = AssetBasicPrice + PriceFor1PercentMTBF * PercentIncreasedMTBF
```

## **Including Short Preventive Maintenance**
Adding short PM events, which further lowers the number of failures. Modify the existing variables.

```python
# Add this variable before computation of preventive maintenance time
NumberOfShortPM = <ADD_VALUE_HERE>
# Modify these variables, do not add new lines at the bottom of the code
NumberOfPreventiveMaintenance = NumberOfLongPM + NumberOfShortPM
MPMT = (2 * 7 * 24 * NumberOfLongPM + 24 * NumberOfShortPM) / NumberOfPreventiveMaintenance
NumberOfFailures = NumberOfFailuresBasic / (1 + PercentIncreasedMTBF / 100) * 0.95 ** NumberOfShortPM
PreventiveMaintenanceCost = 1.4e6 + NumberOfShortPM * 140000
```

---
## **Implementing Loops for Analysis**
This section sows how to add loops over different variables while keeping the code structure unchanged.

```python
# Define Variable Range
VariableRange = range(0, 100+1)  # [0, 100] Adjust the range as needed
NPV_vector = []

for k in VariableRange:
    # Modify only one variable per loop run
    # Example: looping over extra MTBF
    PercentIncreasedMTBF = k
    
    # Run the Entire Calculation    
    # All existing equations remain unchanged, except for the looped variable
    
    # Store Results
    NPV_vector.append(NPV)

# Plot the results
import matplotlib.pyplot as plt

plt.figure()
plt.plot(VariableRange, NPV_vector)
plt.xlabel('Percent extra MTBF')
plt.ylabel('Net Present Value (NPV)')
plt.title('Effect of Extra MTBF on NPV')
plt.grid(True)
plt.show()

```

## **Note on Looping and Variable Re-Assignment**
When implementing loops, remember:
- Only one variable should be looped at a time. Keep all other variables fixed.
- Reassign values inside the loop to reflect changes correctly in calculations.
- If looping over a different variable (e.g., Interest Rate, FU Stops), replace the looped variable accordingly
- Re-assigning fixed variables inside the loop is not recommended coding practice, but it is used here to maintain a consistent code structure across different tasks.
---