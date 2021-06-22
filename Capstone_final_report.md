# Osaka venue analysis 

# Introduction

Osaka is second largest city in Japan and there are a lot of shops and restaurant, but good restaurants are always full of people.
On the other hand, data shows that 30% of new restaurant or cafe goes bankrupt within a year, and 50% within 3 years. The reason of the high bankrupt rate is various, but location is one of most important factor, for example, there are too many similar shops, there is no demand for specific shops for residential area and so on. 
Since I love to have coffee at nice, cozy cafe for a break during work, I'm interested in analyzing location data of osaka, given I would open coffee shop. In this study, I focus on four Osaka wards, Kita-ku,Fukushima-ku, Chuo-ku, and Miyakojima-ku because those are the central part of Osaka. I set the target as **area with less cafe** and **not residential but bussiness** oriented area.

Research question ; Where is the best place to open coffee shop in Osaka, Japan?


# Data source

To execute the idea, I need location data of Osaka. The main resource is Forsqure API, as it is the requirement of the course assignment, additionally I acquire data from `Jusho.jp` and `geocooder`. 

- **Forsqure API**  
As we learned it during this module, I used Forsqure location data via API.

- **Address**   
the list of Osaka neighborhoods are required. It is available in open data base. [http://jusyo.jp/csv/new.php]
This website provides all Japanese address with postal codes, and files are zipped for each prefecture.(Sorry, only Japanese are available!) I took only Osaka address using `wget` command. 

- **Geocoding**    
To get coordinates, I used [geocoorder](https://www.geocoding.jp/) API. It allows to get all latitude and longitude for given address.(Again, addresses are written in Japanese, sorry!)
This [reference](https://qiita.com/paulxll/items/7bc4a5b0529a8d784673) helped me to use the API.

In total, 148 neighborhood's name was obtained from jusyo.jp and all those coordinates could be obtained from geocooder. The representative points of each neighborhoods are shown in blue below.
![](pic/osaka_neighborhoods.png)


Using Forsqure API, total 4257 venues are found.
I found that in forsquare database, many venues are assigned to near neighborhoods. The duplicated venues are close from all assigned nighbors. It is convenient for characterize heighborhoods, but make it difficult to make each neighborhoods statistics.  There was no resource to get unique neighborhoods for all venues, therefore I remove duplicated venues for some statistics, and use whole data set for machine learning. 

# Exploratory data analysis
## Most common categories

All venues are categorized like 'restaurant' in forsqure data. This `Venue Category` is most important for analysis as the venue name itself doesn't tell much. In Osaka data set, there are 199 unique categories. The below graph shows what kind of categories are common. 
![](pic/mostcommoncat.png)
Most common category is convenience store, as expected because Japan is kind of famous for 'convenience', which is open 7/24 and sell everything we need in daily life, like food, snacks, magazine, toiletry and so on.  The second one is a little surprise to me because 'Japanese Curry Restaurant' is not traditional style compared with 'Ramen' or 'Takoyaki' as Japanese 'fast food'. But surely there are a lot in Osaka, this record should be true. From next section, I examine some most common categories to characterize area.



## Convenience store
There are no record about office or company building in forsquere, and it is no direct measure to find business area. One hint to estimate business area would be convenience stores density, because the more convenience store, the more office and entertainment places normally.
Top 10 Neighborhoods which has largest number of convenience stores are shown below. For this purpose, I use not 'unique' venues, because one neighborhood has some convenience store in enough close distance.

|Neighborhood|N of convenience store|
|----|----:|
|大阪市都島区都島北通 | 12|
|大阪市都島区都島本通  |  10|
|大阪市都島区中野町     |10|
|大阪市北区本庄東       |9|
|大阪市北区大淀南       |9|
|大阪市福島区玉川       |9|
|大阪市北区天満橋       |9|
|大阪市都島区善源寺町   |  8|
|大阪市北区大淀中       |8|
|大阪市北区長柄西       |8|

On average, there are more than 4 convenience stores in each neighborhood. I would say it is a lot considering each neighborhood's are quite small in area. Let's check the highest convenience store neighborhood,'大阪市都島区都島本通', where have 10 stores.
![](pic/conveni.png)

Folium has already a lot of location data (actually more informative than foresqure in this case) We can see that there are a large central hospital, restaurants, clinics and many small parking lots in this area. I assume that this is not entertainment area but there are many people coming and going.

## Restaurant
Restaurants are also important indicator of residential or business area. There are so many restaurant related category in forsqure.

    -'Tonkatsu Restaurant'
    -'Nabe Restaurant'
    -'Italian Restaurant'
    -'Ramen Restaurant'
    -'Japanese Restaurant'
    -'Chinese Restaurant'
    -'Asian Restaurant'
    -'Restaurant'
    -'Shabu-Shabu Restaurant'
    -'Sushi Restaurant'
    -'Japanese Curry Restaurant'
    -'Fast Food Restaurant'
    -'Donburi Restaurant'
    -'Japanese Family Restaurant'
    -'Udon Restaurant'
    -'Kaiseki Restaurant'
    -'Thai Restaurant'
    -'Indian Restaurant'
    -'Yakitori Restaurant'
    -'Spanish Restaurant'
    -'Korean Restaurant'
    -'Comfort Food Restaurant'
    -'Teishoku Restaurant'
    -'Seafood Restaurant'
    -'Soba Restaurant'
    -'Tempura Restaurant'
    -'Tapas Restaurant'
    -'Unagi Restaurant'
    -'Okonomiyaki Restaurant'
    -'Yoshoku Restaurant'
    -'Kushikatsu Restaurant'
    -'Mexican Restaurant'
    -'Brazilian Restaurant'
    -'Russian Restaurant'
    -'French Restaurant'
    -'Malay Restaurant'
    -'Sri Lankan Restaurant'
    -'Vietnamese Restaurant'
    -'American Restaurant'
    -'Dumpling Restaurant'
    -'South Indian Restaurant'
    -'Himalayan Restaurant'
    -'Taiwanese Restaurant'
    -'Vegetarian / Vegan Restaurant'
    -'Hotpot Restaurant'
    -'Halal Restaurant'
    -'Dim Sum Restaurant'
    -'German Restaurant'
    -'Mediterranean Restaurant'
       
 Detail labeling above is almost no information, for example, there are 'Kushikatsu restaurant' and 'Yakitori restaurant', both are typical Japanese restaurant serving food and alcohol. Therefore, I decided to reduce number of restaurant category into 2. One is 'Izakaya', where mainly open dinner time and alcohol and people enjoy time with colleagues or friends over food and alcohol. The other is 'Fastfood' style, like hamburger or ramen, where people don't stay long time but just eat for lunch break or after work. Apparently this is not perfect categorization but should be more informative than as it is.
 
After re-label category, there are 337 'Izakaya' style restaurants, which is almost double of convenience store's number. I assumed that the business area has more convenience stores and more fast food. I simply check the trend of restaurant vs convenience store's number below.

![](pic/conveni_vs_fastfood.png)  

It is not fit to straight line, but seems the more convenience store and the fast food shop, and both high number area would be our target.

## Coffee shops
Most important information is the number of coffee shops around neighborhood as we look for less competitive area for it. The category 'Cafe' and 'Coffee shop' are actually same meaning in this data set. And like as restaurant, there are too many sweets shop category, I replace them as 'Dessert shop'. In Osaka, there are 122 Cafes in total and the below shows the most crowded neighborhoods with number of cafes.

|Neighborhood| count|
|----|----:|
|大阪市北区菅原町  |   8|
|大阪市北区天神西町 |   8|
|大阪市中央区北浜   |  8|
|大阪市中央区今橋   |  7|
|大阪市北区中崎     | 7|
|大阪市北区浮田     | 7|
|大阪市北区西天満   |  6|
|大阪市中央区博労町 |   6|
|大阪市中央区備後町 |   6|
|大阪市北区茶屋町   |  6|
|大阪市北区鶴野町   |  6|

This is the map which has most cafes.
![](pic/cafes.png)

It is river side place with green. That would be the reason of a lot of cafes. We have to avoid this neighborhood as high competitive, and the others high cafe density areas as well.


# Clustering
There are many other categories to characterize area, now we perform clustering using machine learning method, K-means, as it is effective for large data set and very intuitive. To execute clustering data needs to be cleaned and transformed.

## Data cleaning
There are a lot of small venue categories. Like we did in restaurant section, some categories are merged to make it more informative.

 - **Bars** : Any kind of bar, e.g. 'Sake bar' or 'Wine bar' is merged into 'Bar'
 - **Museums** : Art / science / History museums are merged.
 - **Sports facility** : There are lots of sports related big facility like tennis court. We can merge them.

Moreover, there are too many inconsistent names, like 'Closing store' and 'Boutique', which are same meaning. Those categories are merged together.

There are some category which should be a lot of places in osaka area, but only few of them are recorded, e.g. intersections and bus stops. I decided to drop them shown below.

|Venue Category|count|
|---|---:|
|ATM             |      3|
|Bridge          |      1|
|Bus Station     |      1|
|Bus Stop        |     14|
|Intersection    |     14|
|Mobile Phone Shop|     1|
|Platform        |      4|
|Toll Plaza      |      1|


Lastly, I omitted unknown/not relevant categories. The number of venue category became 69, this would better to characterize each location.


## K-mean clustering

Using above cleaned data, I transform it into wide format with columns of venue category and rows of each neighborhoods with the method of one-hot encoding. I set cluster number as 5, which would be easy size to interpretative. 

The first step of k-mean is to choose the initial centroids, which is randomly selected from neighborhood in this case. After initialization, K-means consists of looping between the two other steps. The first step assigns each sample to its nearest centroid. The second step creates new centroids by taking the mean value of all of the samples assigned to each previous centroid. The difference between the old and the new centroids are computed and the algorithm repeats these last two steps until this value is less than a threshold. In other words, it repeats until the centroids do not move significantly.

The below is result of K-mean clustering, each cluster pointed as different colors.

![](pic/clustering.png)

I explore each cluster in detail, and here is the summary of assumption.

**Cluster 0 (orange)**

- Mainly located along 'Tenjin-bashi' arcade, which is famous for longest arcade in japan with lots of Izakaya style restaurant
- Most of the neighborhoods have fastfood as most common venue.


**Cluster 1 (purple)**

- relatively outside of central Osaka.  
- It seems more residential area due to gym and supermarket.

**Cluster 2 (blue)**
- Surrounding Osaka station (the central station)
- a lot of entertainment places, like theaters and shopping malls.

**Cluster 3 (pink)**
- Only category which has less fastfood and Izakaya.
- It seems touristic place due to monuments, historic site, garden and museums.

**Cluster 4 (light green)**
- Middle part of Osaka area..
- Many cafe, Izakaya and fastfood. followed by Hotels. This are seems most business oriented.

# Result and Discussion

From the result of clustering, I assume that cluster 4 area is most business oriented and where we are targeting to open cafe. This area contains 37 neighborhoods. Among them, we want to pick up neighborhoods which has less cafe.

|Neighborhood|Number of Cafe|
|---|---:|
|北区芝田,中央区西心斎橋,中央区宗右衛門町,中央区心斎橋筋 ,中央区東心斎橋  |   2|
|中央区石町,北区中之島,中央区北久宝寺町 ,北区万歳町 ,中央区高麗橋,中央区高津 ,中央区道頓堀,中央区道修町,中央区本町橋  |    3|
|北区天満,中央区船場中央 ,中央区久太郎町 ,中央区安土町 ,中央区天満橋京町,中央区伏見町,中央区南本町 ,央区南久宝寺町 ,中央区東高麗橋    | 4|
|中央区淡路町,中央区南船場,北区大深町,北区中崎西 |      5|
|北区茶屋町,中央区備後町,中央区博労町,北区西天満,北区鶴野町  |     6|
|北区浮田 ,中央区今橋 |      7|
|北区天神西町 ,北区菅原町 ,中央区北浜  |     8|

From above neighborhoods list, '北区芝田','中央区西心斎橋','中央区宗右衛門町','中央区心斎橋筋', and '中央区東心斎橋', have only 2 cafes each. That would be our final target.

# Conclusion
In this study, I analyzed Osaka location data to find the best place to open cafe. I used forsqure API and geocord data to get all required information, and explore Osaka venues. The target character of area was business area with less competitive (less cafe). First I examine whole data set and cleaned data to make the best use of it. Then I used K-mean clustering, because it is very useful to categorize large number of data points, in this case I categorize 177 neighborhoods into 5 group. Finally I select one category which is more business oriented, and decide 5 neighborhoods which simply have less cafe.

​
