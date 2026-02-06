# OLIDS Episode of Care
This will provide an overview of key things to be aware of in relation to the OLIDS Episode of Care table


# OLIDS Episode of Care: NULL Episode Type - Known Data Quality Issue

**Last Updated:** 6 February 2026
**Applies To:** OLIDS_COMMON.EPISODE_OF_CARE table
**Status:** Known Limitation - User Acceptance Required

***

## Executive Summary

The OLIDS Episode of Care table contains NULL values in the `EPISODE_TYPE_SOURCE_CONCEPT_ID` field, affecting approximately 11% of records based on point-in-time analysis. This limitation results from structural constraints in EMIS source systems and primarily affects historical registration data from before the initial OLIDS bulk load (mid-2025).[^1]

**Users must accept this as a current limitation** whilst the OLIDS team works on aligning existing and available data transforms. This issue has minimal impact on direct care and active patient cohort analysis, but affects historical retrospective studies.[^1]

***

## Key Takeaways

### What You Need to Know

1. **~11% of Episode of Care records have NULL episode type** (varies by dataset)[^1]
2. **Primarily affects historical data** from before mid-2025 first bulk[^1]
3. **Minimal impact on direct care and active cohort analysis** (>99% complete for current registrations)[^1]
4. **Users must accept this limitation** whilst solutions are explored[^1]
5. **No committed timeline** for resolution[^1]

### What You Should Do

1. **Focus on active registrations** for optimal data quality[^1]
2. **Filter to post-bulk data** for historical analysis where possible
3. **Document the limitation** in analytical outputs
4. **Use DDS for pre-2025 historical studies** if access remains available[^1]
5. **Escalate if critical business impact** to support prioritisation

### What's Being Done

1. **OL-DAS investigating EMIS/OPTUM historic file request**[^1]
2. **Team aligning existing available data transforms**[^1]
3. **Active investigation of unexpectedly high NULL rates** in current registrations[^1]
4. **Alternative solutions being assessed** (subject to prioritisation)[^1]

***

## Background

### What is the Episode of Care Table?

The `OLIDS_COMMON.EPISODE_OF_CARE` table contains records of patient registration episodes with GP practices. Each record represents a period during which a patient was registered at a specific practice, including:[^1]

- Registration start and end dates
- Practice/organisation details
- Care manager (GP) information
- **Episode type** (registration type): Regular, Temporary, Emergency, Immediately Necessary, or Private[^2]


### What is Episode Type?

Episode type (stored in `EPISODE_TYPE_SOURCE_CONCEPT_ID`) identifies the nature of a patient's registration with a GP practice:[^2]


| Episode Type | Description |
| :-- | :-- |
| **Regular** | Standard ongoing care within Local Health Authority boundary |
| **Temporary** | Short-term registration (15-day or 90-day expiry) |
| **Emergency** | Urgent treatment <24 hours for patients outside HA |
| **Immediately Necessary** | Urgent unplanned care for local HA patients |
| **Private** | Fee-paying patients |

### Why Does This Matter?

Episode type is important for:

- Understanding patient registration patterns and mobility
- Distinguishing between permanent and temporary populations
- Service planning and capacity management
- Funding calculations based on registered populations
- Historical cohort analysis and longitudinal studies

***

## The Issue

### What is Happening?

Approximately 11% of records in the `EPISODE_OF_CARE` table have NULL values for `EPISODE_TYPE_SOURCE_CONCEPT_ID`. This means the registration type is unknown for these episodes.[^1]

**Key Statistics** (as of February 2026):


| Dataset/Region | Metric | NULL Rate/Count |
| :-- | :-- | :-- |
| **SEL ICB** (indicative) | Overall records | ~11% NULL rate [^1] |
| **SEL ICB** | Active registrations (no end date) | 0.6% NULL (~40,000 records) [^1] |
| **TPP practices** | Multiple regions (NWL/NEL) | Complete NULLs present [^1] |
| **All datasets** | Historical episodes (pre-bulk) | Higher NULL proportion [^1] |

**Note**: The 11% figure is based on SEL ICB point-in-time analysis and will vary by organisation depending on patient movement patterns, bulk load timing, and system type (EMIS vs TPP).[^1]

### Which Records are Affected?

The NULL values primarily affect:

1. **Historical registrations** that started **before** the OLIDS first bulk load (mid-2025)[^1]
2. **A small percentage** of current/active registrations (under investigation)[^1]
3. **All EMIS practices** where historical data is required[^1]

Records for registrations starting **after** the first bulk load generally have complete episode type data (>99% populated).[^1]

***

## Root Cause

### EMIS Source System Limitations

OLIDS constructs Episode of Care data from EMIS source tables:[^1]

#### Admin_Patient Table

- **What it contains**: Current patient demographics including registration type, date of birth, address, registration dates[^1]
- **Structure**: One row per patient (overwritten each time patient details change)[^1]
- **OLIDS availability**: Snapshots captured from first bulk load onwards (mid-2025)[^1]
- **Limitation**: No historical versions before OLIDS implementation[^1]


#### Admin_PatientHistory Table

- **What it contains**: Audit trail recording timestamp and patient status when Admin_Patient changes[^1]
- **Purpose**: Tracks that a change occurred[^1]
- **Critical limitations**:
    - Does **not** include registration type field[^1]
    - Does **not** identify which practice made the update[^1]
    - Only records that change occurred, not what changed[^1]


### Why Can't We Use Admin_PatientHistory?

The Admin_PatientHistory table cannot be used to fill the gaps because:[^1]

1. **Missing registration type**: The table does not contain the field needed to populate `EPISODE_TYPE_SOURCE_CONCEPT_ID`
2. **No practice attribution**: Cannot determine which practice the registration change relates to
3. **Ambiguous change tracking**: Only shows a change happened at a point in time, not the details of what changed

### No Access to Legacy Data Resources

The OLIDS team currently does **not have access to a data resource** that would enable matching of legacy records within the data supplied specifically for EMIS. The team is working on aligning existing and available data transforms with what is available.[^1]

### Comparison to DDS (Legacy System)

The legacy DDS (Discover Data Service) maintained 5-10 years of historical snapshots of the Admin_Patient table, captured since practices were originally bulked with DDS. This provided complete registration type history.[^1]

OLIDS only has Admin_Patient snapshots from the first bulk load forward (mid-2025), creating a fundamental difference in historical data availability.[^1]


| Aspect | DDS (Legacy) | OLIDS (Current) |
| :-- | :-- | :-- |
| Registration type completeness | 100% populated [^1] | ~89% populated (varies by dataset) [^1] |
| Historical depth | 5-10 years [^1] | From first bulk only (mid-2025) [^1] |
| Source data | Historical Admin_Patient snapshots [^1] | Admin_Patient only; no legacy resource [^1] |
| Practice attribution | Available all periods [^1] | From first bulk forward only [^1] |
| Active patient data quality | High | High (>99% for current registrations) [^1] |


***

## Impact Assessment

### Use Cases **Significantly Impacted**

The NULL episode type limitation affects:

- **Historical cohort analysis**: Retrospective studies requiring registration type classification for periods before mid-2025[^1]
- **Longitudinal registration patterns**: Analysis of patient movement patterns and registration type changes over extended historical periods[^1]
- **Population health trends**: Historical trend analysis dependent on complete registration type data[^1]
- **Service planning with historical data**: Historical service utilisation analysis based on registration type segmentation[^1]
- **Research studies**: Studies requiring complete historical registration classification


### Use Cases **Minimally Impacted**

The following analytical use cases remain largely unaffected:[^1]

- **Live patient care**: Current patient registration information available for active patients[^1]
- **Active cohort analysis**: Current registrations (no end date) have >99% completeness[^1]
- **Direct care use cases**: Point-in-time patient registration status available for clinical decision support[^1]
- **Prospective analysis**: All registrations from mid-2025 onwards have complete episode type data[^1]
- **Current population management**: Active registered patient lists and cohorts
- **Quality improvement**: Current practice populations for QOF and quality initiatives

**Key Message**: If your analysis focuses on **current/active patients** or **data from mid-2025 onwards**, this issue has minimal impact.[^1]

***

## Expected Data Quality Levels

| Scenario | Expected Completeness | Current Status |
| :-- | :-- | :-- |
| **Current registrations** (no end date) | 100% | 99.4% in SEL; 0.6% NULL under investigation [^1] |
| **Registrations starting after first bulk** (mid-2025+) | 100% | ~100% populated [^1] |
| **Historical registrations** (pre-bulk) | **NULL - no viable source** | NULL values expected; **user acceptance required** [^1] |
| **Recent leavers/deaths** (within 5 years) | High | Varies by timing of event [^1] |


***

## What Should Users Do?

### User Acceptance Required

**Users must accept this limitation** for the foreseeable future. This is a structural constraint in EMIS source systems, not a data quality issue that can be immediately resolved.[^1]

### Recommended Approaches for Analysts

#### 1. Focus on Active Registrations

Prioritise analyses using current episodes (no end date) which have >99% completeness:[^1]

```sql
-- Optimal data quality: active registrations only
SELECT *
FROM OLIDS_COMMON.EPISODE_OF_CARE
WHERE EPISODE_OF_CARE_END_DATE IS NULL
  AND EPISODE_TYPE_SOURCE_CONCEPT_ID IS NOT NULL;
```


#### 2. Filter for Post-Bulk Data

Restrict analyses to registrations starting after the first bulk load date:

```sql
-- Complete post-bulk data
SELECT *
FROM OLIDS_COMMON.EPISODE_OF_CARE
WHERE EPISODE_OF_CARE_START_DATE >= '[FIRST_BULK_DATE]'
  OR EPISODE_TYPE_SOURCE_CONCEPT_ID IS NOT NULL;
```


#### 3. Flag and Document Limitations

Clearly identify records with known limitations in your analysis:

```sql
-- Add data quality flags to your queries
SELECT *,
  CASE 
    WHEN EPISODE_TYPE_SOURCE_CONCEPT_ID IS NULL 
      AND EPISODE_OF_CARE_START_DATE < '[FIRST_BULK_DATE]'
    THEN 'Historical - Known Limitation'
    WHEN EPISODE_TYPE_SOURCE_CONCEPT_ID IS NULL 
      AND EPISODE_OF_CARE_START_DATE >= '[FIRST_BULK_DATE]'
    THEN 'Under Investigation'
    ELSE 'Complete'
  END AS DATA_QUALITY_FLAG
FROM OLIDS_COMMON.EPISODE_OF_CARE;
```


#### 4. Check Your Dataset's NULL Rate

Assess the impact on your specific dataset:

```sql
-- Measure NULL rates by dataset and episode status
SELECT  
  LDS_DATASET_ID,
  CASE WHEN EPISODE_OF_CARE_END_DATE IS NULL 
    THEN 'Active' ELSE 'Historical' END AS EPISODE_STATUS,
  COUNT_IF(EPISODE_TYPE_SOURCE_CONCEPT_ID IS NULL) / COUNT(1) * 100 AS PERCENT_NULL,
  COUNT_IF(EPISODE_TYPE_SOURCE_CONCEPT_ID IS NULL) AS NULL_COUNT,
  COUNT(1) AS TOTAL_COUNT
FROM OLIDS_COMMON.EPISODE_OF_CARE
GROUP BY ALL
ORDER BY LDS_DATASET_ID, EPISODE_STATUS;
```


#### 5. Use DDS for Historical Analysis

If your organisation retains access to legacy DDS data, consider using it for historical studies requiring complete registration type data:[^1]

- Link OLIDS and DDS data using `SK_PATIENT_ID` where available
- Use DDS for pre-2025 historical analysis
- Use OLIDS for current and prospective analysis
- Document which data source supports each analytical output


#### 6. Accept NULL as Valid

For historical trend analysis, treat NULL as "unknown registration type" rather than a data error:[^1]

- Document the limitation in analysis reports
- State that historical episode type data is incomplete before mid-2025
- Quantify the proportion of NULL values in your analytical cohort
- Assess whether the limitation affects your conclusions

***

## Potential Solutions That Could Be Explored

**Important**: The OLIDS programme cannot commit to implementing these solutions at this time. These are potential options that could be explored, subject to prioritisation, feasibility, resources, and governance approval.[^1]

### Option 1: DSCRO-Sourced PDS File (ICB-Led Exploration)

**Who can explore**: ICBs and local organisations

**Approach**: Utilise DSCRO (Data Services for Commissioners Regional Office) sourced PDS (Personal Demographics Service) file containing patient registration history over time.[^1]

**Potential benefits**:

- PDS contains registration history across practices
- May provide organisation-level registration data

**Considerations**:

- PDS data may not include detailed episode type classifications
- Requires data linkage methodology between PDS records and OLIDS patient identifiers
- Subject to data governance and information sharing agreements
- Timeline dependent on DSCRO data availability and access approvals
- ICB-led initiative; requires local resources and prioritisation

**Status**: Not actively being pursued by OL-DAS; ICBs can explore independently

***

### Option 2: Historic File from EMIS/OPTUM (OL-DAS Led)

**Who is exploring**: OL-DAS team

**Approach**: Request EMIS/OPTUM provide a one-time historic extract of Admin_Patient records from source systems.[^1]

**Potential benefits**:

- Would provide registration type data with practice attribution for historical periods
- Most direct solution to the data gap

**Considerations**:

- Requires vendor cooperation and technical feasibility assessment
- May incur costs or contractual negotiations
- One-time extract would need to align with existing OLIDS bulk load dates
- Timeline uncertain; dependent on vendor response and prioritisation

**Status**: OL-DAS have requested; under investigation[^1]

***

### Option 3: Build Extract from Source Systems (ICB-Led Exploration)

**Who can explore**: ICBs and local organisations

**Approach**: Develop capability to extract and load historic registration data directly from practice source systems to merge with current OLIDS data.[^1]

**Potential benefits**:

- Direct access to practice-level historical data
- Could fill gaps for specific practices or organisations

**Considerations**:

- Requires significant development effort and testing
- May face technical limitations if source systems don't retain historical snapshots
- Needs practice-by-practice coordination and data access
- Data quality and consistency challenges across multiple source systems
- Resource-intensive; requires local development capacity

**Status**: Not actively being pursued by OL-DAS; ICBs can explore independently if local priorities warrant

***

### Implementation Reality

All potential solutions require:

- Time and development effort[^1]
- Programme prioritisation and governance approval[^1]
- Resources (technical, financial, human)[^1]
- Feasibility assessment[^1]

**No committed delivery dates are available**. Users must accept the current limitation whilst options are explored.[^1]

***

## Active Issues Under Investigation

**Tracking Reference**: Azure DevOps Work Item \#19217743[^1]

### High NULL Rate in Active Registrations

- **Observation**: SEL ICB identified ~40,000 records with no end date (active registrations) that lack episode type[^1]
- **Expected**: Active registrations should be >99% populated from Admin_Patient[^1]
- **Status**: Under investigation to determine why source data is not being captured correctly[^1]


### TPP Complete NULLs

- **Observation**: TPP practices in NWL and NEL regions showing complete NULL values for episode type[^1]
- **Status**: Separate issue being investigated; may have different root cause[^1]


### Sequencing Logic Review

- **Observation**: Potential issue with using "sequenced" Admin_Patient data (most recent record only per patient)[^1]
- **Impact**: May contribute to gaps for post-bulk registrations[^1]
- **Status**: Under technical review[^1]

***

## Escalation Process

If NULL episode types prevent critical analytical work:

### Step 1: Document Your Use Case

Clearly describe:

- The analysis you need to perform
- Why historical registration type data is essential
- The business/clinical impact of not having this data
- The time period your analysis covers


### Step 2: Quantify the Impact

Identify:

- Number of affected patients/records in your organisation
- Percentage of your analytical cohort affected
- Whether the limitation prevents the analysis entirely or reduces accuracy


### Step 3: Submit to Product Oversight Group

Raise via:

- Azure DevOps: Submit ticket tagged with your ICB and "Episode of Care"
- ICB triage sessions
- OLIDS programme governance channels

Formal escalation supports:

- Prioritisation of potential solutions[^1]
- Assessment of feasibility for alternative data sources[^1]
- Documentation of unmet analytical needs
- Potential for collective business case if multiple organisations affected

**Note**: Escalation does not guarantee resolution; all solutions require significant effort and prioritisation.[^1]

***

## Timeline and Expectations

### Short-term (Q1 2026)

- Investigation of unexpectedly high NULL rate in current registrations[^1]
- OL-DAS pursuing EMIS/OPTUM historic file request[^1]
- Users must accept limitation for active analytical work[^1]


### Medium-term (Q2-Q3 2026)

- Programme prioritisation and feasibility assessment of alternative data sources[^1]
- Potential technical solutions evaluated
- No committed delivery dates


### Long-term (Q4 2026+)

- Implementation of approved solution subject to development capacity and prioritisation[^1]
- Alternative solutions may remain under exploration
- Historical data gap may persist indefinitely

**Current expectation**: Users should plan analytical frameworks around this limitation for the foreseeable future.[^1]

***

## Contact and Support

### For Questions

- **ICB Triage Sessions**: Episode of Care data quality raised 5 December 2025[^1]
- **Azure DevOps**: Submit tickets tagged with relevant ICB and "Episode of Care"
- **OLIDS GitHub**: Documentation updates and guidance
- **Programme Communications**: Monitor for solution implementation timelines


### For Updates

- Check OLIDS GitHub repository for latest documentation
- Subscribe to OLIDS programme communications
- Participate in ICB triage and user testing sessions

***

## References

Azure DevOps Work Item \#19217743 - Episode of Care NULL Episode Type Investigation[^1]
