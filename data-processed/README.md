# Data submission instructions

This page is intended to provide teams with all the information they
need to submit scenarios.  

All scenarios should be submitted directly to the [data-processed/](./) 
folder. Data in this directory should be added to the repository
through a pull request. 

Due to file size limitation, the file can be submitted in a in a `.parquet` or 
`.gz.parquet`.

All submissions will be automatically validated upon submission, for
more information please consult the validation wiki page. 

The submission file format has been updated starting round 4 (September 2024).  For 
information on previous file format please refer to past version of this 
README file.

----

## Example

See this file
for an illustration of part of a (hypothetical) submission files. 

---

## Subdirectory

Each subdirectory within the [data-processed/](./) directory has the
format:

    team-model
    
where 

- `team` is the teamname and 
- `model` is the name of your model. 

Both team and model should be less than 15 characters, and not include
hyphens nor spaces.

Within each subdirectory, there should be a metadata file, a license
file (optional), and a set of scenarios.

----

## Metadata 

The metadata file name should have the following format

    metadata-team-model.txt
    
where 

- `team` is the teamname and 
- `model` is the name of your model. 
    
The metadata file must follow the [documented description](https://github.com/midas-network/flu-scenario-modeling-hub/blob/main/data-processed/METADATA.md). 
An example [hypothetical metadata file](https://github.com/midas-network/flu-scenario-modeling-hub/blob/main/data-processed/MyTeam-MyModel/metadata-MyTeam-MyModel.txt) 
has been posted in the `data-processed` directory. 

---

## License (optional)

License information for data sharing and reuse is requested in the
metadata, including a link to the license text. If you cannot link to
the text of a standard license and have specific license text, include
a license file named

    LICENSE.txt

----

## Date/Epiweek information

For week-ahead scenarios, we will use the specification of epidemiological
weeks (EWs) [defined by the US CDC](https://ndc.services.cdc.gov/wp-content/uploads/MMWR_Week_overview.pdf)
which run Sunday through Saturday.

There are standard software packages to convert from dates to epidemic weeks
and vice versa. E.g. [MMWRweek](https://cran.r-project.org/web/packages/MMWRweek/) 
for R and [pymmwr](https://pypi.org/project/pymmwr/) and 
[epiweeks](https://pypi.org/project/epiweeks/) for python.

---

## Model Results

Each model results file within the subdirectory should have the following 
name

    YYYY-MM-DD-team-model.parquet
    
where

- `YYYY` is the 4 digit year,
- `MM` is the 2 digit month,
- `DD` is the 2 digit day,
- `team` is the teamname, and
- `model` is the name of your model.

"parquet" files format from Apache is "is an open source, column-oriented data
file format designed for efficient data storage and retrieval". Please find more
information on the [parquet.apache.com](https://parquet.apache.org/) website.

The "arrow" library can be used to read/write the files in 
[Python](https://arrow.apache.org/docs/python/parquet.html) and 
[R](https://arrow.apache.org/docs/r/index.html).
Other tools are also accessible, for example [parquet-tools](https://github.com/hangxie/parquet-tools)

For example, in R:
```
# To write "parquet" file format:
filename <- ”path/YYYY-MM-DD-team_model.parquet”
arrow::write_parquet(df, filename)
# with "gz compression"
filename <- ”path/YYYY-MM-DD-team_model.gz.parquet”
arrow::write_parquet(df, filename, compression = "gzip", compression_level = 9)

# To read "parquet" file format:
arrow::read_parquet(filename)
```

The date YYYY-MM-DD should correspond to the start date for scenarios
projection ("first date of simulated transmission/outcomes" as noted in the
scenario description on the main 
[README, Submission Information](https://github.com/midas-network/flu-scenario-modeling-hub#submission-information)).

The `team` and `model` in this file must match the `team` and `model` in the 
directory this file is in. Both `team` and `model` should be less than 15 
characters, alpha-numeric and underscores only, with no spaces or hyphens.

If the size of the file is larger than 100MB, it should be submitted in a 
`.gz.parquet` format. 

---

## Model results file format

The file must be a comma-separated value (csv) file with the following 
columns (in any order):

The output file should contains eight columns:
- `origin_date`
- `scenario_id`
- `target`
- `horizon`
- `location`
- `age_group`
- `output_type` 
- `output_type_id` 
- `value`

No additional columns are allowed.

Each row in the file is a specific type for a scenario for a location on
a particular date for a particular target. 


### `origin_date`

Values in the `origin_date` column must be a date in the format

    YYYY-MM-DD
    
The `origin_date` is the start date for scenarios (first date of 
simulated transmission/outcomes).
The "origin_date" and date in the filename should correspond.


### `scenario_id`

The standard scenario id should be used as given in in the scenario 
description in the [main Readme](https://github.com/midas-network/flu-scenario-modeling-hub). 
Scenario id's include a captitalized letter and date as YYYY-MM-DD, e.g., 
`A-2022-08-14`.


### `target`

The submission can contain multiple output type information: 
- 100 representative trajectories from the model simulations. We will 
  call this format "sample" output type. For more information, please
  consult the 
  [sample](https://github.com/midas-network/flu-scenario-modeling-hub/tree/master/data-processed#sample) 
  section.
- A set of quantiles value for all the tarquets (except peak timing).
  We will call this format "quantile" output type. For more information, 
  please consult the 
  [quantile](https://github.com/midas-network/flu-scenario-modeling-hub/tree/master/data-processed#quantile-and-mean) 
  section. 
- A cumulative distribution function for the peak timing target. 
  We will call this format "cdf" output type. For more information, 
  please consult the 
  [quantile](https://github.com/midas-network/flu-scenario-modeling-hub/tree/master/data-processed#quantile-and-mean) 
  section. 

The requested targets are (for "sample" type output):
- weekly incident deaths (US level only)
- weekly incident hospitalizations (US + State level)

Optional target (for "quantile" or "cdf" type output):
- quantile:
    - weekly cumulative deaths (US level only)
    - weekly cumulative hospitalizations (US + State level)
    - weekly incident deaths (US level only)
    - weekly incident hospitalizations (US + State level)
    - peak size hospitalizations (US + State level)
- cdf:
    - weekly peak timing hospitalizations (US + State level)

For all the targets, the age group `"0-130"` is required, all the incident and 
cumulative targets can also include other age group information (optional).

Values in the `target` column must be one of the following character strings:
- "inc death"  
- "inc hosp"
- "cum death"  
- "cum hosp"
- "peak size hosp"
- "peak time hosp"


#### inc death

This target is the national incident (weekly) number of deaths predicted
by the model during the week that is N weeks after
`origin_date`. There should not be any death projections at
the state level. 

A week-ahead scenario should represent the total number of new deaths
reported during a given epiweek (from Sunday through Saturday,
inclusive).

Predictions for this target will be evaluated compared to the number of
new deaths derived from the CDC multiplier model, which inflates flu
deaths reported in the hospital by a factor accounting for
underreporting and out-of-hospital mortality.These estimates are
published in real time starting a few weeks into the influenza
season. 


#### inc hosp

This target is the incident (weekly) number of hospitalized cases
predicted by the model during the week that is N weeks after
`origin_date`.

A week-ahead scenario should represent the total number of new
hospitalized cases reported during a given epiweek (from Sunday through
Saturday, inclusive).

Predictions for this target will be evaluated against the weekly number
of new hospitalized cases, as recorded by the U.S. Department of
Health & Human Services Flu and COVID-19 system (derived from the prior
day influenza admissions variable). 


#### cum death

This target is the national cumulative number of deaths predicted by the
model up to and including N weeks after `origin_date`. There
should be 0 cumulative deaths on week 0 of projection.

A week-ahead scenario should represent the cumulative number of deaths
reported on the Saturday of a given epiweek.

Predictions for this target will be evaluated compared to the number of
cumulative deaths derived from the CDC multiplier model, which inflates
flu deaths reported in the hospital by a factor accounting for
underreporting and out-of-hospital mortality.These estimates are
published in real time starting a few weeks into the influenza season.


#### cum hosp

This target is the cumulative number of incident (weekly) number of
hospitalized cases predicted by the model during the week that is N
weeks after `model_projection_date`.  There should be 0 cumulative
hospitalization on week 0 of projection.

A week-ahead scenario should represent the cumulative number of
hospitalized cases reported up to the Saturday of a given epiweek.

Predictions for this target will be evaluated against the cumulative
number of hospitalized cases since the start of the projection period,
as recorded by the U.S. Department of Health & Human Services Flu and
COVID-19 system (derived from the prior day influenza admissions
variable)


#### peak time hosp

This target is the cumulative probability of the incident
hospitalization peak occurring before or during the week that is N
weeks after `orgin_date`. For instance "peak
time hosp" on the 22nd epiweek of projection is the probability that 
hospitalizations peak within the first 22 weeks of the projection 
period. This cumulative probability will be 1 on the last week of the 
projection period. A probability of 1 in the first week of the projection 
period could mean either future projections are not expected to 
exceed a prior peak or projections expect the peak will occur in the 
first week.


Predictions for this target will be evaluated against the week of the
peak number of hospitalized cases, as recorded by the U.S.
Department of Health & Human Services Flu and COVID-19 system
(derived from the prior day influenza admissions variable).

#### peak size hosp

This target is the magnitude of the peak of weekly incident
hospitalizations in the model, when considering the full projection
period. 

Further, we do not expect a full time series, the `horizon`
column associated with this value should be set to `NA`.

Predictions for this target will be evaluated against the size of the
peak number of weekly hospitalized cases, as recorded by the
U.S. Department of Health & Human Services Flu and COVID-19 system
(derived from the prior day influenza admissions variable).


### `horizon`

Values in the `horizon` column must be a integer (N) between 1 and last week 
horion value representing the associated target value during the N weeks
after `origin_date`. 

For example, between 1 and 39 for Round 4 ("**Simulation end date:** 
 June 1, 2024 (39-week horizon)") and in the following example table,
the first row represent the number of incident death in the US, for the 1st 
epiweek (epiweek ending on 2023-09-09) after 2023-09-03 for the scenario 
A-2023-08-14. 

|origin_date|scenario_id|location|target|age_group|horizon|...|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|2023-09-03|A-2023-08-14|US|inc death|0-130|1|...|
|||||||


### `location`

Values in the `location` column must be one of the "locations" in this 
[FIPS numeric code file](../data-locations/locations.csv) which includes 
numeric FIPS codes for U.S. states, counties, territories, and districts as 
well as "US" for national scenarios. 

Please note that when writing FIPS codes, they should be written in as a 
character string to preserve any leading zeroes.


### `output_type`

Values in the `output_type` column are either

- "sample" or 
- "quantile" (optional) or
- "cdf" (optional)


This value indicates whether that row corresponds to a "sample" scenario or a
quantile scenario, etc. 

**Scenarios must include "sample" scenario for every
  scenario-location-target-horizon-age_group group.**

### `output_type_id`

#### `sample`

For the optional simulation samples format only. Values in the `output_type_id` 
column are numeric between `1` and `100` indicating an id sample number. 
Each ID number represents one in 100 representative trajectories from the 
simulations. 

**All scenario-location-target-horizon-age_group group should have a unique 
associated sample.**

For example:

|origin_date|scenario_id|location|target|horizon|age_group|output_type|output_type_id|value|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|2023-09-03|A-2023-08-14|US|inc death|1|0-130|sample|1||
|2023-09-03|A-2023-08-14|US|inc death|2|0-130|sample|1||
|2023-09-03|A-2023-08-14|US|inc death|3|0-130|sample|1||
||||||||||
|2023-09-03|A-2023-08-14|US|inc death|1|0-130|sample|2||
|2023-09-03|A-2023-08-14|US|inc death|2|0-130|sample|2||
||||||||||


#### `quantile` 

Values in the `output_type_id` column are quantiles in the format

    0.###
    
For quantile scenarios, this value indicates the quantile for the `value` in
this row. 

Teams should provide the following 23 quantiles:

``` 
0.010 0.025 0.050 0.100 0.150 0.200 0.250 0.300 0.350 0.400 0.450 0.500
0.550 0.600 0.650 0.700 0.750, 0.800 0.850 0.900 0.950 0.975 0.990 
```

An optional `0`  and `1` value can also be provided.

For example:

|origin_date|scenario_id|location|target|horizon|age_group|output_type|output_type_id|value|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|2023-09-03|A-2023-08-14|US|inc death|1|0-130|quantile|0.010||
|2023-09-03|A-2023-08-14|US|inc death|1|0-130|quantile|0.025||
||||||||||


#### `cdf`

Values in the `output_type_id` column are the epiweek associated with cumulative 
probability of the incident hospitalization peak occurring before or during the 
week that is N weeks after `origin_date` in the format:

    EWYYYYWW
    

For instance `"EW202337"`` is the probability that hospitalizations peak within 
the epiweek 2023-37 or before.

Teams should provide the complete time series associated with the round, and the
`horizon` column should be set to `NA` value. The week 
information should be in 2 digits format, so if the epiweek is for example 2024-2, 
then it should be reported as `"EW202402"`. 

It can be calculated by applying:

-  `origin_date` +  7 * `N` - 1 (N being the number of
week ahead projection in the associated target, e.g `"1 wk ahead"`, 
`"2 wk ahead"` after the start of the projection), and transform
the output in epiweek format.

For example:
```
# If `origin_date` is "2023-09-03"

# Week 1 will be:
week1_date = as.Date("2023-09-03") + 7 * 1 - 1
epiweek1 = MMWRweek::MMWRweek(week1_date) 
epiweek1 

```

The output file with the cdf should follow this example (following round 4):
|origin_date|scenario_id|location|target|horizon|age_group|output_type|output_type_id|value|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|2023-09-03|A-2023-08-14|US|inc death|NA|0-130|cdf|EW202336||
|2023-09-03|A-2023-08-14|US|inc death|NA|0-130|cdf|EW202337||
||||||||||
|2023-09-03|A-2023-08-14|US|inc death|NA|0-130|cdf|EW202322||


### `value`

Values in the `value` column are non-negative numbers indicating the associated
output_type prediction for this row. 

#### Peak time hosp

For the `peak time hosp` target, the values in the `value` column are non-negative 
numbers between 0 and 1.  


### `age_group ` 

Values in the  `age_group` column are:

- "0-4"
- "5-17"
- "18-49"
- "50-64"
- "65-130"
Or any aggregation of the previous list, for example: "0-17". 
If the submissions files contain projections for the overall population, 
please use `0-130` as age-group. 

The `age_group` are optionals, however, the submission should contain at least 
one age group: `0-130`, if multiples `age_group` are provided the overall 
population should still be provided with the age group `0-130`. 

**For the `peak` targets, only the age-group `0-130` is required.**

## Scenario validation

To ensure proper data formatting, pull requests for new data or updates in
data-processed/ will be automatically validated

When a pull request is submitted, the data are validated by running the
scripts in [validation.R](../code/validation/validation.R). The intent for
these tests are to validate the requirements above and all checks are 
specifically enumerated [on the wiki](https://github.com/midas-network/flu-scenario-modeling-hub/wiki/Scenario-File-Checks).

Please [let us know](https://github.com/midas-network/flu-scenario-modeling-hub/issues) if
the wiki is inaccurate.

#### Workflow

When a pull request is submitted, the validation will be 
automatically triggered.

- If the pull request (PR) contains update on metadata 
and/or abstract file(s):
    - These files are manually validated, the automatic validation
    will only returns a message indicating it did not run any
    validation. 

- If the PR contains model output submission file(s). The validation 
automatically runs and output a message and a PDF file containing the 
projections of the requested targets at national and State level, 
plus some information on calibration.

    - The validation has 3 possible output:
        - "Error": the validation has failled and returned a message 
        indicating the error(s). The error(s) should be fixed to have the PR 
        accepted
        - "Warning": the PR can be accepted. However, it might be necessary 
        for the submitting team to validate if the warning(s) is expected or 
        not before merging the PR.
        - "Success": the validation did not found any issue and returns a message 
        indicating that the validation is a success and the PR can be merged.


#### Run checks locally

To run these checks locally rather than waiting for the results from a pull
request, follow [these instructions](https://github.com/midas-network/flu-scenario-modeling-hub/wiki/Scenario-File-Checks#file-checks-running-locally)
(section File Checks Run Locally).




