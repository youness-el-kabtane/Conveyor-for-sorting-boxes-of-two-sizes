# Conveyor for sorting boxes of two sizes
## Project Overview
> This project features an automated industrial system designed to identify and sort two distinct sizes of products (**Product A** and **Product B**) using a combination of pneumatic actuators and PLC logic. The system is controlled by a **Siemens SIMATIC S7-300 PLC** (CPU 314C-2 PN/DP), a **SIMATIC Multi Panel MP 277 10'' Touch** and integrated with **FluidSIM** for pneumatic simulation.

## Main Objectives
-   **Automated Product Identification**: Utilize a triple-sensor array (**SA1**, **SA2**, **SA3**) to differentiate box sizes based on physical length.
-   **Sequential Pneumatic Control**: Implement a coordinated sequence for three double-acting cylinders (**A**, **B**, and **C**) to manage feeding and redirection.
-   **Real-Time Process Monitoring**: Provide a **SIMATIC Multi Panel** interface for operators to start, stop, and reset the system while viewing live status.
-   **Precise Production Counting**: Integrate PLC-based counters (**Counter1**, **Counter2**) to maintain an accurate tally of **Product A** and **Product B** units.
-   **Data Aggregation**: Calculate cumulative throughput by summing both product counts for a live **N° Total** HMI display.
-   **Integrated Simulation**: Bridge virtual **FluidSIM** simulation with physical PLC logic using **EzOPC** communication.

## System Components / Architecture
**1. Central Controller**

•  **Siemens SIMATIC S7-300 PLC:**  The core of the system is a  **CPU314C-2 PN/DP**, which handles all processing logic, input monitoring, and output commands.

![enter image description here](https://github.com/youness-el-kabtane/Conveyor-for-sorting-boxes-of-two-sizes/blob/343d2367c606f4e3d3cdd76d2490eaba8b28f730/image/Screenshot.png)

**2. Human-Machine Interface (HMI)**

The **SIMATIC Multi Panel** provides a centralized interface for real-time monitoring and system control:

-   **Production Statistics**: A displays live counts for **Product A**, **Product B**, and the cumulative **Total** throughput.
-   **Process Visualization**: A central graphical map tracks the conveyor and pneumatic cylinders (**A, B, C**), using green lamps to indicate active sensors like **SA1**, **SB1**, and **SC1**.
-   **System Controls**: Dedicated touch buttons allow operators to **Start** (green), **Stop** (red), or **Reset** (yellow) the automated sequence.
-   **Actuator & Motor Feedback**: On-screen indicators (**A+/A-**, **B+/B-**, **C+/C-**) confirm cylinder positions, while **M1**, **M2**, and **M3** lamps signal active conveyor motors.

**3.Pneumatic System**

-   **Actuators**: Three double-acting cylinders (**A**, **B**, and **C**) are used to drive the feeding and sorting mechanisms.
-    **Control Valves**: Each cylinder is operated by a **5/2-way directional control valve**.
-   **Solenoid Operation**: The valves are actuated by electrical solenoids (**A1/A2**, **B1/B2**, and **C1/C2**) that interface directly with the PLC outputs.
-   **Position Sensing**: Magnetic proximity sensors (**SA1–SA3**, **SB1–SB2**, and **SC1–SC2**) are mounted on the cylinders to provide feedback on the piston's exact position to the control logic

## Variables Tables
![enter image description here](https://github.com/youness-el-kabtane/Conveyor-for-sorting-boxes-of-two-sizes/blob/343d2367c606f4e3d3cdd76d2490eaba8b28f730/image/Screenshot2.png)

## Control Logic
- ![enter image description here](https://github.com/youness-el-kabtane/Conveyor-for-sorting-boxes-of-two-sizes/blob/343d2367c606f4e3d3cdd76d2490eaba8b28f730/image/Screenshot3.png)
-   **Network 1 (System Control)**: A memory latch that sets the **Start Bit (M0.2)** when the Start button is pressed and resets it when the Stop button is engaged.
-   **Network 2 (Product A Detection)**: Sets a memory bit (**M0.3**) when the first sensor identifies a smaller box; it resets once the sequence moves to the next stage.
-   **Network 3 (Product B Detection)**: Sets a memory bit (**M0.4**) when the sensors identify a larger box; it resets when the system prepares for the next item.
![enter image description here](https://github.com/youness-el-kabtane/Conveyor-for-sorting-boxes-of-two-sizes/blob/343d2367c606f4e3d3cdd76d2490eaba8b28f730/image/Screenshot4.png)
- **Network4**: Initiates the sorting cycle by pushing a box into the detection zone where the sensors can determine its size.
![enter image description here](https://github.com/youness-el-kabtane/Conveyor-for-sorting-boxes-of-two-sizes/blob/343d2367c606f4e3d3cdd76d2490eaba8b28f730/image/Screenshot5.png)
- **Network 5 (Cylinder A Retraction)**: Triggers the **"A2" Solenoid (Q0.1)** to retract the feeder cylinder once **SA2** (for small boxes) or **SA3** (for large boxes) confirms the product has reached the detection sensors.
![enter image description here](https://github.com/youness-el-kabtane/Conveyor-for-sorting-boxes-of-two-sizes/blob/343d2367c606f4e3d3cdd76d2490eaba8b28f730/image/Screenshot6.png)
- **Network 6 (Cylinder B Extension)**: Activates the **"B1" Solenoid (Q0.2)** to extend Cylinder B if a small box (**S1 Bit**) is detected and the cylinder is currently retracted (**SB1**).
![enter image description here](https://github.com/youness-el-kabtane/Conveyor-for-sorting-boxes-of-two-sizes/blob/343d2367c606f4e3d3cdd76d2490eaba8b28f730/image/Screenshot7.png)
-   **Network 7 (Cylinder B Retraction)**: Triggers the **"B2" Solenoid (Q0.3)** to retract Cylinder B once the sorting action is complete and the product has passed the **SB2** position sensor.
![enter image description here](https://github.com/youness-el-kabtane/Conveyor-for-sorting-boxes-of-two-sizes/blob/343d2367c606f4e3d3cdd76d2490eaba8b28f730/image/Screenshot8.png)
-   **Network 8 (Cylinder A Safety/Reset)**: A secondary logic path for Cylinder A's retraction solenoid (**A2**) that ensures it returns to its home position if the system is not in a start state or if sensors **SA1/SA3** indicate a completed cycle.
![enter image description here](https://github.com/youness-el-kabtane/Conveyor-for-sorting-boxes-of-two-sizes/blob/343d2367c606f4e3d3cdd76d2490eaba8b28f730/image/Screenshot9.png)    
-   **Network 9 (Cylinder C Extension)**: Activates the **"C1" Solenoid (Q0.4)** to extend Cylinder C if a large box (**S2 Bit**) is detected and the cylinder is retracted (**SC1**).
![enter image description here](https://github.com/youness-el-kabtane/Conveyor-for-sorting-boxes-of-two-sizes/blob/343d2367c606f4e3d3cdd76d2490eaba8b28f730/image/Screenshot10.png)    
-   **Network 10 (Cylinder C Retraction)**: Triggers the **"C2" Solenoid (Q0.5)** to retract Cylinder C once the large product has cleared the sorting station and is detected at the **SC2** position.
![enter image description here](https://github.com/youness-el-kabtane/Conveyor-for-sorting-boxes-of-two-sizes/blob/343d2367c606f4e3d3cdd76d2490eaba8b28f730/image/Screenshot11.png)
-   **Network 11 (Conveyor Motors)**: Activates the three conveyor belt motors (**KM1, KM2, KM3**) when the **Start Bit** is active, provided the system is not in a "Rest" or Stop state.
![enter image description here](https://github.com/youness-el-kabtane/Conveyor-for-sorting-boxes-of-two-sizes/blob/343d2367c606f4e3d3cdd76d2490eaba8b28f730/image/Screenshot12.png)    
-   **Network 12 (Data Reset)**: Clears all production data and internal memory words (MW10 to MW40) when the **Reset** button is pressed, returning all HMI counters to zero.
![enter image description here](https://github.com/youness-el-kabtane/Conveyor-for-sorting-boxes-of-two-sizes/blob/343d2367c606f4e3d3cdd76d2490eaba8b28f730/image/Screenshot13.png)    
-   **Network 13 (Product A Counter)**: Increments the tally for small boxes. It detects the rising edge of the sorting action (Cylinder B) and updates the **Product A count** on the HMI.    
-   **Network 14 (Product B Counter)**: Increments the tally for large boxes. It triggers when Cylinder C completes a sorting cycle, updating the **Product B count**.
-   **Network 15 (Total Calculation)**: Performs a mathematical addition of the Product A and Product B counts to generate the **Total Product** value displayed on the HMI.
---
**Author:**  Youness El Kabtane

**Website:**  [younesselkabtane](https://youness-el-kabtane.github.io/site/)

**Version:**  1.0.0

**Made with 💗**
