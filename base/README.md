## ICU Data Tables

These are the tables and their column definitions that all site-specific data will be transformed into. 

### patient

**Description:** Patient-level demographics and core identifying information. One record per patient.

**Composite Key:** `patient_id`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `patient_id` | character | Unique patient identifier (must be character string) | True |
| `birth_date` | date | Patient date of birth | True |
| `race_name` | character | Site-specific race designation (original/raw value) | True |
| `race_category` | character | Standardized race category per mCIDE. Fill missing values with "Unknown" | True |
| `sex_name` | character | Site-specific sex designation (original/raw value) | True |
| `sex_category` | character | Standardized sex category per mCIDE. Fill missing values with "Unknown" | True |
| `ethnicity_name` | character | Site-specific ethnicity designation (original/raw value) | True |
| `ethnicity_category` | character | Standardized ethnicity category per mCIDE. Fill missing values with "Unknown" | True |
| `language_name` | character | Site-specific language designation (original/raw value) | True |
| `language_category` | character | Standardized language category per mCIDE. Fill missing values with "Unknown or NA" | True |
| `death_dttm` | datetime | Date and time of death in UTC. If using external sources with date-only: same day as discharge use discharge_dttm, after discharge add midnight timestamp | True |

---

### adt

**Description:** Admissions, Discharges, and Transfers (ADT) tracking patient physical location throughout hospitalization. Represents where the patient is physically located, not patient status.

**Composite Key:** `hospitalization_id`, `in_dttm`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `hospital_id` | character | Unique ID for each hospital within a health system | True |
| `hospital_type` | character | Type of hospital from CLIF permissible set (academic, community, LTACH) | True |
| `in_dttm` | datetime | DateTime patient entered location (UTC) | True |
| `out_dttm` | datetime | DateTime patient left location (UTC) | True |
| `location_name` | character | Site-specific location name (original/raw value) | True |
| `location_category` | character | Standardized location category per mCIDE (e.g., ICU, Ward, ED, Procedural) | True |
| `location_type` | character | For ICU locations, specific ICU type per mCIDE. Null for non-ICU locations | True |

---

### hospitalization

**Description:** High-level episode summary for entire hospital stay. One record per hospitalization episode.

**Composite Key:** `hospitalization_id`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `patient_id` | character | Unique patient identifier | True |
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `hospitalization_joined_id` | character | Optional ID linking contiguous hospitalizations across different facilities within same health system | True |
| `admission_dttm` | datetime | DateTime of hospital admission (UTC) | True |
| `discharge_dttm` | datetime | DateTime of hospital discharge (UTC) | True |
| `age_at_admission` | integer | Patient age in years at time of admission | True |
| `admission_type_name` | character | Site-specific admission type (original/raw value) | True |
| `admission_type_category` | character | Standardized admission type per mCIDE (e.g., Emergency, Elective) | True |
| `discharge_name` | character | Site-specific discharge disposition (original/raw value) | True |
| `discharge_category` | character | Standardized discharge category per mCIDE (e.g., Home, Hospice, Acute Care Hospital) | True |
| `zipcode_nine_digit` | character | Nine-digit zipcode (preferred over five-digit) | True |
| `zipcode_five_digit` | character | Five-digit zipcode | True |
| `census_block_code` | character | Full Census Block ID (15 digits) | True |
| `census_block_group_code` | character | Tract + Block Group code (12 digits) | True |
| `census_tract` | character | State + County + Tract FIPS code (11 digits) | True |
| `state_code` | character | State FIPS code (2 digits) | True |
| `county_code` | character | State + County FIPS code (5 digits) | True |
| `fips_version` | character | Year of Census geography definitions (e.g., 2000, 2010, 2020) | True |
| `zipcode` | integer | Patient's residential zipcode for SDH research | False |
| `pre_hospital_admit_location` | character | Location/facility patient came from before hospital admission | False |
| `ed_arrival_source` | character | Source/mode of arrival to emergency department | False |
| `hospital_discharge_to` | character | Destination/facility patient discharged to from hospital | False |

---

### hospital_diagnosis

**Description:** Finalized billing diagnosis codes for hospital reimbursement (DRG calculation). These do not have timestamps as they are finalized after discharge.

**Composite Key:** `hospitalization_id`, `diagnosis_code`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `diagnosis_code` | character | ICD diagnosis code | True |
| `diagnosis_code_format` | character | Format of the code (ICD10CM or ICD9CM) | True |
| `diagnosis_primary` | integer | 1 = primary diagnosis (rank 1), 0 = secondary (rank ≥2) | True |
| `poa_present` | integer | Present on admission: 1 = Yes, 0 = No. Only definitive values allowed | True |

---

### patient_diagnosis

**Description:** All diagnosis codes for a patient beyond billing diagnoses. Includes problem list, medical history, and encounter diagnoses with timestamps.

**Composite Key:** `patient_id`, `hospitalization_id`, `diagnosis_code`, `start_dttm`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `patient_id` | character | Unique patient identifier | True |
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `diagnosis_code` | character | ICD-10-CM code from clinical documentation | True |
| `diagnosis_code_format` | character | Code format (typically ICD10CM for clinical data) | True |
| `source_type` | character | Source of diagnosis: problem_list, medical_history, encounter_dx | True |
| `start_dttm` | datetime | DateTime when condition started (user-defined, UTC) | True |
| `end_dttm` | datetime | DateTime when condition ended (NULL if ongoing, UTC) | True |

---

### patient_procedures

**Description:** Standardized procedural billing codes (CPT/HCPCS Level 1 and ICD-10-PCS) for procedures that were performed/completed. Does not include hospital billing codes (HCPCS Level 2).

**Composite Key:** `hospitalization_id`, `procedure_code`, `procedure_billed_dttm`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `billing_provider_id` | character | Unique identifier for the billing provider associated with the procedure | True |
| `performing_provider_id` | character | Unique identifier for the performing provider associated with the procedure | True |
| `procedure_code` | character | Encoded procedure identifier (CPT, ICD-10-PCS, or HCPCS code) | True |
| `procedure_code_format` | character | Code format used (CPT, ICD10PCS, or HCPCS) | True |
| `procedure_billed_dttm` | datetime | DateTime the procedure was billed (may differ from actual procedure time, UTC) | True |

---

### or_procedures

**Description:** Operating room procedures with timing information for OR entry, procedure start/completion, and OR exit.

**Composite Key:** `hospitalization_id`, `in_or_dttm`, `primary_procedure_nm`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | False |
| `in_or_dttm` | datetime | DateTime patient entered the operating room (UTC) | False |
| `procedure_start_dttm` | datetime | DateTime the procedure started (UTC) | False |
| `procedure_comp_dttm` | datetime | DateTime the procedure was completed (UTC) | False |
| `out_or_dttm` | datetime | DateTime patient left the operating room (UTC) | False |
| `primary_procedure_nm` | character | Name of the primary procedure performed | False |
| `service_nm` | character | Name of the surgical service (e.g., General Surgery, Cardiothoracic) | False |

---

### LDA

**Description:** Other icu/ed procedures like central lines, chest tubes, tracheostomy, etc. These are usually recorded in Lines,Drains,Airways but can be sourced from different places based on site (sometimes from clinical notes). So this table consists of all procedures that are not coded using ICD/CPT etc. 

**Composite Key:** `hospitalization_id`, `procedure_name`,  `procedure_dttm`

Needs to be implemented. 


### microbiology_culture

**Description:** Microbiology culture test results capturing order/result times, fluid type, method, and organisms identified.

**Composite Key:** `patient_id`, `hospitalization_id`, `organism_id`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `patient_id` | character | Unique patient identifier | True |
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `organism_id` | character | Unique identifier linking organism to susceptibility results | True |
| `order_dttm` | datetime | DateTime when the test was ordered (UTC) | True |
| `collect_dttm` | datetime | DateTime when specimen was collected (UTC) | True |
| `result_dttm` | datetime | DateTime when results became available (UTC) | True |
| `fluid_name` | character | Site-specific fluid name from raw data | True |
| `fluid_category` | character | Standardized fluid category per NIH CDE (e.g., Blood/Buffy Coat, CSF, Urine) | True |
| `method_name` | character | Site-specific method name from source data | True |
| `method_category` | character | Standardized method category (culture, gram stain, smear) | True |
| `organism_name` | character | Site-specific organism name from raw data | True |
| `organism_category` | character | Standardized organism under genus species structure | True |
| `organism_group` | character | Standardized organism group per NIH CDE structure | True |
| `lab_loinc_code` | character | LOINC code for the test | True |

---

### microbiology_susceptibility

**Description:** Antimicrobial susceptibility test results linked to organisms from microbiology_culture via organism_id.

**Composite Key:** `organism_id`, `antimicrobial_category`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `organism_id` | character | Unique identifier linking to microbiology_culture table | True |
| `antimicrobial_name` | character | Site-specific name of the antimicrobial tested | True |
| `antimicrobial_category` | character | Standardized antimicrobial category or class | True |
| `sensitivity_name` | character | Test result value (e.g., MIC value in mcg/mL) | True |
| `susceptibility_name` | character | Site-specific sensitivity interpretation | True |
| `susceptibility_category` | character | Standardized susceptibility (susceptible, non_susceptible, indeterminate, NA) | True |

---

### microbiology_nonculture

**Description:** Non-culture microbiology test results (e.g., PCR tests) with order/result times, fluid type, and organism detection.

**Composite Key:** `patient_id`, `hospitalization_id`, `order_dttm`, `organism_category`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `patient_id` | character | Unique patient identifier | True |
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `order_dttm` | datetime | DateTime when the test was ordered (UTC) | True |
| `collect_dttm` | datetime | DateTime when specimen was collected (UTC) | True |
| `result_dttm` | datetime | DateTime when results became available (UTC) | True |
| `fluid_name` | character | Site-specific fluid name from raw data | True |
| `fluid_category` | character | Standardized fluid category per NIH CDE | True |
| `method_name` | character | Site-specific method name from source data | True |
| `method_category` | character | Standardized method category (e.g., pcr) | True |
| `micro_order_name` | character | String name of microbiology non-culture test | True |
| `organism_category` | character | Standardized organism under genus species structure | True |
| `organism_group` | character | Standardized organism group per NIH CDE structure | True |
| `result_name` | character | Result name from raw data | True |
| `result_category` | character | Standardized result category (detected, not_detected, indeterminate) | True |
| `reference_low` | numeric | Reference low value | True |
| `reference_high` | numeric | Reference high value | True |
| `result_units` | character | Unit of the test result | True |
| `lab_loinc_code` | character | LOINC code for the test | True |

---

### patient_assessments

**Description:** Clinical assessment scores and measurements across different domains (neurological, sedation, pain, nursing risk, etc.).

**Composite Key:** `hospitalization_id`, `recorded_dttm`, `assessment_category`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `recorded_dttm` | datetime | DateTime when the assessment was recorded (UTC) | True |
| `assessment_name` | character | Site-specific assessment tool name from source data (e.g., GCS, NRS, SAT Screen) | True |
| `assessment_category` | character | Standardized assessment category per mCIDE | True |
| `assessment_group` | character | Broader assessment group (Sedation, Neurologic, Pain, Nursing Risk, etc.) | True |
| `numerical_value` | numeric | Numerical result or score from the assessment (e.g., 0-10, 3-15) | True |
| `categorical_value` | character | Categorical outcome from the assessment (e.g., Pass/Fail, Yes/No) | True |
| `text_value` | character | Textual explanation or notes from the assessment | True |

---

### vitals

**Description:** Vital signs measurements in long form (one vital sign per row). Units must match the vital category.

**Composite Key:** `hospitalization_id`, `recorded_dttm`, `vital_category`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `recorded_dttm` | datetime | DateTime when the vital was recorded (UTC) | True |
| `vital_name` | character | Site-specific vital sign name from source data (not used for analysis) | True |
| `vital_category` | character | Standardized vital category (temp_c, heart_rate, sbp, dbp, spo2, respiratory_rate, map, height_cm, weight_kg) | True |
| `vital_value` | numeric | Recorded value of the vital. Unit must match vital category | True |
| `meas_site_name` | character | Site where vital was recorded (optional) | True |

---

### invasive_hemodynamics

**Description:** Invasive hemodynamic measurements recorded via invasive monitoring. All pressures expressed in mmHg.

**Composite Key:** `hospitalization_id`, `recorded_dttm`, `measure_category`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `recorded_dttm` | datetime | DateTime when the measurement was recorded (UTC) | True |
| `measure_name` | character | Site-specific description of the invasive hemodynamic measurement (e.g., Right Atrium) | True |
| `measure_category` | character | Standardized measurement type (cvp, ra, rv, pa_systolic, pa_diastolic, pa_mean, pcwp, cardiac_output_thermodilution, cardiac_output_fick) | True |
| `measure_value` | numeric | Numerical value of the invasive hemodynamic measurement in mmHg | True |

---


### labs

**Description:** Laboratory results in long form (one lab result per row). Blood/plasma/serum specimens only. All values must use CLIF reference units.

**Composite Key:** `hospitalization_id`, `lab_result_dttm`, `lab_category`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `lab_order_dttm` | datetime | DateTime when the lab was ordered (UTC) | True |
| `lab_collect_dttm` | datetime | DateTime when specimen was collected (UTC) | True |
| `lab_result_dttm` | datetime | DateTime when lab results became available (UTC) | True |
| `lab_order_name` | character | Site-specific procedure name for the lab (e.g., "Complete blood count w/ diff") | True |
| `lab_order_category` | character | Standardized lab order name per mCIDE (e.g., "CBC") | True |
| `lab_name` | character | Site-specific lab component name from raw data (e.g., "AST (SGOT)") | True |
| `lab_category` | character | Standardized lab category per mCIDE minimum set for critical illness | True |
| `lab_value` | character | Recorded lab value (may contain non-numeric results like "> upper limit") | True |
| `lab_value_numeric` | numeric | Numeric part parsed from lab_value (optional) | True |
| `reference_unit` | character | Unit of measurement for the lab (must match CLIF reference units) | True |
| `lab_specimen_name` | character | Site-specific fluid/tissue name from source data | True |
| `lab_specimen_category` | character | Standardized specimen type (blood/plasma/serum, urine, csf, other) | True |
| `lab_loinc_code` | character | LOINC code for the lab | True |

---

### respiratory_support

**Description:** Wider longitudinal table capturing simultaneously recorded ventilator settings and observed parameters. Designed for most common respiratory support devices and modes used in ICU.

**Composite Key:** `hospitalization_id`, `recorded_dttm`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `recorded_dttm` | datetime | DateTime when device settings/measurements were recorded (UTC) | True |
| `device_name` | character | Site-specific raw string of the device | True |
| `device_id` | character | Unique ID of individual physical device (e.g., ventilator ACZ3RV91), enables linkage to waveform data | True |
| `device_category` | character | Standardized device category (IMV, NIPPV, CPAP, High Flow NC, Face Mask, Trach Collar, Nasal Cannula, Room Air, Other) | True |
| `vent_brand_name` | character | Ventilator model name when device_category is IMV or NIPPV | True |
| `mode_name` | character | Site-specific raw string of ventilation mode (e.g., CMV volume control) | True |
| `mode_category` | character | Standardized ventilation mode (Assist Control-Volume Control, Pressure Control, PRVC, SIMV, Pressure Support/CPAP, Volume Support, Blow by, Other) | True |
| `tracheostomy` | integer | Indicates if tracheostomy is present: 0 = No, 1 = Yes | True |
| `fio2_set` | numeric | Fraction of inspired oxygen set (e.g., 0.21) | True |
| `lpm_set` | numeric | Liters per minute of supplemental oxygen set for patients NOT on positive pressure ventilation | True |
| `tidal_volume_set` | numeric | Tidal volume set (in mL) | True |
| `resp_rate_set` | numeric | Respiratory rate set (in bpm) | True |
| `pressure_control_set` | numeric | Pressure control set (in cmH2O) | True |
| `pressure_support_set` | numeric | Pressure support set (in cmH2O) | True |
| `flow_rate_set` | numeric | Flow rate of air delivered to patients on non-invasive devices (in lpm) | True |
| `peak_inspiratory_pressure_set` | numeric | Peak inspiratory pressure set (in cmH2O). Equivalent to IPAP in non-invasive modes | True |
| `inspiratory_time_set` | numeric | Inspiratory time set (in seconds) | True |
| `peep_set` | numeric | Positive-end-expiratory pressure set (in cmH2O) | True |
| `tidal_volume_obs` | numeric | Observed tidal volume (in mL) | True |
| `resp_rate_obs` | numeric | Observed respiratory rate (in bpm) from respiratory device, not general vitals | True |
| `plateau_pressure_obs` | numeric | Observed plateau pressure (in cmH2O) | True |
| `peak_inspiratory_pressure_obs` | numeric | Observed peak inspiratory pressure (in cmH2O) | True |
| `peep_obs` | numeric | Observed PEEP (in cmH2O) | True |
| `minute_vent_obs` | numeric | Observed minute ventilation (in liters) | True |
| `mean_airway_pressure_obs` | numeric | Observed mean airway pressure (in cmH2O) | True |

---

### crrt_therapy

**Description:** Continuous Renal Replacement Therapy (CRRT) data including modalities, operational parameters, and fluid exchange details.

**Composite Key:** `hospitalization_id`, `recorded_dttm`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `device_id` | character | Unique ID of the individual dialysis machine used (e.g., machine ACZ3RV91) | True |
| `recorded_dttm` | datetime | DateTime when CRRT parameters were recorded (UTC) | True |
| `crrt_mode_name` | character | Site-specific name of CRRT mode (e.g., CVVHDF) | True |
| `crrt_mode_category` | character | Standardized CRRT mode (scuf, cvvh, cvvhd, cvvhdf, avvh) | True |
| `dialysis_machine_name` | character | Brand name for the dialysis machine | True |
| `blood_flow_rate` | numeric | Rate of blood flow through the CRRT circuit (mL/min) | True |
| `pre_filter_replacement_fluid_rate` | numeric | Rate of pre-filter replacement fluid infusion (mL/hr) | True |
| `post_filter_replacement_fluid_rate` | numeric | Rate of post-filter replacement fluid infusion (mL/hr) | True |
| `dialysate_flow_rate` | numeric | Flow rate of dialysate solution (mL/hr) | True |
| `ultrafiltration_out` | numeric | Net ultrafiltration output (mL/hr) | True |

---

### ecmo_mcs

**Description:** ECMO/MCS support capturing start/stop times, device type, and work rate. Wider longitudinal table.

**Composite Key:** `hospitalization_id`, `recorded_dttm`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `recorded_dttm` | datetime | DateTime when device settings/measurements were recorded (UTC) | True |
| `device_name` | character | Site-specific name of ECMO/MCS device including brand (e.g., Centrimag) | True |
| `device_category` | character | Standardized device category per mCIDE | True |
| `mcs_group` | character | Standardized MCS type group per mCIDE | True |
| `ecmo_configuration_category` | character | ECMO configuration type by cannulation strategy (vv, va, va_v, vv_a, etc.) | True |
| `control_parameter_name` | character | Site-specific measure of work rate (e.g., RPMs, Impella Power) | True |
| `control_parameter_category` | character | Standardized control parameter category | True |
| `control_parameter_value` | numeric | Value of the control parameter | True |
| `flow` | numeric | Blood flow (L/min) | True |
| `sweep_set` | numeric | Gas flow set (L/min). Applies to ECMO only | True |
| `fdO2_set` | numeric | Fraction of delivered oxygen set (0-1). Applies to ECMO only | True |

---

### transfusion

**Description:** Blood product transfusion events with detailed timing, component type, attributes, and volume information.

**Composite Key:** `hospitalization_id`, `transfusion_start_dttm`, `component_name`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `transfusion_start_dttm` | datetime | DateTime the transfusion of the blood component began (UTC) | True |
| `transfusion_end_dttm` | datetime | DateTime the transfusion of the blood component ended (UTC) | True |
| `component_name` | character | Name of the blood component transfused (e.g., Red Blood Cells, Plasma, Platelets) | True |
| `attribute_name` | character | Attributes describing modifications to the component (e.g., Leukocyte Reduced, Irradiated) | True |
| `volume_transfused` | numeric | Volume of the blood component transfused | True |
| `volume_units` | character | Unit of measurement for the transfused volume (e.g., mL) | True |
| `product_code` | character | ISBT 128 Product Description Code representing the specific blood product | True |

---

### medication_admin_continuous

**Description:** Medications administered at a rate over time with NO set dose. Examples include vasopressors, sedation, and paralysis drips. Multiple observations capture rate changes over time.

**Composite Key:** `hospitalization_id`, `med_order_id`, `admin_dttm`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `med_order_id` | character | Medication order ID (foreign key to link to other medication tables) | True |
| `admin_dttm` | datetime | DateTime when the medicine was administered (UTC) | True |
| `med_name` | character | Site-specific med name from raw data, often contains concentration (e.g., NOREPInephrine 8 mg/250 mL) | True |
| `med_category` | character | Standardized active ingredient for important ICU medications (e.g., norepinephrine) | True |
| `med_group` | character | Limited number of ICU medication groups (e.g., vasoactives, sedation) | True |
| `med_route_name` | character | Site-specific medicine delivery route | True |
| `med_route_category` | character | Standardized medication delivery route | True |
| `med_dose` | numeric | Quantity of active drug delivered per unit time | True |
| `med_dose_unit` | character | Unit of dose in format [active ingredient quantity]/[time] (e.g., mcg/min, mg/hr, units/hr, mcg/kg/min) | True |
| `mar_action_name` | character | Site-specific MAR (medication administration record) action (e.g., stopped) | True |
| `mar_action_category` | character | Standardized MAR action | True |
| `mar_action_group` | character | Whether medication was administered or not (administered, not_administered, other) | True |

---

### medication_admin_intermittent

**Description:** Medications administered as fixed doses at discrete time points. Examples include antibiotics, steroids, and boluses. Each row represents ONE observation for each medication administered.

**Composite Key:** `hospitalization_id`, `med_order_id`, `admin_dttm`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `med_order_id` | character | Medication order ID (foreign key to link to other medication tables) | True |
| `admin_dttm` | datetime | DateTime when the medicine was administered (UTC) | True |
| `med_name` | character | Site-specific med name from raw data, often contains concentration | True |
| `med_category` | character | Standardized active ingredient for important ICU medications | True |
| `med_group` | character | Limited number of ICU medication groups | True |
| `med_route_name` | character | Site-specific medicine delivery route | True |
| `med_route_category` | character | Standardized medication delivery route | True |
| `med_dose` | numeric | Quantity taken in dose | True |
| `med_dose_unit` | character | Unit of dose (e.g., mcg) | True |
| `mar_action_name` | character | Site-specific MAR (medication administration record) action | True |
| `mar_action_category` | character | Standardized MAR action | True |
| `mar_action_group` | character | Whether medication was administered or not (administered, not_administered, other) | True |

---

### intake_output

**Description:** Intake and output events capturing fluid administration and fluid removal with timestamps and amounts.

**Composite Key:** `hospitalization_id`, `intake_dttm`, `fluid_name`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | True |
| `intake_dttm` | datetime | DateTime of intake or output event (UTC) | True |
| `fluid_name` | character | Name of the fluid administered or output | True |
| `amount` | numeric | Amount of fluid (in mL) | True |
| `in_out_flag` | integer | Indicator for intake or output: 1 = intake, 0 = output | True |

---

### radiology_notes

**Description:** Radiology imaging studies with procedure details, timing, and clinical documentation including findings and impressions.

**Composite Key:** `hospitalization_id`, `accession_number`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `hospitalization_id` | character | Unique hospitalization identifier | False |
| `accession_number` | character | Unique identifier for the imaging study | False |
| `radiology_study_name` | character | Name of the radiology study (e.g., CT Chest with Contrast) | False |
| `procedure_code` | character | Procedure code for the imaging study | False |
| `procedure_code_source` | character | Source of procedure code (e.g., CPT, hospital-specific) | False |
| `radiology_study_descriptors` | character | Additional descriptors for the study (e.g., with/without contrast, laterality) | False |
| `order_dttm` | datetime | DateTime when the study was ordered (UTC) | False |
| `study_start_dttm` | datetime | DateTime when the imaging procedure started (UTC) | False |
| `study_end_dttm` | datetime | DateTime when the imaging procedure ended (UTC) | False |
| `note` | text | Full radiology report note | False |
| `findings` | text | Detailed findings from the radiology report | False |
| `impressions` | text | Radiologist's impressions/conclusions | False |
| `clinical_indication` | text | Clinical reason/indication for ordering the study | False |

---

### code_status

**Description:** Code status orders placed by clinicians. This is NOT equivalent to the code status display name in the EMR.

**Composite Key:** `patient_id`, `start_dttm`

| Column Name | Data Type | Description | is_in_CLIF |
|------------|-----------|-------------|------------|
| `patient_id` | character | Unique patient identifier | True |
| `start_dttm` | datetime | DateTime when the specific code status was initiated (UTC) | True |
| `code_status_name` | character | Site-specific name/description of the code status | True |
| `code_status_category` | character | Standardized code status per mCIDE (DNR, DNAR, UDNR, DNR/DNI, DNAR/DNI, AND, Full, Presume Full, Other) | True |

---

