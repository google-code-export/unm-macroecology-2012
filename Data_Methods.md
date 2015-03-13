This wiki page is meant to give information about how the data that we have is compiled. The purpose is to clarify potential ambiguities of the data.

# Overview of Methods #

For a detailed discussion of the process, see the closed issues labeled Data (link here: http://code.google.com/p/unm-macroecology-2012/issues/list?can=1&q=label%3Adata).  For links to primary sources, see the DataLinks page.

Below is an overview of the steps that were followed to get the final dataset.

  * Automated download of per-college, self-reported emissions data from ACUPCC (~600 colleges total).  This data was manually checked/currated in an attempt to identify and correct obvious errors, which are documented in a metadata file.
  * Zipcodes were manually obtained for all colleges in the ACUPCC dataset (it turns out that we could have gotten this from NCES).
  * Locations and ID codes of US major airports was obtained.  The nearest major airports from ACUPCC colleges were calculated based on zipcode (~300 airports total).
  * Daily weather station records were obtained from these ~300 airports, and yearly summaries were computed.  Weather data was joined to ACUPCC college based on proximity, calculated above.
  * Detailed per-college 2010 information was obtained from NCES, and was joined to ACUPCC colleges based on name and zipcode.

# Weather/Geographic Data #
See [issue 30](https://code.google.com/p/unm-macroecology-2012/issues/detail?id=30) for details.  Location of nearest major airport was calculated for each college.  One year (2010) of daily weather data was automatically obtained from each airport.  Yearly summaries of several variables were obtained.  Distance from school to airport is also shown in decimal degrees.


# ACUPCC Data #

The American College & University’s President Climate Commitment is a consortium of universities that have all agreed into a compact to make greenhouse gas emission data for each university public and also to promote progress towards footprint ‘neutrality’. All committing universities are required to perform the following actions:

  * Complete an emissions inventory.

  * Within two years, set a target date and interim milestones for becoming climate neutral.

  * Take immediate steps to reduce greenhouse gas emissions by choosing from a list of short-term actions.

  * Integrate sustainability into the curriculum and make it part of the educational experience.

  * Make the action plan, inventory and progress reports publicly available.

## Emissions inventory/Greenhouse gas inventory ##

Campuses are expected to conduct thorough inventories of sources of greenhouse gases (ghg), but at a minimum universities are required to submit ghg from ‘emissions produced through on-site combustion of fossil fuels; electricity consumption; student, faculty, and staff commuting; and institution-funded air travel’.

It’s important to note that not every university will report mobile combustion, process emissions, fugitive emissions, purchased heating, cooling, steam and solid waste/wastewater derived emissions (described below). So it may be more prudent to just combine the emissions from electricity consumption & on-site combustion – so that every school will have directly comparable data. Total gross emissions may take into account some sources of emissions that some schools report and others don’t.

Most universities use a campus carbon calculation system developed by folks at the University of New Hampshire. The calculation spreadsheet takes input as sources of greenhouse gases (e.g. kWh electricity purchased, gallons of diesel fuel burned, BTU gas used, etc.) and converts these to metric tons CO2 based on standard multiplication factors (e.g. 0.686 kg CO2 / kWh electricity used).

## The data in the master data sheet: ##

  * **Year**: Colleges are instructed to report emissions for an exactly 12 month period. Year is generally the starting year of the reporting period.

  * **Gross emissions (Scope 1 & 2)**: Combines the emissions from Scope 1 (ghg produced from on campus activities) and Scope 2 (ghg produced from importing energy, other resources)

  * **Gross emissions (Scope 1, 2 & 3)**: Combines the above two scopes as well as ghg that were produced from university travel/commuting

  * **Net emissions**: Subtracts renewable energy certificates (REC) from gross emissions. Apparently, REC’s are bought/traded and show that you’re supporting renewable energy that someone else is using.

  * **Stationary Combustion**: Emissions from all on-campus fuel combustion, excluding vehicle fuels. This takes into account both on campus cogeneration plants and heating/cooling.

  * **Purchased Electricity**: Calculated from the amount of kWh purchased

  * **Commuting**: Emissions derived from gallons of fuel used for student/faculty commuting. If data is given, takes into account the # of commuters, average distance of commute, and how they commute – converts these into fuel gallon totals.

  * **Heating degree days**: Calculation based on how much below a certain temperature (65\*F) a particular location is for a given year. Relative way to measure how much heating is required for a building at a given locations – based on local weather.

  * **Cooling degree days**: Opposite of above.

  * **Biogenic emissions from stationary combustion**: This values comes from “total carbon dioxide emissions generated through the stationary combustion of biomass and biomass-based fuels” – so biofuels, wood-burning, etc.

  * **Purchased Steam**: This is calculated from the amount of steam purchased and imported to campus for energetic use, but is produced outside of the university.

  * **Purchased Cooling**: Same as above, but for imported cooled water.

  * **Mobile Combustion**: Emissions that are calculated from the amount of fuel (diesel, gasoline, electric powered) that is used for university-owned vehicles).

  * **Purchased Heating**: Calculated from energy used to produce heating for the university, but the energy used was performed by an entity outside of the university; i.e. energy used when hot water is imported to university from outside party.

  * **Purchased Cooling**: Same as above, but for cooling related activities (i.e. chilled water importing).

  * **Sequestration due to land owned by the institution**: Amount of CO2 sequestered by the university on university owned property.

  * **Fugitive Emissions**: This is described as emissions from “production, processing, transmission, storage, and use of fuels and other substances”. Primarily this includes methane given off by farm animals owned by the institution and emissions from the use of refrigerants that produce volatile hydrocarbons.

  * **Process emissions**: GHG from non-fuel combusting processes, e.g. given off from smelting and other industrial activities.

  * **Solid waste**: GHG from incineration or decomposition of solid waste (calculated from tons of waste incinerated, deposited in landfills, etc.)

  * **Biogenic emissions from mobile combustion**: Emissions generated from the use of biofuels in mobile fleets – e.g. the use of biodiesel in university owned vehicles.

  * **Air Travel**: Calculated from total miles flown by students/faculty

# NCES #
See [issue 33](https://code.google.com/p/unm-macroecology-2012/issues/detail?id=33) for detail.
Institutions were selected by group -- all degree-granting institutions were selected:
http://nces.ed.gov/ipeds/datacenter/InstitutionByGroup.aspx

Data was gathered for 2010.  A number of variables were selected.  The resulting table was merged with ACUPCC data.  Name mismatching between ACUPCC and NCES has caused some problems.

Ambiguous Variables are described in detail below (directly from NCES site):

**Control of Institution**: A classification of whether an institution is operated by publicly elected or appointed officials or by privately elected or appointed officials and derives its major source of funds from private sources.

Public institution - An educational institution whose programs and activities are operated by publicly elected or appointed school officials and which is supported primarily by public funds.

Private not-for-profit institution - A private institution in which the individual(s) or agency in control receives no compensation, other than wages, rent, or other expenses for the assumption of risk. These include both independent not-for-profit schools and those affiliated with a religious organization.

Private for-profit institution - A private institution in which the individual(s) or agency in control receives compensation other than wages, rent, or other expenses for the assumption of risk.

**Degree of Urbanization**: Locale codes identify the geographic status of a school on an urban continuum ranging from “large city” to “rural.” They are based on a school’s physical address. The urban-centric locale codes introduced in this file are assigned through a methodology developed by the U.S. Census Bureau’s Population Division in 2005. The urban-centric locale codes apply current geographic concepts to the original NCES locale codes used on IPEDS files through 2004.

City: Large: Territory inside an urbanized area and inside a principal city with population of 250,000 or more.

City: Midsize: Territory inside an urbanized area and inside a principal city with population less than 250,000 and greater than or equal to 100,000.

City: Small: Territory inside an urbanized area and inside a principal city with population less than 100,000.

Suburb: Large: Territory outside a principal city and inside an urbanized area with population of 250,000 or more.

Suburb: Midsize: Territory outside a principal city and inside an urbanized area with population less than 250,000 and greater than or equal to 100,000.

Suburb: Small: Territory outside a principal city and inside an urbanized area with population less than 100,000.

Town: Fringe: Territory inside an urban cluster that is less than or equal to 10 miles from an urbanized area.

Town: Distant: Territory inside an urban cluster that is more than 10 miles and less than or equal to 35 miles from an urbanized area.

Town: Remote: Territory inside an urban cluster that is more than 35 miles of an urbanized area.

Rural: Fringe: Census-defined rural territory that is less than or equal to 5 miles from an urbanized area, as well as rural territory that is less than or equal to 2.5 miles from an urban cluster.

Rural: Distant: Census-defined rural territory that is more than 5 miles but less than or equal to 25 miles from an urbanized area, as well as rural territory that is more than 2.5 miles but less than or equal to 10 miles from an urban cluster.

Rural: Remote: Census-defined rural territory that is more than 25 miles from an urbanized area and is also more than 10 miles from an urban cluster.

**Carnegie Classification, Size and Setting**: To ensure continuity of the classification framework and to allow comparison across years, the 2010 Classification update retains the same structure of six parallel classifications, initially adopted in 2005. They are as follows: Basic Classification (the traditional Carnegie Classification Framework), Undergraduate and Graduate Instructional Program classifications, Enrollment Profile and Undergraduate Profile classifications, and Size & Setting classification. These classifications provide different lenses through which to view U.S. colleges and universities, offering researchers greater analytic flexibility.

These classifications are time-specific snapshots of institutional attributes and behavior based on data from 2008 to 2010. and collectively they depict the most current landscape of U.S. colleges and universities. Institutions might be classified differently using a different timeframe. Individual classifications are not updated with more recent data.

This classification describes institutions’ size and residential character. Because residential character applies to the undergraduate student body, exclusively graduate/professional institutions are not included.

Four-year institutions are divided into four categories of full-time equivalent (FTE) enrollment and three categories of residential character. Neither characteristic implies differences in the quality of undergraduate education, but an institution’s location along the two continua generally corresponds to a distinctive mix of educational challenges and opportunities. Because few two-year institutions serve a residential population, these institutions are classified solely based on FTE enrollment.

The residential character measure is based on two attributes: the proportion of degree-seeking undergraduates who attend full-time and the proportion living in institutionally-owned, -operated, or -affiliated housing. It is important to note the variety of situations of students who do not live in college or university housing. Some are true “commuting” students, while others may live with other students in rental housing on the periphery of campus, and still others are distance education students who rarely or never set foot on a campus.

The categories are as follows:

VS2: Very small two-year. Fall enrollment data show FTE enrollment of fewer than 500 students at these associate’s degree granting institutions.

S2: Small two-year. Fall enrollment data show FTE enrollment of 500–1,999 students at these associate’s degree granting institutions.

M2: Medium two-year. Fall enrollment data show FTE enrollment of 2,000–4,999 students at these associate’s degree granting institutions.

L2: Large two-year. Fall enrollment data show FTE enrollment of 5,000–9,999 students at these associate’s degree granting institutions.

VL2: Very large two-year. Fall enrollment data show FTE enrollment of at least 10,000 students at these associate’s degree granting institutions.

VS4/NR: Very small four-year, primarily nonresidential. Fall enrollment data show FTE enrollment of fewer than 1,000 degree-seeking students at these bachelor’s degree granting institutions. Fewer than 25 percent of degree-seeking undergraduates live on campus (includes exclusively distance education institutions).

VS4/R: Very small four-year, primarily residential. Fall enrollment data show FTE enrollment of fewer than 1,000 degree-seeking students at these bachelor’s degree granting institutions. 25-49 percent of degree-seeking undergraduates live on campus.

VS4/HR: Very small four-year, highly residential. Fall enrollment data show FTE enrollment of fewer than 1,000 degree-seeking students at these bachelor’s degree granting institutions. At least half of degree-seeking undergraduates live on campus.

S4/NR: Small four-year, primarily nonresidential. Fall enrollment data show FTE enrollment of 1,000–2,999 degree-seeking students at these bachelor’s degree granting institutions. Fewer than 25 percent of degree-seeking undergraduates live on campus (includes exclusively distance education institutions).

S4/R: Small four-year, primarily residential. Fall enrollment data show FTE enrollment of 1,000–2,999 degree-seeking students at these bachelor’s degree granting institutions. 25-49 percent of degree-seeking undergraduates live on campus.

S4/HR: Small four-year, highly residential. Fall enrollment data show FTE enrollment of 1,000–2,999 degree-seeking students at these bachelor’s degree granting institutions. At least half of degree-seeking undergraduates live on campus.

M4/NR: Medium four-year, primarily nonresidential. Fall enrollment data show FTE enrollment of 3,000–9,999 degree-seeking students at these bachelor’s degree granting institutions. Fewer than 25 percent of degree-seeking undergraduates live on campus (includes exclusively distance education institutions).

M4/R: Medium four-year, primarily residential. Fall enrollment data show FTE enrollment of 3,000–9,999 degree-seeking students at these bachelor’s degree granting institutions. 25-49 percent of degree-seeking undergraduates live on campus.

M4/HR: Medium four-year, highly residential. Fall enrollment data show FTE enrollment of 3,000–9,999 degree-seeking students at these bachelor’s degree granting institutions. At least half of degree-seeking undergraduates live on campus.

L4/NR: Large four-year, primarily nonresidential. Fall enrollment data show FTE enrollment of at least 10,000 degree-seeking students at these bachelor’s degree granting institutions. Fewer than 25 percent of degree-seeking undergraduates live on campus (includes exclusively distance education institutions).

L4/R: Large four-year, primarily residential. Fall enrollment data show FTE enrollment of at least 10,000 degree-seeking students at these bachelor’s degree granting institutions. 25-49 percent of degree-seeking undergraduates live on campus.

L4/HR: Large four-year, highly residential. Fall enrollment data show FTE enrollment of at least 10,000 degree-seeking students at these bachelor’s degree granting institutions. At least half of degree-seeking undergraduates live on campus.

-FTE: Full-time equivalent enrollment was calculated as full-time plus one-third part-time.

-On campus is defined as institutionally-owned, -controlled, or -affiliated housing.

**Unduplicated Headcount**: Indicates how many individuals - all students (EFFYLEV=1,FYRACE24) the institution served over a 12-month period (the unduplicated headcount)
Unduplicated count - The sum of students enrolled for credit with each student counted only once during the reporting period, regardless of when the student enrolled.
Credit - Recognition of attendance or performance in an instructional activity (course or program) that can be applied by a recipient toward the requirements for a degree, diploma, certificate, or other formal award.

**FTE Staff**: The full-time-equivalent (FTE) of staff is calculated by summing the total number of full-time staff from the Employees by Assigned Position (EAP) component and adding one-third of the total number of part-time staff.

Specifically

SUM(of round(A1+B1/3),
round(A2+B2/3),
round(A3+B3/3),
round(A4+B4/3),
round(A5+B5/3),
round(A6+B6/3),
round(A7+B7/3))

Where

A1=Full-time, Primarily instruction total (EAPRECTP=2110)

B1=Part-time, Primarily instruction total (EAPRECTP=3110)

A2=Full-time, Instruction combined with research/public service (EAPRECTP=2120)

B2=Part-time, Instruction combined with research/public service (EAPRECTP=3120)

A3=Full-time, Primarily research total (EAPRECTP=2130)

B3=Part-time, Primarily research total (EAPRECTP=3130)

A4=Full-time, Primarily public service total (EAPRECTP=2140)

B4=Part-time, Primarily public service total (EAPRECTP=3140)

A5=Full-time, Executive/administrative and managerial (EAPRECTP=2150)

B5=Part-time, Executive/administrative and managerial (EAPRECTP=3150)

A6=Full-time, Other professional (EAPRECTP=2160)

B6=Part-time, Other professional (EAPRECTP=3160)

A7=Full-time, Non-professional (EAPRECTP=2170)

B7=Part-time, Non-professional (EAPRECTP=3170)