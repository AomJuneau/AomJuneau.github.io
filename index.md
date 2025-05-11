## Wanvimol "Aom" Juneau
I am a senior at Agnes Scott College, pursuing a degree in Biology. I am currently taking a class called "Data Intensive Ecology" where I learn how to code in R using large databases such as ones from [NEON](https://www.neonscience.org/) and [GBIF](https://www.gbif.org/).

### Contact Information
* ormjuneau@icloud.com
* juneau262@agnesscott.edu
* [LinkedIn](https://www.linkedin.com/in/aom-juneau-0bb2401a9/)

---

## Data Intensive Ecology Project
My project is called Investigating Phytoplankton Population Changes and Algae Bloom Occurrences with NEON Data. <br>


Algae blooms happen across bodies of water, fresh and marine alike. In this wide range of environments, they can significantly harm the various surrounding  organisms. Harmful algae blooms (HAB) cause oxygen depletion underneath the water surface, leading many aquatic organisms to suffocate. Understanding the causes of HABs will help conservation efforts of many aquatic species and lead to lower rates of oxygen depletion. <br>


NEON (National Ecological Observatory Network) is the main source of data used in this project. Specifically, the [“Periphyton, Seston, and Phytoplankton”](https://data.neonscience.org/data-products/DP1.20166.001) collection and the [“Temperature at Specific Depth in Surface Water”](https://data.neonscience.org/data-products/DP1.20264.001) database were used to match the phytoplankton growth and water surface temperature of their respective collection sites. <br>

#### Periphyton, Seston, and Phytoplankton Dataset <br>
This dataset has these important records:
* siteID
* collectDate
* sampleCode
* fieldSampleVolume
* labSampleVolume
* domainFilterVolume
* adjAshFreeDryMass
  <ol>
  This is an important record of organic matter collected from a set amount of the field sample volume

#### Temperature at Specific Depth in Surface Water <br>
This dataset has these important records:
* TSD_30_min
* siteID
* tsdWaterTempMean
* tsdWaterTempVariance


## MY HYPOTHESIS: Exposure to both constant and high heat has a positive correlation with increased levels of algae growth

### Data Wrangling
I used three NEON sites from August 2020-Dec 2023
* BARC
* SUGG
* TOMB

#### Wrangling the Temperature Data

To download the Temperature at Specific Depth in Surface Water Data, I used: <br>
<code>water_temp <- loadByProduct(dpID = "DP1.20264.001", site=c("BARC","SUGG","TOMB"), 
                            startdate = "2020-08", enddate = "2022-12",
                            token = Sys.getenv("NEON_TOKEN"),
                            check.size = F)'</code>

Extracted only the things I wanted by using: <br>
<code>Data_unfiltered  <- data.frame(
  dateTime = water_temp$TSD_30_min$startDateTime,
  tsdWaterTempMean = water_temp$TSD_30_min$tsdWaterTempMean,
  tsdWaterTempVariance = water_temp$TSD_30_min$tsdWaterTempVariance,
  siteID = water_temp$TSD_30_min$siteID </code>


then seperated and named by site using: <br>
* <code>BARC_data <- Data_unfiltered %>%
  filter(grepl("BARC", siteID, ignore.case = TRUE))</code>

* <code>SUGG_data <- Data_unfiltered %>%
  filter(grepl("SUGG", siteID, ignore.case = TRUE))</code>

* <code>TOMB_data <- Data_unfiltered %>%
  filter(grepl("TOMB", siteID, ignore.case = TRUE))</code>


I then made individual plots using R package "ggplot". I replced the [SITE] with each site name and [Mean/Variance] with either one depending on which plot I was aiming to make.
<code>ggplot([SITE]_data, aes(x=dateTime, y=tsdWaterTemp[Mean/Variance])) +
  geom_point(size=.2, color="forestgreen") +
  theme_classic() +
  ylab("Water Temperature [Mean/Variance]") +
  xlab("Date") +
  labs(title = "[SITE] Water Temperature Over Time")</code>

