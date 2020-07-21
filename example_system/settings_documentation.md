# PowerGenome settings file documentation

This file provides documentation of each parameter in the current version of PowerGenome. It attempts to provide a comprehensive description of the data type and purpose of every parameter.

## Multi-scenario/period parameters

<!-- Combine model_year and model_first_planning_year into a list of tuples -->
### model_year

type: list[int]

description: Integer values. The final year in each planning period. Can be considered the planning year for capacity expansion.

### model_first_planning_year

type: list

description: Integer values. The first year in each planning period. These are combined with the values from [model_year](#model_year) to define the range of years for each planning period. Cost values (capex, fuel cost, etc) are the average of values from all years within a planning period.

### input_folder

type: str

description: Name of a subfolder - directly below the settings file - with user-supplied scenario information and extra input data.

### case_id_description_fn

type: str

description: Pointer to a csv file with columns `case_id` and `case_description`. `case_id` is a shorthand id for longer case description names.

### scenario_definitions_fn

type: str

description: Pointer to a csv file that starts with mandatory columns `case_id`, which should use `case_id` from the file `case_id_description.csv` as a foreign key, and `year`, which should match the [model_year](#model_year) values from the settings file. Every `case_id` should have a row for each value of `year` - make sure that the number of rows is equal to the number of unique case ids multiplied by the number of model years. All other columns are user-defined names that refer to the types of parameters in the settings file that will being changed across cases. The values in each column are also user-defined strings describing the scenario (e.g. "high", "mid", "low").

The column names and values in each column are used in the settings parameter [settings_management](#settings_management) to define how the default settings values should be changed across each case.

### distributed_gen_profiles_fn

type: str

description: Pointer to a csv file with normalized hourly generation profiles for distributed generation in all model regions listed in the settings file under [distributed_gen_method](#distributed_gen_method) and [distributed_gen_values](#distributed_gen_values).

### demand_response_fn

type: str

description: Pointer to a csv file with hourly (not normalized) profiles for demand response resources in each region/year/scenario. The top four rows are 1) the name of the DR resource (matching key values in the settings parameter [demand_response_resources](#demand_response_resources)), 2) the model year, 3) the scenario name (matching names from the "demand_response" column of [scenario_definitions_fn](#scenario_definitions_fn)), and 4) the model region from [model_regions](#model_regions).

### emission_policies_fn

type: str

description: Pointer to a csv file that describes the emission policies in each case. The first two columns are `case_id` and `year`. Next, the `region` column can either contain the name of a model region or the string "all" (when identical policies are applied to all regions). The column `copy_case_id` indicates if policies from another case should be used (slightly more clear and hopefully fewer errors than just using copy/paste across multiple rows). Other column names should match the columns used in output policy files (e.g. `RPS`, `CES`, etc) and contain numeric values or the string `None`.

**Come back to other external data files**

### reduce_time_domain

type: bool

description: If the load and generation profiles should be reduced from 8760 hourly values to a representative subset of hours.

### time_domain_periods

type: int

description: The number of periods (or clusters) of days. As an example, a system with 10 representative weeks would have 10 periods.

### time_domain_days_per_period

type: int

description: The number of days (24 consecutive hours) to include in each period. If a model uses operational constraints like start-up or shutdown times then multiple days might be appropriate.

### include_peak_day

type: bool

description: If the system-wide peak demand day should be included in one of periods.

### demand_weight_factor

type: int

description: Demand and variable generation profiles are scaled from 0-1 before calculating clusters. Demand profiles are then multiplied by this parameter. Values greater than 1 will weight demand more heavily than variable generation profiles.

### distributed_gen_method

type: dict

description: This is a dictionary with keys that can be any value from [model_regions](#model_regions). Values define the method by which distributed generation profiles are calculated in each region. Available values are `capacity` and `fraction_load`. This method is used with `distributed_gen_values` and `distributed_gen_profiles_fn`.

### distributed_gen_values

type: dict

description: This dictionary has top-level keys of model years from `model_year`, then regions from `model_region`, then a numeric value representing either `capacity` (MW) or `fraction_load` (0-1).

### avg_distribution_loss

type: float

description: Distribution level line-loss is used when subtracting distributed generation from total load. Total load is load at the transmission network, so it includes distribution line loss.

### demand_response_resources

type: dict

description: This nested dictionary has top-level keys of model years from [model_year](#model_year). The second level is names of demand response resources. Below the name are they keys:

- `fraction_shiftable` (float)
- `parameter_values` (dict) with key: value pairs for specific columns in the GenX file `Generators_data.csv`.

.
.
.
.
.

### data_years

type: list(int)

description: The years of EIA 923 and 860 data to use when calculating heat rates of generators.

### target_usd_year

type: int

description: The dollar year that all costs will be converted to.

### target_region_pst_offset

type: int

description: Soon to be depreciated. IPM load profiles are provided in Pacific Standard Time, and this value is used to shift them to a different timezone. Note that all hourly profiles should be aligned to a common timezone.

### capacity_col

type: str

description: The capacity of a generator is given in terms of `capacity_mw`, `winter_capacity_mw`, or `summer_capacity_mw`, representing the reported nameplate, winter, and summer capacity. Use one of these three values to determine the total capacity available in each resource cluster. Summer is usually the lowest value, and nameplate the highest.

### small_hydro

type: bool

description: If PG should separate small hydroelectric dams into their own resource type. Some states treat small hydro different from large hydro for RPS qualification.

### small_hydro_mw

type: int

description: The generator size (MW) below which hydroelectric units are considered "small".

### small_hydro_regions

type: list(str)

description: Regions from [model_regions](#model_regions) that will have hydroelectric generators split into small and conventional. Regions not listed here will not have small hydro split out.

### model_regions

type: list(str)

description: The names of model regions that will be used in output files. These can either be single IPM regions or the name assigned to a group of (one or more) IPM regions in [region_aggregations](#region_aggregations).

### region_aggregations

type: dict(list(str))

description: A dictionary with list values, used to aggregate IPM regions into groups. The keys, which are names of the aggregated regions, should be used in [model_regions](#model_regions). These region names should also be used in the following parameters:

- [regional_no_grouping](#regional_no_grouping)
- [alt_num_clusters](#alt_num_clusters)
- [regional_tag_values](#regional_tag_values)
- [new_gen_not_available](#new_gen_not_available)
- [new_wind_solar_regional_bins](#new_wind_solar_regional_bins) (depreciated)
- [cost_multiplier_region_map](#cost_multiplier_region_map)
- [load_region_map](#load_region_map)
- [future_load_region_map](#future_load_region_map)
- [alt_growth_rate](#alt_growth_rate)
- [aeo_fuel_region_map](#aeo_fuel_region_map)

### cluster_method

type: str

description: Not implemented yet, leave as `kmeans`

### num_clusters

type: dict(str: int)

description: The default number of clusters that resources will be split into in every region. More than one cluster might be appropriate if there are a large number of resources with varied ages/sizes/heat rates. Use `alt_num_clusters` to specify a different number of resource clusters in specific regions.

### alt_num_clusters

type: dict

description: A nested dictionary with keys from [model_regions](#model_regions), and values that are a key: value pair of the resource name (from EIA) and the number of clusters to create within that region. This parameter lets you set a different number of clusters for a resource within a specific region. You can specify a value of 0 to drop a resource from within a region, which is useful when only a few generators exist and they have extremely high heat rates according to EIA data.

### alt_cluster_method

type: str

description: Not currently in use. Designed to specify different algorithms for clustering existing generators.

### group_technologies

type: bool

description: If `True`, group different technologies together as specified in [tech_groups](#tech_groups). This can be used to combine multiple small capacity technologies that serve a similar purpose or have similar fuel inputs.

### tech_groups

type: dict

description: Key values are the name for a grouping of technologies, values are a list of the EIA technology names to include in the group. This renames all of the listed technologies to the group name.

### regional_no_grouping

type: ???

description: ???

### capacity_factor_techs

type: list

description: (Not used anymore, fix in code.) Existing technologies that should have their capacity discounted by their average capacity factor (calculated using generation data from [capacity_factor_default_year_filter](#capacity_factor_default_year_filter)).

### capacity_factor_default_year_filter

type: list

description: The years of data to use when calculating capacity factors for each technology cluster.

### alt_year_filters:

type: dict

description: Keys are EIA technology names, values are a list of years to use when calculating the capacity factor of that technology.

### derate_capacity

type: bool

description: If calculated capacity factors should be used to derate the total capacity of a technology.

### retirement_ages

type: dict

description: Keys are EIA technology names, values are the maximum age of a generator before it is retired. Retirement means that PowerGenome will remove the capacity of a generator. If you want a capacity expansion model to have control over retirements for a technology, set the retirement age to some very high value like 500.

### model_tag_names

type: list

description: This parameter was designed specifically for GenX outputs, and the items of this list each correspond to a column in `Generators_data.csv`.

### default_model_tag

type: Union(int, float, str)

description: This is the default value for every row of the columns specified in [model_tag_names](#model_tag_names).
