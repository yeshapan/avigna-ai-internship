# **Week 01 - HVAC and Chiller Fundamentals**

## **HVAC Overview and System Boundaries**
* HVAC stands for Heating, Ventilation and Air Conditioning.
* The system boundaries encompass the central utility plant (chillers, cooling towers), air distribution systems (AHUs, ductwork) and the conditioned spaces themselves.
* **HVAC architecture in commercial buildings** typically consists of interconnected loops:
    * The airside loop delivering conditioned air to the building.
    * The chilled water loop circulating cold water.
    * The refrigerant loop inside the chiller.
    * The heat rejection loop rejecting heat to the atmosphere.

![Chiller Diagram](assets/chiller-diagram.png)

## **Chiller Working Principle**
* Industrial chillers operate on the principle of **Vapor Compression Refrigeration**.
* The goal is to continuously remove heat from an internal building loop (chilled water) and reject it to the outside environment.

The continuous thermodynamic cycle follows four main stages:
1. **Phase 1: Evaporation (Heat Absorption)**
    * Low-pressure, cold liquid refrigerant enters the evaporator
    * Warm return water from the building passes through the evaporator tubes
    * Refrigerant absorbs this heat $\implies$ water cools down and the refrigerant boils into a low-pressure gas

2. **Phase 2: Compression (Pressure + Temperature Boost)**
    * This low-pressure refrigerant vapor is drawn into the compressor
    * Compressor mechanically squeezes the gas
    * This drastically raises its pressure, density and temperature $\implies$ converts it into a superheated, high-pressure gas.

3. **Phase 3: Condensation (Heat Rejection)** 
    * This hot, high-pressure gas enters the condenser
    * Cooler water from a cooling tower loop (or ambient air) passes through the condenser
    * The refrigerant rejects its heat into this water loop $\implies$ refrigerant gas cools down and condenses back into a high-pressure liquid

4. **Phase 4: Expansion (Pressure Drop)**
    * This high-pressure liquid passes through an expansion valve
    * The valve restricts flow $\implies$ creates a sudden pressure drop
    * This flash-evaporates a small portion of the liquid $\implies$ instantly lowers the temperature and pressure of the remaining refrigerant $\implies$ prepares it to re-enter the evaporator

![Vapor Compression Refrigeration Cycle](assets/Vapor-Compression-Refrigeration-Cycle.png)

## **Major HVAC Components and Their Interactions**
A complete industrial chiller plant relies on several interconnected mechanical and electrical assets:
* **Compressor**: 
    * Mechanical heart of the system.
    * It drives the refrigerant flow.
    * Common industrial types include Centrifugal (for massive loads), Screw, Scroll compressors.
* **Evaporator**: 
    * Heat exchanger where heat transfers from the building's chilled water loop to the cold refrigerant.
* **Condenser**: 
    * Heat exchanger that transfers heat out of the hot refrigerant gas into an external cooling medium (water or air).
* **Auxillary Plant Loops (Pumps and cooling towers)**
    * **Cooling Tower**: 
        * Used in water-cooled systems.
        * It receives hot water from the condenser, sprays it into the air and uses evaporative cooling to reject the heat into the atmosphere.
    * **Chilled Water Pumps (CHWPs)**: 
        * Circulate the cooled water from the evaporator out to the building's air handling units (AHUs) and fan coil units (FCUs).
    * **Condenser Water Pumps (CWPs)**: 
        * Circulate water between the condenser and the outdoor cooling tower.
* **Thermal Expansion Valve (TXV/EEV)**: 
    * A precision metering device that regulates the exact amount of refrigerant entering the evaporator based on cooling load demands.

## **Air-Cooled vs Water-Cooled Chillers**
Classification on the basis of Condenser Cooling Method:
1. **Water-Cooled Chillers**: 
    * Highly efficient
    * Use a dedicated cooling tower loop to reject heat via evaporation into the atmosphere
    * The standard for large industrial facilities
2. **Air-Cooled Chillers**: 
    * Have a lower upfront installation cost and zero water usage
    * Generally operate at a lower efficiency (lower COP) because air is a poorer heat transfer medium than water

## **Main Sources of Energy Consumption and Performance Losses**
* **HVAC/chiller energy consumption and performance KPIs:**
    * **Coefficient of Performance (COP)**: Ratio of useful cooling output to electrical power input $\rightarrow$ Higher is better.
    * **kW/ton**: Electrical power ($kW$) required to produce one ton of refrigeration $\rightarrow$ Lower is better.
* **Temperature Lift (The "Hill" to Climb)**:
    * $$\text{Lift} = \text{Condensing Temp} - \text{Evaporating Temp}$$
    * Lower lift $\implies$ exponentially higher efficiency.
* **Part-Load Efficiency (NPLV / IPLV)**:
    * Chillers rarely operate at 100% of their design load.
    * Efficiency curves are inherently non-linear.
    * Many modern chillers (especially those equipped with Variable Speed Drives (VSD)) actually peak in efficiency at **40% to 70% part-load** rather than at 100%.
    * It is often mathematically cheaper to run two chillers at 45% load than one chiller at 90% load.
* **Operating constraints:**
    * **Surge (Centrifugal Chillers)**: It is an aerodynamic stall that reverses gas flow violently within the compressor. Occurs when the cooling load is too low but the required pressure "lift" is too high.
    * **Choke / Stonewall**: It is the maximum volumetric flow rate limit.

## **Review Questions**
* **What consumes the most energy in an HVAC/chiller plant?**
    * The compressor motor is the largest electrical load.
    * The real-time power draw of the compressor motor heavily dictates the entire plant's efficiency.
    * Secondary consumers include heavy inductive loads like the chilled water pumps, condenser water pumps and cooling tower fans.
* **Why are chillers critical for both comfort and energy performance?**
    * They provide the baseline cooling utility required to manage the building cooling load (dynamic heat generated by servers, people, or industrial processes).
    * Because they represent massive inductive electrical loads $\rightarrow$ optimizing their COP provides the single largest impact on facility-wide energy efficiency.

## **References**
1. [WaterChillers Blog - What is a chiller and how does it work?](https://waterchillers.com/blog/how-does-a-chiller-work/)
2. [Oxmaint - AI-powered predictive maintenance for chillers in manufacturing plants](https://oxmaint.com/industries/manufacturing-plant/ai-powered-predictive-maintenance-for-chillers-in-manufacturing-plants)
3. [Medium - Digital Twin Solutions for Data Centers Simulation System Design and Thermal Control](https://medium.com/@gwrx2005/digital-twin-solutions-for-data-centers-simulation-system-design-and-thermal-control-dffe2d1798ad)
4. [Vertiv - Cooling the future how high-capacity chillers are shaping tomorrow's data centers and industry](https://www.vertiv.com/en-emea/insights/articles/blog-posts/cooling-the-future-how-high-capacity-chillers-are-shaping-tomorrows-data-centers-and-industry/)