# **Moja Global Land Sector Data Analysis Guide**

# **1.0 Introduction**

The aim of this document is to guide contributors on how to report the analysis of [Moja Global's Land Sector Dataset](https://datasets.mojaglobal.workers.dev/0:/). The dataset is a collection of publicly available [Opensource datasets](https://github.com/moja-global/Land_Sector_Datasets) which you can chose any and work on. 
>These datasets have been used by many countries and are effective in real-world scenarios. For each dataset information is provided on content and license. Proper permissions have been cited for all the datasets and we urge you to follow the license for every dataset before proceeding to work with it.

If you have another dataset on your mind that would be beneficial to [Moja Global's mission](https://moja.global/), please feel free to reach out to us on [info@moja.global](info@moja.global) to share details about this dataset.


Concerning the data analysis software or programming language for the analysis, we encourage you to pick a softwate that supports geospatial analysis since you will be dealing with polygon files. 
Moja Global is an open source project, the interest here is to share your processes(**code**) and results(**interpretations**) to support the open source movement. In order to efficiently doucment your analysis, we advice you use [notebooks](https://sourceforge.net/software/product/Google-Colab/alternatives). Notebooks are documents produced by the data analysis software, which contain both computer code (e.g. python) and rich text elements (paragraph, equations, figures, links, etc…). 

Notebook documents are both human-readable documents containing the analysis description and the results (figures, tables, etc..) as well as executable documents which can be run to perform data analysis. 

It is important to note that when you have your contribution ready, you would have to make PR to have it merged. Please refer to the [community website](https://github.com/moja-global/About_moja_global/blob/master/Contributing/How-to-Contribute-Code.md) to know how to make a pull request.

---



If you have experience and want to help, please send an email to [mentorship@moja.global](mailto:mentorship@moja.global). We are open to mentoring new developers and contributors through various mentorship program and initiatives.




With this in mind, we will be expecting different notebooks which is good since it would be at your convinience and you can reveal insight which has not been previously uncovered.


When we look at climate action, we would like to get descriptions from a global scale to a specific state administration wthin a particular country. A draft review of the characteristics of each file in the dataset is found [here](https://colab.research.google.com/github/Simpleshell3/Land-Sector-Dataset-Description/blob/main/LandSectorDatasetDescription101.ipynb).


Amongst the numerous factors that affecct climate change, the Agriculture, Forestry and Other Land Use (AFOLU) sector is responsible for almost a quarter of the global Greenhouse gases (GHG) emissions. The emissions associated with AFOLU activities are projected to increase in the future.


The following sections of this guide will direct you on how to document your analysis at any scale(global, continental, country, state).

[**1.1 Global Analysis**]

[**1.2 Continental Analysis**]

[**1.3 National Analysis**]

[**1.4 State/Province/County/Region/City Analysis**]


# **1.1 Global Analysis**
This is the study of the entire globe's features, activities and consequences on climate change. Some factors that should guide your analysis include the following. 



---



# 1.1.0  The Globe's Boundary
In the first section of your global analysis notebook, it is suitable to;
1. State the aim of the analysis. 
2. Define the [boundary](https://datasets.mojaglobal.workers.dev/0:/Administrative/Boundaries/) of the globe with a figure and brief topological description

3. Present a figure with the world's [protected areas](https://datasets.mojaglobal.workers.dev/0:/Administrative/Protected%20Areas/) and give a description of how it has affected global changes.

4. Present a figure showing the complex road network of the world

5. Provide a brief summary of how the earth's  celestrial location results in climate change.


---



# 1.1.1 The Earth's BioClimatic Zones
Explore the earth's [bioclimatic zone dataset](https://datasets.mojaglobal.workers.dev/0:/Bioclimatic&EcologicalZones/). Fill in this section by trying to answer the follwing questions.
1. What are the hot zones of the earth.(provide a figure with the biodiversity hotspots)?
2. Which continents/countries/states are dorminant/repressive with hotspots?
3. What life is characteristic of the hotspots?
4. What life is going extinct on earth with the given data?

The files contain more features that should be explited.


---

# 1.1.2 The Earth's AgroEcological Zones
Explore the AgroEcological climate of the earth and reveal information about the following:

1. The agro hotspots
2. Dominant soil and related features
3. Restricted areas from cultivation
4. Nutrient availaibilty
5. water sccarcity.
6. Types of protected Areas

The files contain more features to be exploited


---

# 1.1.3 The Earth's Ecological Zones

Explore the ecological zone [dataset](https://datasets.mojaglobal.workers.dev/0:/Bioclimatic&EcologicalZones/Global_Ecological_Zone_GEZ/) by displaying it revealing interesting patterns.



---

# 1.1.4 The Earth's Climate

Explore the earth's climate [dataset](https://datasets.mojaglobal.workers.dev/0:/Climate/) from KoppenGeiger and IPCC. It would be suitable to give brief figurative desscriptions of the climate shifts.


---

# 1.1.5 The Earth's Land Cover
The Land cover dataset contains interesting information on Biomass carbon,
Forest, Forests loss, and Land use.

Explore this dataset to find relationships between any activitiees such as cropland and forest lost etc. 



---



# 1.1.6 The Earth's Soil Resources

Soil composition is an important factor that affects settlement and human activities. Display the data, identify relationships between attributes such as locations and crop production. nutrient availability and intensive agriculture etc



---

# 1.1.7 Interpretation of Results

This is the most important part of the analysis. The whole process comes down to this very aspect of drawing insight from the data. Being able to explain complex relationships that exist betwween **1.1.1** and **1.1.6**. 

In this section, you can statiscally elaborate on how the individual features of each factor above affects climate change. Present your opinion scientifically and use your calculations, refences and description to back up your facts.


# 1.1.8 Recommendation and Conclusion

Use this section as an opportunity to present possible recommendations on how managing the evaluated factors would benefit earth and help control, mitigate and adapt to climate changes.


# **1.2 Continental Analysis**

If you are interested in analyzing a given continent, then this is the right guide for you. The dataset available in the repository can be manupulated(joined, merged, concatenated etc) to narrow down to any continent.

Moreover, there are files that can be queried to display information only for a defined area. Use the follwing characteristics as a guide to analze a continent.

# 1.2.0  The Continent's Boundary
In the first section of your continental analysis notebook, it is suitable to;
1. State the aim of the analysis. 
2. Define the [boundary](https://datasets.mojaglobal.workers.dev/0:/Administrative/Boundaries/) of the  with continent a figure and brief topological description

3. Present a figure with the continents's [protected areas](https://datasets.mojaglobal.workers.dev/0:/Administrative/Protected%20Areas/) and give a description of how it has affected continental shift in climate and activities.

4. Present a figure showing the complex road network of the continent.

5. Provide a brief summary of how the continent's geographical location results in climate shifts within the area.


---



# 1.2.1 The Continent's BioClimatic Zones
Explore the continents's [bioclimatic zone dataset](https://datasets.mojaglobal.workers.dev/0:/Bioclimatic&EcologicalZones/). If the dataset contains other continental inforamtion, filter out the continents you are not analyzing.
Fill in this section by trying to answer the follwing questions.
1. What are the hot zones of the continent.(provide a figure with the biodiversity hotspots)?
2. Which countries/states are dorminant/repressive with hotspots?
3. What life is characteristic of the hotspots?
4. What life is going extinct on the continent with the given data?
5. What is the population of the continent?

The files contain more features that should be explited. Identify more characteristics that you find interesting


---

# 1.2.2 The Continent's AgroEcological Zones
Explore the AgroEcological climate of the continent and reveal information about the following:

1. The agro hotspots of the continent
2. Dominant soil and related features within the continent
3. Restricted areas from cultivation
4. Nutrient availaibilty
5. water sccarcity.
6. Types of protected Areas

The files contain more features to be exploited


---

# 1.2.3 The Continent's Ecological Zones

Explore the ecological zone [dataset](https://datasets.mojaglobal.workers.dev/0:/Bioclimatic&EcologicalZones/Global_Ecological_Zone_GEZ/) by displaying it revealing interesting patterns.



---

# 1.2.4 The Continent's Climate

Explore the continents's climate [dataset](https://datasets.mojaglobal.workers.dev/0:/Climate/) from KoppenGeiger and IPCC. It would be suitable to give brief figurative desscriptions of the climate shifts of the continent. What are the key characteristics of the climate and how does it vary over place and time


---

# 1.2.5 The Continent's Land Cover
The Land cover dataset contains interesting information on Biomass carbon,
Forest, Forests loss, and Land use.

Explore this dataset to find relationships between any activitiees such as cropland and forest lost etc. 

In the case where you can't find complete one-file datasets, try merging or filtering some the dataset to get the data of interest.



---



# 1.2.6 The Continent's Soil Resources

Soil composition is an important factor that affects settlement and human activities. Display the data, identify relationships between attributes such as locations and crop production. nutrient availability and intensive agriculture etc



---

# 1.2.7 Interpretation of Results

This is the most important part of the analysis. The whole process comes down to this very aspect of drawing insight from the data. Being able to explain complex relationships that exist betwween **1.2.1** and **1.2.6**. 

In this section, you can statiscally elaborate on how the individual features of each factor above affects climate change. Present your opinion scientifically and use your calculations, refences and description to back up your facts.


# 1.2.8 Recommendation and Conclusion

Use this section as an opportunity to present possible recommendations on how managing the evaluated factors would benefit the people and help control, mitigate and adapt to climate changes.

# **1.3 National Analysis**

If you are interested in analyzing a given country, then this is the right guide for you. The dataset available in the repository can be manupulated(joined, merged, concatenated etc) to narrow down to any continent.

Moreover, there are files that can be queried to display information only for a defined area. Use the follwing characteristics as a guide to analze a given country.

# 1.3.0  The Country's Boundary
In the first section of your national analysis notebook, it is suitable to;
1. State the aim of the analysis. 
2. Define the [boundary](https://datasets.mojaglobal.workers.dev/0:/Administrative/Boundaries/) of the country with a figure and brief topological description

3. Present a figure with the continents's [protected areas](https://datasets.mojaglobal.workers.dev/0:/Administrative/Protected%20Areas/) and give a description of how it has affected the climate's shift in climate and activities.

4. Present a figure showing the complex road network of the country.

5. Provide a brief summary of how the country's geographical location results in climate shifts within the area.


---



# 1.3.1 The Country's BioClimatic Zones
Explore the country's [bioclimatic zone dataset](https://datasets.mojaglobal.workers.dev/0:/Bioclimatic&EcologicalZones/). If the dataset contains other national inforamtion, filter out the countries you are not analyzing.
Fill in this section by trying to answer the follwing questions.
1. What are the hot zones of the country.(provide a figure with the biodiversity hotspots)?
2. Which states are dorminant/repressive with hotspots?
3. What life is characteristic of the hotspots?
4. What life is going extinct on the countries with the given data?
5. What is the population of the country

The files contain more features that should be explited. Identify more characteristics that you find interesting


---

# 1.3.2 The Country's AgroEcological Zones
Explore the AgroEcological climate of the country and reveal information about the following:

1. The agro hotspots of the country
2. Dominant soil and related features within the country
3. Restricted areas from cultivation
4. Nutrient availaibilty
5. water sccarcity.
6. Types of protected Areas

The files contain more features to be exploited


---

# 1.3.3 The Country's Ecological Zones

Explore the ecological zone [dataset](https://datasets.mojaglobal.workers.dev/0:/Bioclimatic&EcologicalZones/Global_Ecological_Zone_GEZ/) by displaying it revealing interesting patterns.



---

# 1.3.4 The Country's Climate

Explore the countrie's climate [dataset](https://datasets.mojaglobal.workers.dev/0:/Climate/) from KoppenGeiger and IPCC. It would be suitable to give brief figurative desscriptions of the climate shifts of the country. What are the key characteristics of the climate and how does it vary over place and time


---

# 1.3.5 The Country's Land Cover
The Land cover dataset contains interesting information on Biomass carbon,
Forest, Forests loss, and Land use.

Explore this dataset to find relationships between any activities such as cropland and forest lost etc. 

In the case where you can't find complete one-file datasets, try merging or filtering some the dataset to get the data of interest.



---



# 1.3.6 The Country's Soil Resources

Soil composition is an important factor that affects settlement and human activities. Display the data, identify relationships between attributes such as locations and crop production. nutrient availability and intensive agriculture etc



---

# 1.3.7 Interpretation of Results

This is the most important part of the analysis. The whole process comes down to this very aspect of drawing insight from the data. Being able to explain complex relationships that exist betwween **1.3.1** and **1.3.6**. 

In this section, you can statiscally elaborate on how the individual features of each factor above affects climate change. Present your opinion scientifically and use your calculations, refences and description to back up your facts.


# 1.3.8 Recommendation and Conclusion

Use this section as an opportunity to present possible recommendations on how managing the evaluated factors would benefit the people and help control, mitigate and adapt to climate changes.

# **1.4  State/Province/County/Region/City Analysis**

If you are interested in analyzing a remote geographical area such as a city, state or province , then this is the right guide for you. The dataset available in the repository can be manupulated(joined, merged, concatenated etc) to narrow down to any continent.
.

# 1.3.0  The State's Boundary
In the first section of your state's analysis notebook, it is suitable to;
1. State the aim of the analysis. 
2. Define the [boundary](https://datasets.mojaglobal.workers.dev/0:/Administrative/Boundaries/) of the state with a figure and brief topological description

3. Present a figure with the state's [protected areas](https://datasets.mojaglobal.workers.dev/0:/Administrative/Protected%20Areas/) and give a description of how it has affected the climate's shift in climate and activities.

4. Present a figure showing the road network of the state.

5. Provide a brief summary of how the state's geographical location results in climate shifts within the area.


---



# 1.3.1 The State's BioClimatic Zones
Explore the state's [bioclimatic zone dataset](https://datasets.mojaglobal.workers.dev/0:/Bioclimatic&EcologicalZones/). If the dataset contains other national inforamtion, filter out the regions you are not analyzing.

Fill in this section by trying to answer the follwing questions.
1. What are the hot zones of the state.(provide a figure with the biodiversity hotspots)?
2. Which areas are dorminant/repressive with hotspots?
3. What life is characteristic of the hotspots?
4. What life is going extinct on the state with the given data?
5. What is the population of the state

The files contain more features that should be exploited. Identify more characteristics that you find interesting


---

# 1.3.2 The State's AgroEcological Zones
Explore the AgroEcological climate of the state and reveal information about the following:

1. The agro hotspots of the state
2. Dominant soil and related features within the state
3. Restricted areas from cultivation
4. Nutrient availaibilty
5. water sccarcity.
6. Types of protected Areas

The files contain more features to be exploited


---

# 1.3.3 The State's Ecological Zones

Explore the ecological zone [dataset](https://datasets.mojaglobal.workers.dev/0:/Bioclimatic&EcologicalZones/Global_Ecological_Zone_GEZ/) by displaying it revealing interesting patterns.



---

# 1.3.4 The State's Climate

Explore the state's climate [dataset](https://datasets.mojaglobal.workers.dev/0:/Climate/) from KoppenGeiger and IPCC. It would be suitable to give brief figurative desscriptions of the climate shifts of the state. What are the key characteristics of the climate and how does it vary over place and time


---

# 1.3.5 The State's Land Cover
The Land cover dataset contains interesting information on Biomass carbon,
Forest, Forests loss, and Land use.

Explore this dataset to find relationships between any activities such as cropland and forest lost etc. 

In the case where you can't find complete one-file datasets, try merging or filtering some the dataset to get the data of interest.



---



# 1.3.6 The State's Soil Resources

Soil composition is an important factor that affects settlement and human activities. Display the data, identify relationships between attributes such as locations and crop production. nutrient availability and intensive agriculture etc



---

# 1.3.7 Interpretation of Results

This is the most important part of the analysis. The whole process comes down to this very aspect of drawing insight from the data. Being able to explain complex relationships that exist betwween **1.3.1** and **1.3.6**. 

In this section, you can statistically elaborate on how the individual features of each factor above affects climate change. Present your opinion scientifically and use your calculations, refences and description to back up your facts.


# 1.3.8 Recommendation and Conclusion

Use this section as an opportunity to present possible recommendations on how managing the evaluated factors would benefit the people and help control, mitigate and adapt to climate changes.
