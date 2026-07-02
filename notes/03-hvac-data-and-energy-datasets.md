# **Week 03 - HVAC Data and Energy Datasets**

## **Time-Series Data Architecture in HVAC**

* **Timestamp Synchronization**
    * HVAC systems typically rely on decentralized PLCs and IoT sensors logging at disparate frequencies.
    * For example:
        * Power meters might be at 1-minute intervals
        * Temperature sensors at 15-minute intervals
        * etc
    * Aligning these into a single, dense dataframe requires strict upsampling/downsampling strategies.

* **Handling Sensor Dropouts**
    * There will inevitably be missing data in industrial environments. 
    * Preprocessing requires deciding between forward-filling (useful for static setpoints) or linear interpolation (better for continuous environmental variables) without artificially smoothing over actual mechanical faults.
    * This step is crucial for ensuring that the data fed into ML models is clean and reliable.

* **Timezone Normalization**
    * All time-series indices must be coerced to UTC before applying local weather data mappings. 
    * Failing to do this guarantees the models will misalign peak solar load with peak chiller demand.

## **Equipment and Chiller Plant Metadata**

* **Static Boundary Conditions**
    * Metadata defines the physical constraints for predictive models. 
    * This includes the chiller's nominal cooling capacity (Tons), design flow limits (GPM) and maximum power draw (kW).
* **Component Specifications**
    * Capturing whether a compressor is centrifugal, screw, or scroll is non-negotiable—it dictates the expected efficiency curves at part-load conditions.
* **Pump and Fan Configurations**
    * Identifying if cooling tower fans and condenser water pumps utilize Variable Frequency Drives (VFDs) or are constant-speed. This defines the available "action space" for any downstream optimization algorithms.

## **Weather Data as an External Forcing Function**

* **Dry-Bulb Temperature**:
    * It is the standard ambient air temperature.
    * It directly drives the sensible cooling load through the building envelope and ventilation intake.

* **Wet-Bulb Temperature (WBT)**:
    * This is the absolute most critical weather variable for water-cooled chiller plants.
    * It dictates the thermodynamic limit of the cooling tower $\rightarrow$ a tower can mathematically never cool the condenser water below the ambient WBT.

* **Solar Irradiance**:
    * Measured in W/m²
    * This tracks the radiant heat gain on the building's exterior.
    * It causes delayed spikes in the internal cooling load due to the building's thermal mass.

## **HVAC/Chiller Energy and Performance Signals**
* **Thermodynamic Differentials ($\Delta T$)**:
    * The spread between Chilled Water Return Temperature (CHWRT) and Chilled Water Supply Temperature (CHWST). 
    * A low $\Delta T$ indicates poor heat transfer efficiency in the air handling units (the classic "low $\Delta T$ syndrome").
* **Real-Time Load Calculation**:
    * The actual thermal work being executed by the plant is calculated continuously using fluid dynamics and thermodynamics:
    * $$Load = \text{Flow Rate} \times \text{Density} \times \text{Specific Heat} \times (\text{CHWRT} - \text{CHWST})$$
* **Electrical Signals**:
    * Active power (kW) drawn by the heavy inductive loads (compressor motors).
    * Tracking this power draw against the thermal load yields the real-time Coefficient of Performance (COP) $\leftarrow$ which is the ultimate target metric for optimization.

## **Dataset Catalog**

| Feature | ASHRAE Great Energy Predictor III | Building Data Genome Project 2 (BDG2) | UCI Energy Efficiency |
| :--- | :--- | :--- | :--- |
| **Dataset Scope & Rows** | **Massive Scale**: ~20 million rows. Spans 1,448 buildings across 16 geographical sites. | **Massive Scale**: ~30 million+ rows. Spans 3,053 buildings across 19 geographical sites. | **Micro Scale**: Exactly 768 rows. It is a synthetic dataset generated via Ecotect simulation, not field data. |
| **Feature Count & Types** | **Narrow Feature Set**: 4 core features. Focuses heavily on meter readings (electricity, chilled water, steam, hot water), basic weather vectors, and building metadata (square footage, primary use). | **Broad Feature Set**: Extends beyond ASHRAE by offering deeper, cleaned hourly meter readings specifically isolating chilled water consumption, alongside rich metadata. | **Static Features Only**: 8 input features (relative compactness, surface area, wall area, roof area, overall height, orientation, glazing area, glazing area distribution). |
| **Time Span** | **3 Years**: 2016 through 2018. Typically split into 1 year of training data and 2 years for testing/validation. | **2 Years**: Full years of 2016 and 2017, providing complete seasonal cycles. | **No Time Series**: A single static snapshot. Time span is entirely irrelevant here. |
| **Granularity** | **Hourly**: Aggregated meter readings logged strictly at 1-hour intervals. | **Hourly**: Standardized 1-hour intervals, heavily cleaned to remove anomalous dropouts. | **N/A**: This dataset represents static building designs, not operational chronologies. |
| **Weather Availability** | **Included**: Site-aligned weather files providing air temperature, cloud coverage, dew point, precipitation, sea level pressure, and wind direction/speed. | **Included**: Highly comprehensive local weather features perfectly synced to the timestamped meter readings. | **None**: No external environmental forcing functions are included. |
| **Forecasting Suitability**| **Excellent**: The scale, geographic diversity, and weather alignment make it a gold standard for training generalized load-forecasting models (like XGBoost or LSTMs). | **Excellent**: Similar to ASHRAE but often preferred due to stricter data cleaning protocols and better isolation of specific meter types. | **Non-Starter**: Impossible to use for time-series forecasting because it has no temporal dimension. |
| **Chiller KPI Availability** | **Poor**: It tracks the aggregated chilled water energy used by the building, but provides zero visibility into the chiller plant's internal thermodynamics (no COP, no condenser water temps). | **Moderate**: It successfully isolates the chilled water loops (unlike datasets that lump all HVAC power into one meter), making it a great proxy for total thermal load, but still lacks compressor-level signals. | **None**: Outputs are strictly heating and cooling loads for static architectural designs. |

## **Dataset Suitability + Risk Summary**
As we transition into Month 2 (Analytics and Forecasting), the choice of dataset will dictate the architectural limits of our machine learning pipeline.

* **The Forecasting Clear Winner**:
    * **BDG2** and **ASHRAE** are both robust
    * But **BDG2** is slightly more suited for our goals because of its cleaner isolation of chilled water data. 
    * These datasets will allow us to build highly accurate models that predict building-level cooling demand based on a 48-hour weather forecast. 
    
* **The Operational Gap (Data Limitations)**: 
    * The major risk with these open-source datasets is that they are macro-level (building energy focus) rather than micro-level (equipment physics focus). 
    * They tell us *how much* cooling the building demanded, but they do not capture the internal state of the chiller (e.g., refrigerant pressures, VFD frequencies, compressor vibration). 
    * We cannot calculate real-time COP or train fault-detection algorithms on this data alone.

* **Mitigation Strategy**:
    * To train models specifically for chiller performance optimization, we cannot rely solely on BDG2. 
    * We will need to use the physical simulators researched in week-2 (such as EnergyPlus or Modelica) to generate synthetic, high-fidelity internal sensor data (temperatures, pressures, flow rates) and fuse it with the macro-level BDG2 load profiles.

## **Review Questions**

* **Which dataset is most suitable for forecasting and why?**
    * The **Building Data Genome Project 2 (BDG2)** is the most robust choice for time-series forecasting. 
    * **Why it works**:
        * **Historical Context**: 
            * Forecasting models (like LSTMs or Transformers) require deep historical context to learn seasonal patterns.
            * BDG2 provides two full years of uninterrupted, hourly data across diverse climates. 
        * **Weather Integration**: 
            * More importantly, the time-series arrays are perfectly aligned with local weather data.
            * This allows an algorithm to definitively map how a 5-degree spike in ambient temperature impacts the chilled water demand exactly two hours later (accounting for the building's thermal mass). 
            * The UCI dataset is entirely useless here, and BDG2 generally offers cleaner chilled-water isolation than the ASHRAE set.

* **Which dataset best supports chiller performance analysis and why?**
    * **BDG2** is the best of the available options, though it comes with strict limitations.
    * **Why it works**:
        * Many generic datasets lump all facility electricity into one massive "power" column.
        * But, BDG2 explicitly isolates the "chilled water" meter readings.
        * This allows us to establish a reliable baseline of the actual thermal work (cooling load) the plant is doing independent of the building's lighting or server loads.
    * **Limitations**:
        * True chiller performance analysis requires calculating the Coefficient of Performance (COP) $\rightarrow$ which demands internal sensor data (Chilled Water Supply Temp, Return Temp, mass flow rates, compressor kW).
        * BDG2 does not have these micro-level signals.
        * So, while BDG2 is excellent for establishing the *cooling load baseline*, it must be augmented with simulated operational data to actually optimize chiller setpoints.