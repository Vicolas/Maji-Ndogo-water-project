# Maji-Ndogo-water-project
SQL project to solve the water crisis in Maji Ndogo(A fictitious location), solving real-world problems.

This project highlights the practical application of SQL in data exploration, cleaning, and analysis to address real-world problems in water quality and infrastructure. The detailed SQL queries and techniques demonstrated include data retrieval, aggregation, transformation, and ranking to provide actionable insights. This structured approach showcases the capability to handle large datasets, ensure data integrity, and derive meaningful conclusions to support decision-making in resource management.


# Disclaimer:
This project is part of a learning series in Data Science with ALX offered by Explore AI. The dataset and training document were provided by Explore AI.

# Part 1: Water Quality in Maji Ndogo: A Data Exploration Using SQL
In this first part of the project, I used a database of 60,000 records to examine the water quality in Maji Ndogo. The goal was to understand the data by getting an overview of each table and addressing inconsistencies. 
I queried the database to retrieve the first few records from each table and the overall information each table contained.
Tables:
data_dictionary
employee
global_water_access
location
water_quality
visits
water_source
well_pollution
In the next step, all the unique water sources available in the dataset were uncovered, including tap_in_home, tap_in_home_broken, well, shared_tap, and river.
1. River—People collect drinking water along a river. Millions of people use this open water source in Maji Ndogo. Water from a river is at high risk of being contaminated with biological and other pollutants, so it is the worst water source possible.
2. Well—These sources draw water from underground sources and are commonly shared by communities. Since these are closed water sources, contamination is much less likely than in a river. Unfortunately, many of the wells are not clean due to the aging infrastructure and past corruption of officials.
3. Shared tap— This is a tap in a public area shared by communities.
4. Tap in-home—These are taps inside citizens' homes. On average, about six people live together in Maji Ndogo, so each tap serves about six people.
5. Broken tap in home—These are taps installed in a citizen's home, but the infrastructure connected to that tap is not functional. Burst pipes, broken pumps, or non-working water treatment plants can cause broken taps in homes.

Next up, the visits table. I investigated all trips made to each of the water sources above to understand the frequency of these visits and how much time citizens spent at each location by checking the time_spent_in_queue, with emphasis on where time spent was over 500 minutes, that is, about 8 hours, and the water source where people spent the most amount of time queueing.

Moving on to investigate the quality of each water source, a table within the dataset contains a quality score for each visit made about a water source. The surveyors assign these scores to each source, from 1 being terrible to 10 for a good, clean water source in a home. Shared taps are not rated as high, and the score also depends on how long the queue times are.

The final step in part 1 was investigating the pollution associated with each water source. While some sources, like tap_in_home, are clean, others, primarily wells, are chemically or biologically contaminated. 

The well_polutuin table, where pollution results are recorded, had some inconsistencies. The biological column measures the amount of contamination in the water in units of CFU/mL. 0 is clean, and anything more than 0.01 is contaminated.

I checked the integrity of the data to ensure there was no contamination in wells where the result was 'clean.' The worst case is if we had contamination, but we think we didn't. People could get sick, so there was a need to ensure no errors. 

In some cases, it seemed that if the description field began with the word "Clean," the results were classified as "Clean" in the results column, even though the biological column was > 0.01.

Accurate descriptions should only include the word "Clean" if there was no biological contamination. However, I identified inconsistencies in the records that mistakenly have the word 'Clean' in the description column by searching for the word Clean with additional characters after it. These additional characters separated incorrect descriptions from the records that should have "Clean."

1. All records that mistakenly had Clean Bacteria: E. coli I updated to Bacteria: E. coli.
2. All records that mistakenly had Clean Bacteria: Giardia Lamblia I updated to Bacteria: Giardia Lamblia.
3. Allrecords that were clean remained clean, without any additional characterss after it.

Also, the results column was updated from Clean to Contaminated: Biological, where the biological column had a value greater than 0.01.

# Key Insight:
Identified contamination issues and corrected misclassified data to ensure accurate reporting.

-----------------------------------------------------------------------------------------------

# Part 2: Water Quality in Maji Ndogo: Clustering data to unveil Maji Ndogo's water crisis
Here, we begin by looking at some of the tables in the dataset at a larger scale, identifying some trends, summarising important data, and drawing insights. We then clean and update the data, starting with the employee table, which has information on the workers who put the data together. 

The initial document did not include the email addresses of these employees. The emails needed to be added to this dataset so stakeholders could send employees reports and figures. 

The email address for each employee was determined by:
- selecting the employee_name column
- replacing the space with a full-stop
- made it lowercase
- and stitched it all together

The result is first_name.last_name@ndogowater.gov. After that, I updated the table.

Zooming closer into the employees, the number of employees living in each town is investigated. The result shows how many workers live in smaller communities in the rural parts of Maji Ndogo. The number of records each employee collects is also checked to see the top 3 employees with the most location visits.

The main task begins by analyzing the location table, focusing on the province_name, town_name, and location_type to understand where the water sources are in Maji Ndogo.

This was done by creating a result set showing:
• province_name
• town_name
• An aggregated count of records for each town (named records_per_town).
• Ensured the data was grouped by both province_name and town_name.
2. Ordered results primarily by province_name. The towns were sorted by their record counts in descending order within each province.


The number of records for each location type shows that rural communities have more sources than urban sources by 60%.

Diving into the different sources, I was curious about.
1. How many people were surveyed in total?
	The sum of the number_of_people_served column from the water_source table was calculated to get the answer. 

2. How many wells, taps, and rivers there are?
	Each type of water source was counted, and wells happened to be the most common type. This information is critical to understanding how much repairs will cost. Knowing how many taps need to be installed and how much it will cost to install them, we can calculate how much it will cost to solve the water crisis.

3. How many people shared particular types of water sources on average?
The average number of people served was calculated. Calculating the average number of people served by a single instance of each water source type helps us understand the typical capacity or load on a single water source. This can help us decide which sources should be repaired or upgraded. For example, wells don't seem to be a problem, as fewer people share them.

On the other hand, 2000 people share a single public tap on average! This is directly reflected in the queue times, and it has become clear why. So, looking at these results, it would be wise to focus on improving shared taps first.


4. How many people get water from each type of source?
	The total number of people served by each type of water source was calculated here. 43% of people use shared taps in their communities; by adding tap_in_home and tap_in_home_broken together, we see that 31% of people have water infrastructure installed in their homes, but 45% (14/31) of these taps do not work! This isn't the tap itself that is broken, but rather the infrastructure, like treatment plants, reservoirs, pipes, and pumps that serve these homes, that are broken. 18% of people are using wells. But only 4916 out of 17383 are clean = 28%

This is where we began to find the data-driven solution to fix or improve the water infrastructure by using the following columns and methods
- Type of sources 
- Total people served grouped by the types 
- A rank based on the total people served, grouped by the types

We had established that tap_in_home is the best source of water. It was removed from the analysis since it did not need improvement. The created rank shows that shared_taps ranked as the number 1 priority for improvement, given the large number of people it serves.

Since there are many shared taps across different locations/ communities, I used the same ranking logic to find the top priority location to begin improvement with.

In summary, this survey aimed to identify the water sources people use and determine the total and average number of users for each source. Additionally, it examined the duration citizens typically spend in queues to access water. 

Insights
1. Most water sources are rural.
2. 43% of our people are using shared taps. 2000 people often share one tap.
3. 31% of our population has water infrastructure in their homes, but within that group, 45% face non-functional systems due to issues with pipes,
pumps, and reservoirs.
4. 18% of our people are using wells of which, but within that, only 28% are clean..
5. Our citizens often face long wait times for water, averaging more than 120 minutes.
6. In terms of queues:
- Queues are very long on Saturdays.
- Queues are longer in the mornings and evenings.
- Wednesdays and Sundays have the shortest queues.

# Key Insights:
1. Identified that shared taps are the top priority for improvement due to the high number of users and long queue times.
2. Highlighted the significant proportion of non-functional water infrastructure and the need for focused repair efforts.








