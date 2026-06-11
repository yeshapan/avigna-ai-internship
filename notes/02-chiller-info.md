# **Chiller Systems Research**

### **What are the different types of chillers?**

Classification on the basis of Compressor Technology:
1. **Centrifugal Compressors**: 
    * Best for massive loads (eg: district cooling, large data centers)
    * They offer high efficiency at full load; but are prone to a severe mechanical fault called "surge" at low loads.
    
2. **Screw (Rotary) Compressors**: 
    * Ideal for medium to large loads
    * They handle part-load conditions much better than centrifugal units and provide stable continuous operation.
        
3. **Scroll Compressors**: 
    * Used for smaller commercial applications
    * They are often staged in modular multiples to step up or down capacity as needed

Classification on the basis of Condenser Cooling Method:
1. **Water-Cooled Chillers**: 
    * Highly efficient
    * Use a dedicated cooling tower loop to reject heat via evaporation into the atmosphere
    * The standard for large industrial facilities
2. **Air-Cooled Chillers**: 
    * Have a lower upfront installation cost and zero water usage
    * Generally operate at a lower efficiency (lower COP) because air is a poorer heat transfer medium than water

### **What are the key metrics for chillers and what does each metric signify?**

**Key Metrics** for chiller performance evaluation are:
* **Coefficient of Performance (COP)**: Ratio of useful cooling output to electrical power input $\rightarrow$ Higher is better 
* **kW/ton**: Electrical power ($kW$) required to produce one ton of refrigeration $\rightarrow$ Lower is better 

* **Part-Load Efficiency (NPLV / IPLV)**:
    * Chillers rarely operate at 100% of their design load.
    * Efficiency curves are inherently non-linear
    * Many modern chillers (especially those equipped with Variable Speed Drives (VSD)) actually peak in efficiency at **40% to 70% part-load** rather than at 100%
    * Understanding this non-linear curve is the foundation of "chiller staging"
    * It is often mathematically cheaper to run two chillers at 45% load than one chiller at 90% load.

* **Temperature Lift (The "Hill" to Climb)**:

    $$\text{Lift} = \text{Condensing Temp} - \text{Evaporating Temp}$$
    * Lower lift $\implies$ exponentially higher efficiency
    * So, slightly raising the Chilled Water Supply Temp (CHWST) or lowering the Condenser Water Supply Temp (CWST) drastically reduces compressor work and increases COP.

### **What are operating constraints and how are they important?**

When training Machine Learning or control agents $\rightarrow$ simulator must penalize operations that violate these mechanical boundaries:

* **Surge (Centrifugal Chillers)**: 
    * It is an aerodynamic stall that reverses gas flow violently within the compressor. 
    * It occurs when the cooling load is too low (not enough refrigerant flow) but the required pressure "lift" is too high
    * If left unchecked $\implies$ surge destroys compressor bearings.
* **Choke / Stonewall**: 
    * It is the maximum volumetric flow rate limit
    * Past this boundary $\rightarrow$ the gas reaches sonic velocity in the compressor throat $\implies$ capacity cannot be increased further
* **Minimum / Maximum Flow Rates**: 
    * Water velocity in the evaporator and condenser tubes must stay:
        * Above a minimum threshold $\leftarrow$  prevent freezing or rapid scale fouling
        * Below a maximum limit $\leftarrow$  prevent physical erosion of the copper tubes.
* **Condenser Water Minimum Temperature**: 
    * If the incoming cooling tower water is too cold $\rightarrow$ system pressure drops too much
    * Then the expansion valve will lack the pressure differential needed to push liquid refrigerant into the evaporator $\implies$ starves the system.

### **What are the controllable and uncontrollable system variables?**

To train effective AI models (like Reinforcement Learning agents) or optimization algorithms $\rightarrow$ we must strictly separate the actionable control levers from the external environment.

#### **Uncontrollable Variables (Environment / State)**
These are external boundary conditions that the system cannot change but must react to.

* **Ambient Weather Conditions**:
    * **Dry-Bulb Temperature**:
        * It is the standard "air temperature"
        * Doesn't account for moisture (humidity) present in the atmosphere
        * It is measured by a thermometer freely exposed to air but shielded from moisture and direct sunlight
        * It is a measure of the sensible heat (aka pure heat energy)
    * **Wet-Bulb Temperature**: 
        * Critical for cooling tower efficiency
        * Cooling towers reject heat via evaporation
        * Wet-bulb temperature dictates the **absolute thermodynamic limit** of the cooling tower
        * If the wet-bulb is high (e.g., a humid monsoon day) $\rightarrow$ water can't evaporate easily
        * If cooling tower struggles (so basically condenser water stays hot) $\implies$ chiller's required pressure "lift" skyrockets $\implies$ efficiency (COP) drops.
* **Building Cooling Load ($Q_{\text{evap}}$)**: 
    * It referes to the total dynamic heat generated (by servers / people / industrial processes) that must be actively removed.
    * This load fluctuates continuously based on the time of day / occupancy / compute workloads (for data centers).
* **Utility Grid Prices**: 
    * These refer to dynamic time-of-use electricity tariffs.
    * This is the cost function parameter for AI agents optimizing "load shifting" 
    * Example: Pre-cool a facility when electricity is cheap & avoid running chillers at max capacity when tariffs are high.

#### **Controllable Variables (Action Space / Setpoints)**
These are the knobs and dials the control system or AI agent can adjust to minimize energy usage while meeting the load.
* **Chilled Water Supply Temperature (CHWST) Setpoint**: 
    * Slightly raising this (eg: from 6°C to 8°C) saves massive compressor energy, but reduces the dehumidification and cooling capacity inside the building.
* **Condenser Water Supply Temperature (CWST) Setpoint**: 
    * Managed by speeding up or slowing down cooling tower fans
    * Lowering this helps the chiller run more efficiently, but costs more in fan energy
    * ML objective is finding the crossover sweet spot
* **Pump VFD Speeds**: 
    * Adjusting the Variable Frequency Drives of the Chilled Water Pumps (CHWP) and Condenser Water Pumps (CWP) to match actual flow demand instead of running at 100%.
* **Chiller Sequencing / Staging**: 
    * The boolean decision of turning individual chiller units `ON` or `OFF` to keep the active fleet operating at their peak part-load efficiency curves.
