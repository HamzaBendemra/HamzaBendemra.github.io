---
layout: post
title: "Using Unsupervised Learning to Plan a Vacation to Paris: Geo-location Clustering"
date: 2018-05-22 10:00:00 +0400
categories: [data-science, machine-learning]
tags: [machine-learning, clustering, unsupervised-learning, k-means, hdbscan, geolocation, travel, python]
original_url: "https://medium.com/data-science/using-unsupervised-learning-to-plan-a-paris-vacation-geo-location-clustering-d0337b4210de"
canonical_url: "https://medium.com/data-science/using-unsupervised-learning-to-plan-a-paris-vacation-geo-location-clustering-d0337b4210de"
description: "A practical application of K-Means and HDBSCAN clustering algorithms to optimize travel itinerary planning using geo-location data from Paris landmarks and attractions."
image: "/assets/images/posts/paris-clustering.jpg"
republished: true
---

> *Originally published on [Medium]({{ page.original_url }}) on {{ page.date | date: "%B %d, %Y" }}*

![Eiffel Tower Paris](https://unsplash.com/photos/Q0-fOL2nqZc/download?force=true&w=800)
*The Eiffel Tower in the City of Love but there are so many great sights in Paris — how can I help her organise her trip?*

When my friend told me she was planning a 10-day trip to Paris, I figured I could help.

> **Her**: "My friend and I are thinking of going to Paris on vacation."  
> **Me**: "Oh sounds fun. Maybe I can join in as well."  
> **Her**: "Yes! We're planning to enrol in a French language course, and do loads of shopping! We already listed all the shopping malls we want to visit, and …"  
> **Me**: "Ah sounds great… so what time do you need me to drive you to the airport?"

Since I've been to Paris a few times myself, I figured I could help in other ways like contributing to the list of sights and places to visit. After listing all those sights and attractions, I created a Google map with a pin for each location.

![Google Maps with pins](https://unsplash.com/photos/dC6Pb2JdAqs/download?force=true&w=800)
*The initial Google Map with pins for sights to visit — but in what order should these sights be visited?*

It became quite clear that some scheduling work would be needed to see all that Paris had to offer — but how can she decide what to see first, and in what order? This seemed to me like a [clustering](https://towardsdatascience.com/tagged/clustering) problem to me and [unsupervised learning](https://towardsdatascience.com/tagged/unsupervised-learning) method could help solve it.

Algorithms like [K-Means](http://scikit-learn.org/stable/modules/clustering.html#k-means), or [DBScan](http://scikit-learn.org/stable/modules/clustering.html#dbscan) would probably do the trick. But first, the data must prepared to facilitate for such algorithm to perform as intended.

## Geolocations Collection and Data Preparation

First, I had to gather the Google map pins geo-locations in a 2D format (something that could be stored in a [numpy array](https://docs.scipy.org/doc/numpy/reference/generated/numpy.array.html)). Translating those pins into [longitude, latitude] would be perfect.

Since this was a one-off, I looked for a quick way to extract this info from the Google map. This [StackOverflow query](https://stackoverflow.com/questions/2558016/how-to-extract-the-lat-lng-of-pins-in-google-maps) gave me all that I needed.

Basically, one needs to go to google.com/maps/d/kml?mid={map_id} and download a *.kmz file. Then, manually change the extension of the *.kmz file to a *.zip file. Extract the file, and open doc.kml in a text editor of your choice ([SublimeText](https://www.sublimetext.com/) is my personal go-to).

Then, you may decide to manually CTRL+F to search for `<coordinates>` fields, or decide to not be so lazy about it and use [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/) (as shown below)!

```python
from bs4 import BeautifulSoup

# Parse the KML file
with open('doc.kml', 'r') as f:
    soup = BeautifulSoup(f, 'xml')

# Extract coordinates
coordinates = []
for placemark in soup.find_all('Placemark'):
    coords = placemark.find('coordinates')
    if coords:
        coordinates.append(coords.text.strip())
```

Once I extracted the coordinates from the XML file, I stored the coordinates in a dataframe. The total number of landmarks is 26 (stored in `<Placemark></Placemark>` in the XML file).

![Data analysis visualization](https://unsplash.com/photos/JKUTrJ4vK00/download?force=true&w=800)
*Dataframe populated with info from the XML file (first 7 rows only shown)*

From the dataframe which stored coordinates and the name of the landmark/sight, I generated a scatter plot.

## K-Means Clustering

In general, unsupervised learning methods are useful for datasets without labels AND when we do not necessarily know the outcome we are trying the predict. These algorithms typically take one of two forms: (1) clustering algorithms, or (2) dimensionality reduction algorithms.

In this section, we'll focus on [k-means](https://en.wikipedia.org/wiki/K-means_clustering), which is a clustering algorithm. With k-means, a pre-determined number of clusters is provided as input and the algorithm generates the clusters within the un-labeled dataset.

k-means generates a set of k cluster centroids and a labeling of input array X that assigns each of the points in X to a unique cluster. The algorithm determines cluster centroids as the arithmetic mean of all the points belonging to the cluster, AND defines clusters such that each point in the dataset is closer to its own cluster center than to other cluster centers.

It can be implemented in Python using [sklearn](http://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html) as follows:

```python
from sklearn.cluster import KMeans
import numpy as np

# Prepare coordinates array
coordinates_array = np.array([[lat, lng] for lat, lng in zip(df['Latitude'], df['Longitude'])])

# Apply K-Means clustering
kmeans = KMeans(n_clusters=10, random_state=42)
cluster_labels = kmeans.fit_predict(coordinates_array)

# Add cluster labels to dataframe
df['Cluster'] = cluster_labels
```

As one can see in the scatter plot below, I generated 10 clusters — one for each vacation day. But the sights in the center of Paris are so close to each other, it is quite difficult to distinguish one cluster from the other. The resulting predictions were also sorted and stored in a dataframe.

Using the 10 clusters generated by k-means, I generated a dataframe that assigns a day of the week to each cluster. This would constitute an example of a schedule.

Now, at this stage, I could have simply handed over the sorted dataframe, re-organised the Google map [pins by layers](https://support.google.com/mymaps/answer/3024933?co=GENIE.Platform%3DDesktop&hl=en) (i.e. each layer representing one day), and that's it — itinerary complete.

But something was still bugging me, and that is that k-means was generating clusters based on the Euclidean distance between points — meaning the straight-line distance between two pins in the map.

But as we know, the Earth isn't flat ([right?](http://theconversation.com/how-to-reason-with-flat-earthers-it-may-not-help-though-95160)) so I was wondering if this approximation was affecting the clusters being generated, especially since we have quite a few landmarks far from the high-density region in the center of Paris.

## Because the Earth isn't flat: enter HDBSCAN

Hence, we need a clustering method that can handle [Geographical distances](https://en.wikipedia.org/wiki/Geographical_distance), meaning lengths of the shortest curve between two points along the surface of the Earth.

A density function such as [HDBSCAN](http://hdbscan.readthedocs.io/en/latest/index.html), which is based on the [DBScan](https://en.wikipedia.org/wiki/DBSCAN) algorithm, may be useful for this.

Both HDBSCAN and DBSCAN algorithms are density-based spatial clustering method that group together points that are close to each other based on a distance measurement and a minimum number of points. It also marks as outliers the points that are in low-density regions.

Thankfully, HDBSCAN supports [haversine distance](https://en.wikipedia.org/wiki/Haversine_formula) (i.e. longitude/latitude distances) which will properly compute distances between geo-locations. For more on HDBSCAN, check out this [blog post](https://towardsdatascience.com/lightning-talk-clustering-with-hdbscan-d47b83d1b03a).

HDBSCAN isn't included in your typical Python distribution so you'll have to pip or conda install it. I did so, and then ran the code below.

```python
import hdbscan

# Apply HDBSCAN clustering with haversine distance
clusterer = hdbscan.HDBSCAN(
    min_cluster_size=2,
    metric='haversine'
)

# Convert coordinates to radians for haversine distance
coordinates_rad = np.radians(coordinates_array)
cluster_labels = clusterer.fit_predict(coordinates_rad)

# Add cluster labels to dataframe
df['HDBSCAN_Cluster'] = cluster_labels
```

And ended up with the following scatter plot and dataframe. We see that isolated points were clustered in cluster '-1' which means they were identified as 'noise'.

Unsurprisingly, we end up with several points being flagged as noise. Since the minimum number of points for a HDBSCAN cluster is 2, isolated locations like the Palais de Versailles were categorised as noise. Sainte-Chapelle de Vincennes, and Musée Rodin suffered a similar fate.

The interesting part however is in the number of clusters that HDBSCAN identified which is 9 — one less than the set vacation days. I guess that for the number of sights/data points that we chose, 10 days sounds like it'll be okay.

Ultimately, the results from k-means were the ones we used to lay out a schedule as the clusters generated by k-means were similar to the ones generated by HDBSCAN and all data points were included.

## Conclusion and Future Improvements

The clustering method presented here can be improved of course. One possible improvement is in the addition of a weight feature for the datapoints. For instance, the weights could represent the amount of time needed to fully visit a particular venue (e.g. Le Louvre easily takes one full day to appreciate), and hence this would affect total number of data points in a cluster with highly weighted points — something to investigate in future projects.

**Jupyter Notebook for this mini-project can be [found here](https://github.com/hamzaben86/Vacation-Clustering-MiniProject).**

---

*If you found this article helpful, feel free to connect with me on [LinkedIn](https://linkedin.com/in/hamzabendemra){:target="_blank" rel="noopener"} or check out my other articles on [Medium](https://medium.com/@hamzabendemra){:target="_blank" rel="noopener"}.*
