# Applied IBM Data Science Capstone Project - The Battle of Neighborhoods (Week 2)

### Khanh Hung Nguyen, PhD candidate in Computer Science, University of Newcastle, Australia

## 1. Introduction 

I am an international student currently studying and living in Newcastle, New South Wales, Australia. I have been living in Newcastle with my wife and a four-year-old boy for more than a year. We plan to stay in Newcastle for at least three and four following years. What strikes me over the year is that which suburb in Newcastle that is worth to live in the near future. On the one hand, as a family first time living abroad together, we love to experience the Australian lifestyle in general and in the Newcastle area in specific. On the other hand, we have certain constraints such as financial capability and time raising our kid and provides him with a good education. 

In this capstone project, I will find suburbs in the City of Newcastle that meets certain criteria. I consider three specific criteria which are as follows: 
1. Living lifestyle in the suburb and nearby area where we can interact and enjoy the time while living in Australia. 
2. The price of the property for renting or buying (hopefully in the future) needs to be in an acceptable range which I can afford to pay. 
3. The primary school in the suburb provides good education so that I am confident to have my son go to school and know that he is in good hands. 

I believe the question where to live, and what criteria affecting the choice are crucial to every people who choose to move to a new country, region or area. In this project, I focus specifically on the City of Newcastle. I try to answer the question of which suburb I might consider living in the future based on my own criteria. In my opinion, these criteria are fundamentally basic and relevant factors that apply to almost anyone who relocates to a different place, especially those come together with a small family. The data I collected and analyzed and how I answered my questions are particularly beneficial for those who recently made their move or currently consider leaving for Newcastle. 

## 2. Data 

The list of the sources of data and short explanation are provided as follows: 
- List of suburbs in the Greater Newcastle region which including City of Newcastle, City of Lake Macquarie, City of Cessnock, City of Maitland and Port Stephens Council. In this project, we are only interested in the City of Newcastle with 51 suburbs. The details could be found in the link: https://en.wikipedia.org/wiki/List_of_suburbs_in_Greater_Newcastle,_New_South_Wales. 
- List of postcode, and coordinates of every suburbs in Australia. The data is actually in SQL syntax to create schema and data. The link is https://gist.github.com/randomecho/5020859. Basically, the data table contains five columns including: postcode, suburb name, state, latitude and longitude.
- Data indicate property price and how expensive to rent or buy a property in each suburb in City of Newcastle. The one I found most relevant was provided in the report: https://paigcrm.s3.amazonaws.com/2018/05/02/0d9101ce429fbd0c05c76f1d6d18db862bd57b4fe8b75b6930ebe0d88f927e18/Newcastle_Location_Report.pdf. The data contain 28 suburbs with attributes including sales houses, median houses, one-year growth, growth average and median yield. For this project we are interested in the median house price. The median house prices reflect the fact how expensive to rent or buy a property in the suburbs. 
- The shapefile data and relevant database of the whole state of New South Wales are downloaded in https://data.gov.au/dataset/ds-dga-91e70237-d9d1-4719-a82f-e71b811154c6/distribution/dist-dga-5e295412-357c-49a2-98d5-6caf099c2339/details?q=Newcastle%20suburb. 
- A list of primary schools in each suburb in the City of Newcastle. I found a website providing the list of primary school in each suburb as long as the suburb name and postcode are filled in the search box. The link of the website is http://bettereducation.com.au/school/Primary/nsw/nsw_primary_school_rating.aspx. The most important files include shp, dbf and prj file type, which are required to read shapefile format. 

## 3. Methodology

**Firstly**, I need to have a list of suburbs in the City of Newcastle, the corresponding postcode and coordinates (latitude and longitude). 
- I extracted the names of 51 suburbs in the area from the Wikipedia page. 
- Following the second link in the Data section, I downloaded the SQL file, imported to mySQL workbench then run the script to generate the data. The data was then exported to a CSV file to have easy access in the future. The name of each suburb in the City of Newcastle with the state of New South Wales was matched with the corresponding postcode by merging the two relevant dataframes. For the coordinates in the provided data, I tested and found the location of certain suburbs (including some in the City of Newcastle) are totally wrong and misleading, so I decided not to use the coordinates data in the second link.
- I generate the coordinates location of 51 suburbs in the City of Newcastle with the package geocoder.arcgis 
 
**Secondly**, I use the above data, including suburb name, postcode and coordinate to find surrounding venues of each suburb in the City of Newcastle. I took advantage of FourSquare data. I applied the k-mean clustering algorithm to achieve the group of suburbs, sharing similarities and further helps me to determine the living lifestyle of each group of suburbs. 

**Thirdly**, the data of property price only cover 28 out of 51 suburbs in the area. In order to have a complete indicator of how expensive it is to rent or buy a property in every suburb I need to interpolate the data of the missing values of the rest 23 suburbs. I made the assumption that the missing property price of each suburb is the average of the available price of the neighbouring suburbs. In order to identify the list of neighbours of each suburb, I take advantage of the package geopandas to read spatial data with Python. The neighbours of each suburbs are those who share the polygon borders and are then store in the relevant dataframe for further access. 

**Fourthly**, I need to extract the complete list of all primary schools in every suburb in the City of Newcastle. I currently have 51 suburbs. The whole manual process to fill in the search box with suburb name and then get the results list is long and might generate unnecessary errors, so I decided to automate the process. I took advantage of the package selenium in Python to create a driver of the Firefox browser that provides me with a way to automatically fill the search box with all suburbs and get the complete result without any manual work. 

## 4. Results

**Firstly**, I achieved the dataframe containing name of suburb, corresponding postcode, latitude and longitude of 51 suburbs in the City of Newcastle. The first five rows of the dataframe is shown in Figure 1. 

![Suburb](https://www.dropbox.com/s/g9by7k8m310vdty/1-Suburbs.png?raw=1 "Suburbs in Newcastle")

Figure 1. First five suburbs in the City of Newcastle. 

Based on the generated coordinate of each suburb and postcode, I plot the map of the City of Newcastle with each marker representing each postcode suburb in Figure 2. Please note that there are several suburbs assigned with the same postcode. 

![NewMap](https://www.dropbox.com/s/gus61m77kaneyix/2-NewcastleMap.png?raw=1 "Map of Newcastle") 

Figure 2. Map of City of Newcastle with suburbs. 

**Secondly**, I created a function to get all nearby venues based on the provided coordinate and the set distance, which is in this case 4 kilometers. The functions is passed to all suburbs and the corresponding location. Based on the results of the Foursquare inquiry data, the list of ten most common venues are listed for each suburb (shown in Figure 3). 

![TenVenues](https://www.dropbox.com/s/dae0u2wwngongkj/3-topTenVenues.png?raw=1 "Top 10 Venues") 

Figure 3. First five suburbs with top ten popular venues. 

The dataframe which contains venues of suburbs in the region was grouped by each suburb. This data was passed applied with k-means clustering algorithm. The number of clusters was set to 6. The map of City of Newcastle was then mapped again with 6 clusters of suburbs (shown in Figure 4). 

![Clusters](https://www.dropbox.com/s/wo50wxuyxkufqyi/4-Clusters.png?raw=1 "Clusters of Suburbs") 

Figure 4. Map of City of Newcastle with six clusters of suburbs.

Based on my own judgement and experience living in Newcastle, I am surprised by how good the results yielded by the algorithm. It also proves that data generated from FourSquare are adequate. The whole process helps me to narrow down the list of potential suburbs that meet my criteria. 

Cluster 2 and 5 include those suburbs which are far away from the city centre. There are not much dynamic activities in these areas. The most popular venues are train stations, fish & chips shop, fast food restaurants. 

Cluster 0 includes 8 suburbs and Cluster 4 includes 13 suburbs located in the north western part of the City of Newcastle. The areas are popular among students who are studying in the Callaghan campus at the University of Newcastle. The most popular venues in these areas are convenience stores, supermarkets, coffee shop and gas stations. I consider the suburbs in the two clusters are better than the two previous ones. 

The last two, cluster 1 and cluster 3, cover the suburbs which I prefer to relocate in the future. The suburbs have more dynamic lifestyle in which we as family can enjoy and further understand more local culture. The suburbs are close to beach, pool area. There are lots of park and playgrounds nearby for children. People living in these suburbs can also easily access to multiple facilities such as gym, grocery, supermarket, cafe, coffee shop and range of restaurants. In certain suburbs, multiplex for accommodation is also identify popular which give varying options to live with competitive prices. Suburbs of cluster 1 and 3 are then combined together with most 10 common venues are stored in a dataframe to further examine. 

**Thirdly**, the shapefile containing spatial data of the whole state of NSW was read into a GeoDataFrame format. All suburbs of City of Newcastle were chosen and drawn a quick plot in Figure 5. 

![NewBoundaries](https://www.dropbox.com/s/a4w1bopo6fi4vg6/5-NewBoundaries.png?raw=1 "Suburb Boundaries") 

Figure 5. Suburb-level map with boundaries in City of Newcastle.

The list of neighbors of each suburbs were determined by those sharing the same border. Please note each suburb was presented as a polygon forming by lines connected by points. Each point was stored in the shapefile format. The first 5 suburbs with points forming its polygon and the list of corresponding neighbours are presented in Figure 6. 

![Neighbours](https://www.dropbox.com/s/vblg5y5zu0lwal3/6-SuburbNeighbours.png?raw=1 "Suburb Neighbours") 

Figure 6. List of neighbours of the first five suburbs. 

With the collected data of property prices of 28 out of 51 suburbs in the City of Newcastle, I calculated the missing value based on the previously mentioned assumption. The most important factor is the list of neighbours which we have already identified. The data interpolation was done in a while loop until all the data was filled. Figure 7 shows the first five rows of the original data while Figure 8 shows the ones after the process of interpolation. 

![OriginalPrices](https://www.dropbox.com/s/lo939spldd6mi1y/7-OriginalPrice.png?raw=1 "Original Prices") 

Figure 7. First five rows of the original property prices.

![ProcessedPrices](https://www.dropbox.com/s/y3je1eiecgjjo89/8-ProcessedPrice.png?raw=1 "Processed Prices") 

Figure 8. First five rows of the property prices after being interpolated.






Based on all the data that I collected and analyzed, I can filter the data based on my certain criteria. The filtering procedure yields a list of potential suburbs that I prefer to relocate in the near future. I hope this work will help other determining which suburb in the City of Newcastle to move in.