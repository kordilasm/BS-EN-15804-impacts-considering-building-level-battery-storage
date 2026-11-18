# BS-EN-15804-impacts-considering-building-level-battery-storage
Open source Python tool for calculating the environmental impacts of building level PV battery storage across the whole impact spectrum of BS EN 15804:2012+A2:2019, under grid decarbonisation pathways and climate change. The battery embodied and operational impacts are calculated over a building's lifespan, assumed here 60 year as per RICS WLC, for three battery chemistries (LFP, NMC811, NCA) with replacement estimated via the capacity fade model of BLAST-Lite from NREL.

Details on the analysis are provided in the associated publication:

Kordilas M, Mumovic D, Schwartz Y, et al (2026a) Net benefits and burdens of PV battery storage systems in buildings: Lifecycle carbon and impacts beyond carbon under high temporal grid decarbonisation dynamics [under review]. Energy and Buildings

The tools integrate the "Future_impacts_calculator" from BS-EN-15804-impacts-considering-grid-decarbonisation-trajectories for the embodied impacts of the battery and the inverter in future years, which is described in https://github.com/kordilasm/BS-EN-15804-impacts-considering-grid-decarbonisation-trajectories and peer reviewed in:

Marios Kordilas, Dejan Mumovic, Yair Schwartz, Rob Cooke, Smith Mordak, Zaid Chalabi, Temporal myopia in building Life Cycle Assessment? Granular versus coarse dynamics in climate change and grid decarbonisation, Building and Environment, Volume 295, 2026, 114419, ISSN 0360-1323, https://doi.org/10.1016/j.buildenv.2026.114419.

Files needed
1. Future_impacts_calculator_Batteries folder

Download the complete "Future_impacts_calculator_Batteries" folder and store it locally in /Users/yourname/Downloads/Future_impacts_calculator_Batteries, and set BATTERIES_FOLDER in msc_battery_strategy.py and batteries_folder in main() of milp_battery_strategy.py to its path. The folder contains the files for the Battery and inverter.

Files, as in the Future_impacts_calculator:

Impacts per MJ of technology inventory.xlsx
National Grid ESO scenarios.xlsx
EU future impacts_for python.xlsx
IEA_Generation_Shares.xlsx
IEA_WEO_Generation_Shares_Regions.xlsx
IEA GLO future impacts_for python.xlsx
AR6_Electricity_Shares for ISO countries.csv
Ecoinvent geographies.xlsx (sheet "Geographies")
ISO 3166 geographies.xlsx

Battery and inverter files. For each module there is one pair of files, calculated for the process under consideration with SimaPro, using the default units:

- A4 (road transport): SimaPro impacts_A4.xlsx & Impacts to subtract_A4.xlsm; per tkm
- B4 (battery):	SimaPro impacts_B4.xlsx	& Impacts to subtract_B4.xlsm; per kg of battery
- C (battery):	SimaPro impacts_C.xlsx	& Impacts to subtract_C.xlsm;	per kg of battery
- B4 (inverter):	SimaPro impacts_B4_inverter.xlsx	& Impacts to subtract_B4_inverter.xlsm; per inverter unit of 2.5 kW
- C (inverter):	SimaPro impacts_C_inverter.xlsx	& Impacts to subtract_C_inverter.xlsm; per inverter unit of 2.5 kW

- SimaPro impacts_{module}.xlsx, such as SimaPro impacts_B4.xlsx needs to be filled in "Sheet1" with the columns "Impact category", "Unit" and the result of the process for every BS EN 15804:2012+A2:2019 impact category.
- Impacts to subtract_{module}.xlsm: such as Impacts to subtract_B4.xlsx needs the electricity of the process that is replaced by the future electricity mix. Paste the process contribution inventory exported from SimaPro into cell A1 of the sheet "Paste results here" and save the file.
- Using SimaPro, calculate all Ecoinvent High Voltage electricity market country mixes in MJ, i.e. every dataset of the form "Electricity, high voltage {CN}| market group for electricity, high voltage | Cut-off, U". Extract the results for all BS EN 15804:2012+A2:2019 impact categories and paste them into the "Countries electricities" sheet of each "Impacts to subtract" file.
- Using SimaPro, calculate all Ecoinvent electricity by fuel in MJ, i.e. every dataset of the form "Electricity, high voltage {CN-GS}| electricity production, natural gas, conventional power plant | Cut-off, U", and paste the results into the relevant sheets of "Impacts per MJ of technology inventory.xlsx".

2. Hourly files folder

Set base_path to the folder with the hourly input files, such as /Users/yourname/Downloads/hourly files/.

Files
- BASELINE2 hourly_impacts.xlsx
- FS2 hourly_impacts.xlsx 
- ST2 hourly_impacts.xlsx 
- CT2 hourly_impacts.xlsx 
- LW2 hourly_impacts.xlsx	
- Hourly environmental impacts per kWh of UK grid electricity for each electricity scenario. "Sheet1", columns B to Z, one column per impact category in the order of the list labels in the scripts, 8,760 rows for each year from 2025 to 2050
- Your building hourly needs.xlsx containing hourly building electricity needs and PV generation of the case study, set with FEEDS_FILE_NAME (msc_battery_strategy.py) or in the path under base_path (milp_battery_strategy.py). Sheets "current", "2020s medium", "2050s medium", "2080s medium", "2020s high", "2050s high" and "2080s high" (current weather and the CIBSE future weather files for RCP 4.5 and RCP 8.5). Columns B to F are the hourly loads in kWh and column H is the PV generation, entered as negative values. 

3. Inventory data to be filled in

A1-A3, B4 and C values for the BASELINE scenario and the A4 transport values are inventory data from Ecoinvent; there are currently "add value" placeholders in the scripts and must be filled in before running. 

All lists follow the order of the 25 impact categories of BS EN 15804, which makes it necessary to follow this order.

The inverter has the power of battery's capacity divided by 2 hours, is replaced every 12 years, and its values scale with the power divided by 2.5 kW (A4 scales with its mass of 7.4 kg/kW).

Workflow:
1. Download the repository and the "Future_impacts_calculator_Batteries" folder, and prepare the hourly files.
2. Fill in the inventory data and set the paths in the script (base_path, OUTPUT_FOLDER, BATTERIES_FOLDER).
3. Run the script. You should select:
- the battery strategy and capacity (MSC or MILP); MILP takes approximately 85 hours for all optimisations and capacities, worth keeping that in mind
- the battery chemistry (1. LFP, 2. NMC811, 3. NCA)
- the BLAST-Lite capacity fade model of the selected chemistry
- the electricity scenarios and weather cases
- the sensitivity runs: S0 (default settings), S1 (BLAST degradation scalar), S2 (end of life capacity) and S3 (roundtrip efficiency)

BLAST-Lite capacity fade models:
1. For LFP: Sony-Murata 3 Ah LFP-Gr or 250 Ah prismatic LFP-Gr
2. For NMC811: LG M50 5 Ah NMC811-GrSi or LG MJ1 4 Ah NMC811-GrSi
3. For NCA: Panasonic 3 Ah NCA-Gr or Sony-Murata 2.5 Ah NCA-GrSi

The battery is replaced when its relative capacity falls below the end of life capacity, assumed here 80% for S0 and S1, 70% for S2.

Results are written to OUTPUT_FOLDER as Excel files named with the chemistry and sensitivity run (for MSC as impact_results_current_weather.xlsx, impact_results_future.xlsx, impact_results_fade_details.xlsx; for MILP as batch_optimisation_.xlsx).

Licenses:

IEA World Energy Outlook Free Dataset is used under the Creative Commons Attribution-NonCommercial-ShareAlike 4.0 license (CC BY-NC-SA 4.0)

ISO 3166 country codes can be accessed here https://www.iso.org/obp/ui/#search

Ecoinvent geographies file can be accessed here https://support.ecoinvent.org/geographies

The capacity fade models are from BLAST-Lite (https://github.com/NatLabRockies/BLAST-Lite), which is used under the following license:

Copyright (c) 2023, Alliance for Energy Innovation, LLC
All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this
   list of conditions and the following disclaimer.

2. Redistributions in binary form must reproduce the above copyright notice,
   this list of conditions and the following disclaimer in the documentation
   and/or other materials provided with the distribution.

3. Neither the name of the copyright holder nor the names of its
   contributors may be used to endorse or promote products derived from
   this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
