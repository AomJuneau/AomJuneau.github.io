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


I then made individual plots using R package "ggplot". I replaced the [SITE] with each site name and [Mean/Variance] with either one depending on which plot I was aiming to make.
<code>ggplot([SITE]_data, aes(x=dateTime, y=tsdWaterTemp[Mean/Variance])) +
  geom_point(size=.2, color="forestgreen") +
  theme_classic() +
  ylab("Water Temperature [Mean/Variance]") +
  xlab("Date") +
  labs(title = "[SITE] Water Temperature Over Time")</code>

### **Mean Temperatures by Sites**
![Image](https://github.com/user-attachments/assets/cd3de1f3-d9e1-4b7a-b232-3b68af5d6bcd)

![Image](https://github.com/user-attachments/assets/2c4e37b4-3b56-4de3-abb9-04e6712cca4a)

![Image](https://github.com/user-attachments/assets/1402c019-f8c4-4728-b07c-bb3fa0475e09)

### **Variance of Temperatures by Sites**

![Image](https://github.com/user-attachments/assets/3566c2e0-c10d-4e6b-bacc-48508310a0d8)

![Image](https://github.com/user-attachments/assets/be035efc-e3c3-4c0e-a9db-89bc7ec06da0)

![Image](https://github.com/user-attachments/assets/85a65282-38e2-4a50-b5ca-21274bb93c6b)

I then made a plot with all the sites combined for... <br>

Water Temperature Mean
<code>TempAllSites <- bind_rows(BARC_data, SUGG_data, TOMB_data)</code>

<code>ggplot(TempAllSites, aes(x = dateTime, y = tsdWaterTempMean, color = siteID)) +
  geom_point(size = 0.1, alpha = 0.1) +  # alpha = transparency (0 = invisible, 1 = fully opaque)
  theme_classic() +
  xlab("Date") +
  ylab("Water Temperature Mean") +
  guides(color = guide_legend(override.aes = list(size = 6))) +
  labs(title = "Surface Water Temp Mean at BARC, SUGG, and TOMB")</code>

![Image](https://github.com/user-attachments/assets/34732da9-6bbd-4f5a-b2da-01bd1b1249a7)

Water Temperature Variance
<code>PhytoAllSites <- bind_rows(phyto_data_BARC, phyto_data_SUGG, phyto_data_TOMB)</code>

<code>ggplot(PhytoAllSites, aes(x = collectDate, y = adjAshFreeDryMass, color = siteID)) +
  geom_point(size = 1, alpha = 1) +
  theme_classic() +
  xlab("Date") +
  ylab("Ash Free Dry Mass Amounts") +
  labs(title = "Ash Free Dry Mass Amounts Over Time at BARC, SUGG, and TOMB")</code>

![Image](https://github.com/user-attachments/assets/94363639-c9c9-4b47-ac1b-51f097d820fb)

#### Wrangling the Algae Data

To download the Temperature at Specific Depth in Surface Water Data, I used: <br>
<code>alg <- loadByProduct(dpID = "DP1.20166.001", site=c("TOMB", "BARC", "SUGG", ), 
                     startdate = "2021-01", enddate="2022-12", 
                     token = Sys.getenv("NEON_TOKEN"),
                     check.size = F)</code>

Extracted only the things I wanted by using: <br>
<code>algbiomass <- alg$alg_biomass  #algae biomass</code>

<code>afdm <- alg$alg_biomass$adjAshFreeDryMass #algae ash free dry mass</code>

<code>AlgSampleID <- alg$alg_biomass$parentSampleID</code>


then seperated and named by site using: <br>
* <code>phyto_data_SUGG <- alg$alg_biomass %>%
  filter(grepl("PHYTOPLANKTON", parentSampleID, ignore.case = TRUE)) %>%
  filter(grepl("SUGG", siteID, ignore.case = TRUE))</code>

* <code>phyto_data_BARC <- alg$alg_biomass %>%
  filter(grepl("PHYTOPLANKTON", parentSampleID, ignore.case = TRUE)) %>%
  filter(grepl("BARC", siteID, ignore.case = TRUE))</code>

* <code>phyto_data_TOMB <- alg$alg_biomass %>%
  filter(grepl("PHYTOPLANKTON", parentSampleID, ignore.case = TRUE)) %>%
  filter(grepl("TOMB", siteID, ignore.case = TRUE))</code>


I then made individual plots using R package "ggplot". I replaced the [SITE] with each site name. <br>
<code>plot(
  phyto_data_[SITE]$collectDate, phyto_data_[SITE]$adjAshFreeDryMass,
  col = "darkgreen", type = "p",
  xlab = "Date", ylab = "Ash Free Dry Mass",
  main = paste0("[SITE] Ash Free Dry Mass Amounts by Time"),
  pch = 16, 
  cex = 1)</code>

### **Ash Free Dry Mass Amounts by Sites**

![Image](https://github.com/user-attachments/assets/939a7aaf-a9a9-412d-818e-aadfbb05c1ab)

![Image](https://github.com/user-attachments/assets/64e86da9-bcec-4e33-885a-ffbd37479e31)

![Image](https://github.com/user-attachments/assets/67fb8117-1ec6-45af-a3b8-dd809f2aec5f)

I then made a plot with all the sites combined <br>

<code>PhytoAllSites <- bind_rows(phyto_data_BARC, phyto_data_SUGG, phyto_data_TOMB)</code>

<code>ggplot(PhytoAllSites, aes(x = collectDate, y = adjAshFreeDryMass, color = siteID)) +
  geom_point(size = 1, alpha = 1) +
  theme_classic() +
  xlab("Date") +
  ylab("Ash Free Dry Mass Amounts") +
  labs(title = "Ash Free Dry Mass Amounts Over Time at BARC, SUGG, and TOMB")</code>

![Image](https://github.com/user-attachments/assets/4caf2641-825a-4f84-910d-0b27f2fea23b) <br>

Afterwards, I prepped the data for analysis:

1. Backdated the collectDate 6mo from AshFreeDryMass
2. Made range of dates (backDate - collectDate)
3. Made range of temps at those dates
4. Created means of temp means and temp variance
5. Plot, create linear model, summarized linear model


### Analysing the Data
I looked at the Ash Free Dry Mass Amounts to compare with the temperature means of the past 6 months of each ash free dry mass record

<code>LnModelTempMean <- lm(AFDM ~ tsdTempMean6, data = Algae_Final)</code>

Here is what I received: 
<code>Multiple R-squared:  0.006015,	Adjusted R-squared:  -0.0216 
F-statistic: 0.2178 on 1 and 36 DF,  p-value: 0.6435</code>


