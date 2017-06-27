# Lab 3: Interacting with users and services in the Cloud

* [Task 3.1: How to provide your services through an API?](#Tasks31)
* [Task 3.2: How to provide our service combined with third-party services?](#Tasks32)  
* [Task 3.3: Student proposal](#Tasks33)  
* [Task 3.4: Using Advanced Analytics as a Service in the Cloud](#Tasks34)  
   
<a name="Tasks31"/>

#  Tasks of Lab 3
<a name="Tasks31"/>

## Task 3.1:  How to provide your services through an API?
As we show, we can plots in Python using libraries like matplotlib.  However, how to provide our results through an API to consumers?
If you want to use python to build a prototype server (of your service) and want to provide web-base API with minimal efforts, you can just use python `SimpleHTTPServer` and `SocketServer` packages. 

Create the python file `OurService.py`:

```python
#!/usr/bin/env python
import SimpleHTTPServer
import SocketServer

class MyRequestHandler(SimpleHTTPServer.SimpleHTTPRequestHandler):
    def do_GET(self):
        if self.path == '/':
            self.path = '/OurService.html'
        return SimpleHTTPServer.SimpleHTTPRequestHandler.do_GET(self)

Handler = MyRequestHandler
server = SocketServer.TCPServer(('0.0.0.0', 8954), Handler)

print 'Started my REST API on port 8954' 
server.serve_forever()

```
and lauch it as:

```
torres@vm:~$  python OurService.py
```
Now you can open your browser at http://localhost:8954 and obtain acces to `OurService.html`.

The question now is how we can generate our service `OurService.html` in order to be provided by the server. Imagine that we want to provide the plot obtained in the previous Lab. We have different options, however I suggest to use [D3.js](https://d3js.org). 

Because D3 plays well with web standards like CSS and SVG, and allows to create some wonderful interactive visualisations in the web, many of my data science colleagues think that it is one of the coolest libraries for data visualisation.

[D3.js](https://d3js.org) is, as the name suggests, based on Javascript. We will present a simple option to support data D3 visualisation with Python using the Python library [Vincent](https://github.com/wrobstory/vincent), that bridges the gap between a Python back-end and a front-end that supports D3.js visualisation. Vincent takes our data in Python format and translates them into [Vega](https://github.com/trifacta/vega), a JSON-based visualisation grammar that will be used on top of D3.

Firstly, we need to install Vincent:

```
torres@vm:~$  sudo pip install vincent
```

Using the list of most frequent hashtags from the case study data set we used in the previous Lab, we want to plot their frequencies:


```
import vincent
 
word_freq = count_all.most_common(15)
labels, freq = zip(*word_freq)
data = {'data': freq, 'x': labels}
bar = vincent.Bar(data, iter_idx='x')
bar.to_json('term_freq.json')

```
At this point, the file `term_freq.json` will contain a description of the plot that can be handed over to D3.js and Vega. A simple template (taken from [Vincent resources](https://github.com/wrobstory/vincent)) to visualise the plot:

```html
<html>  
<head>    
    <title>Vega Scaffold</title>
    <script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
    <script src="http://d3js.org/topojson.v1.min.js"></script>
    <script src="http://d3js.org/d3.geo.projection.v0.min.js" charset="utf-8"></script>
    <script src="http://trifacta.github.com/vega/vega.js"></script>
</head>
<body>
    <div id="vis"></div>
</body>
<script type="text/javascript">
// parse a spec and create a visualization view
function parse(spec) {
  vg.parse.spec(spec, function(chart) { chart({el:"#vis"}).update(); });
}
parse("term_freq.json");
</script>
</html>
```
Save the above HTML page as `OurService.html`.
Now you can open your browser at http://localhost:8954 and obtain acces to `OurService.html`.

With this procedure, we can plot many different types of charts with `Vincent`. [Explore yourself](http://vincent.readthedocs.io/en/latest/). If you are interested in building a real server, there are many good Python frameworks for building a RESTful API as [Flask](http://flask.pocoo.org/), [Falcon](http://falconframework.org/) and [Bottle](http://bottlepy.org/docs/dev/index.html).

However, if you need to deploy your service in the Cloud, [AWS Lambda](https://cloudacademy.com/amazon-web-services/understanding-aws-lambda-course/) allows you to not deal with over/under capacity, deployments, scaling and fault tolerance, OS or language updates, metrics, and logging. AWS Lambda introduced a new serverless world. AWS community started working on a complete solution to develop AWS-powered microservices or backends for web, mobile, and IoT applications. The migration required facilitation because of the building-block nature of AWS Lambda and its complex symbiosis with Amazon API Gateway. One example is [Serverless Framework](https://serverless.com) that allows to build auto-scaling, pay-per-execution, event-driven apps on AWS Lambda. **(optional project)**

 
Before starting the next [task 3.2](#Tasks32), create a `.pynb` file that demonstrates that you followed this example.

<a name="Tasks32"/>

## Task 3.2: How to provide our service combined with third-party services?

In order to augment the value of our service we can build our service upon other services. As a example of combining our service with third-party services I suggest to plotting tweets on a map. Twitter allows its users to provide their location when they publish a tweet, in the form of latitude and longitude coordinates. With this information, we are ready to create some nice visualisation for our data, in the form of interactive maps. 
However the problem in our Twitter case is that we can find details about the geographic localization of the user's device in the form of geographic coordinates only in a small portion of tweets. Many users disable this functionality on their mobile, however there is still some tweets tagged with this information and allow us to show an example of using a third-party services that allows a nice way to provide an easy-to-digest of a particular feature of a dataset. 



For this purpose we will use [GeoJSON](http://geojson.org), a format for encoding a variety of geographic data structures and [Leaflet.js](http://leafletjs.com), a Javascript library for interactive maps.

GeoJSON supports a variety of geometric types of format that can be used to visualize the desired shapes onto a map. A GeoJSON object can represent a geometry, feature, or collection of features. Geometries only contain the information about the shape; its examples include Point, LineString, Polygon, and more complex shapes. Features extend this concept as they contain a geometry plus additional (custom) properties. Finally, a collection of features is simply a list of features. A GeoJSON data structure is always a JSON object. The following snippet shows an example (taken from https://github.com/bonzanini/Book-SocialMediaMiningPython) of GeoJSON that represents a collection with two different points, each point used to pin a particular city:
```
{
	"type": "FeatureCollection",
	"features": [
		{
			"type": "Feature",
			"geometry": {
				"type": "Point",
				"coordinates": [
					-0.12
					51.5
				]
			},
			"properties": {
				"name": "London"
			}
		},
		{
			"type": "Feature",
			"geometry": {
				"type": "Point",
				"coordinates": [
					-74,
					40.71
				]
			},
			"properties": {
				"name": "New York City"
			}
		}
	]
}

```
In this GeoJSON object, the first key is the `type` of object being represented. This field is
mandatory and its value must be one of the following:
* `Point`: This is used to represent a single position
* `MultiPoint`: This represents multiple positions
* `LineString`: This specifies a string of lines that go through two or more positions
* `MultiLineString`: This is equivalent to multiple strings of lines
* `Polygon`: This represents a closed string of lines, that is, the first and the last positions are the same
* `GeometryCollection`: This is a list of different geometries
* `Feature`: This is one of the preceding items (excluding GeometryCollection) with additional custom properties
* `FeatureCollection`: This is used to represent a list of features

Given that `type` in the preceding example has the `FeatureCollection` value, we will expect the `features` field to be a list of objects (each of which is a `Feature`).

The two features shown in the example are simple points, so in both cases, the
`coordinates` field is an array of two elements: longitude and latitude. This field also
allows for a third element to be there, representing altitude (when omitted, the altitude is
assumed to be zero).

For our examples, we just need the simplest structure, a `Point` identified by its coordinates (latitude and longitude). In order to generate this GeoJSON data structure we simply need to iterate all the tweets looking for the coordinates field. A very important warning is that this field may not be present. As we mentioned before, many tweets not include their geographic location (many users prefer not indicate this information on their tweets).

The following code will create the GeoJSON data structure (for tweets where the coordinates are explicitely given) and then the data are dumped into a file called geo_data.json:
```
import json

fname = 'Lab3.CaseStudy.json'
with open(fname, 'r') as f:
    geo_data = {
        "type": "FeatureCollection",
        "features": []
    }
    for line in f:
        tweet = json.loads(line)
        if tweet['coordinates']:
            geo_json_feature = {
                "type": "Feature",
                "geometry": tweet['coordinates'],
                "properties": {
                    "text": tweet['text'],
                    "created_at": tweet['created_at']
                }
            }
            geo_data['features'].append(geo_json_feature)
 
with open('geo_data.json', 'w') as fout:
    fout.write(json.dumps(geo_data, indent=4))
```

Now, with the Leaflet.js Javascript library for interactive maps, we can create our `.html` file that will host the map.
The following `.html`page provides the maps with our geolocated tweets:

```html
<!DOCTYPE html>
<html>
<head>
	
	<title>Quick Start - Leaflet</title>

	<meta charset="utf-8" />
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	
	<link rel="shortcut icon" type="image/x-icon" href="docs/images/favicon.ico" />

	<link rel="stylesheet" href="https://unpkg.com/leaflet@1.0.3/dist/leaflet.css" />
	<script src="https://unpkg.com/leaflet@1.0.3/dist/leaflet.js"></script>

	<script src="http://code.jquery.com/jquery-2.1.0.min.js"></script>

	<style>
		#map {
    	height: 600px;
		}
</style> 

</head>
<body>


<!-- this goes in the <body> -->
<div id="map"></div>
<script>
// Load the tile images from OpenStreetMap
var mytiles = L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png', {
    attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
});

// Initialise an empty map
	var map = L.map('map');
// Read the GeoJSON data with jQuery, and create a circleMarker element for each tweet
$.getJSON("./geo_data.json", function(data) {
    var myStyle = {
        radius: 2,
        fillColor: "red",
        color: "red",
        weight: 1,
        opacity: 1,
        fillOpacity: 1
    };
 
    var geojson = L.geoJson(data, {
        pointToLayer: function (feature, latlng) {
            return L.circleMarker(latlng, myStyle);
        }
    });
    geojson.addTo(map)
});
map.addLayer(mytiles).setView([40.5, 5.0], 5);
</script>
</body>
</html>


```
A screenshot of the results:

![Lab3map2](https://github.com/jorditorresBCN/Assignments-2017/blob/master/Lab3map2.png "Lab3map2")
![Lab3map1](https://github.com/jorditorresBCN/Assignments-2017/blob/master/Lab3map1.png "Lab3map1")

As you can look, in our example we have few geolocated tweets. However, the code is the same for any other example. 

<a name="Tasks33"/>

## Task 3.3: Student proposal

We are asking to the student to reproduce the steps described in the previous tasks in their dataset generated in Lab 3. Visit the [Leaflet Quick Start Guide](http://leafletjs.com/examples/quick-start/) for more details about the `html` file and  how `Leaflet.js` can be used. 

Create a `.pynb` file with markdown cells describing the program steps and the characteristics of the dataset created (e.g. the time frame for the download, etc.).

<a name="Tasks34"/>

## Task 3.4: Advanced Analytics as a Service in the Cloud

During this hands on we are going to experiment the power of the Cloud Vision API from Google to detect text within images as an example of Advanced Analytics as a Service in the Cloud. 
This hands-on follows the official introduction to image classification that is available [here]( https://cloud.google.com/vision/docs/detecting-labels).


This hands-on helps you to classify images using labels. [Cloud Vision API](https://cloud.google.com/vision/) enables developers to understand the content of an image by encapsulating powerful machine learning models in an easy to use REST API. It quickly classifies images into thousands of categories (e.g., "sailboat", "lion", "Eiffel Tower"), detects individual objects and faces within images and finds and reads printed words contained within images. You can build metadata on your image catalog, moderate offensive content, or enable new marketing scenarios through image sentiment analysis. Analyze images uploaded in the request or integrate with your image storage on Google Cloud Storage. Try the API [here (Drag image file here or Browse from your computer)](https://cloud.google.com/vision/).


### 3.4.1 Cloud Platform sign up
The first step is to [sign up]( https://cloud.google.com/vision/). It exists a free trial for this service, to fully enjoy the benefits of Google’s Cloud platform the best option is to get a business trial, with $300 worth of free credit for this year. That amount is far more than enough for the expected needs on this subject’s scope and will give you time enough to explore other features. Once the registration process is finished, we need to access the [management console]( https://console.cloud.google.com/home), where it is possible to create a new project, choosing a name and an ID for it. It is necessary to enable billing for this project to continue, but as long as we are spending from the free credit we have there is nothing to worry about.
The project creation process takes a few seconds, after finishing it will appear in our list. It is time to select the new project and enable the Cloud Vision API that is the tool we will use during this hands-on. Finally, it is needed to set up some credentials that will be the way we authenticate to enable the communication: This can be done selecting “Credentials” in the left menu and simply clicking over the blue button saying “Create credentials” and choosing the “service account key” option.  Select JSON as your key type. Once completed, your service account key is downloaded to your browser's default location.

### 3.4.2 Environment setup
The base language is Python and those packages and almost every needed module will be already installed. The scripts and demo files needed can be downloaded from the official Google Cloud Platform GitHub repository, and only a few more changes will be needed to be ready.

#### Download the Example Code

Download the code from this repository.
If you have [`git`](https://git-scm.com/) installed, you can do this by executing the following command:

```$ git clone https://github.com/jorditorresBCN/google-cloud-vision-example.git```

This will download the repository of samples into the directory
`cloud-vision`.

Otherwise, github offers an
[auto-generated zip file](https://github.com/jorditorresBCN/google-cloud-vision-example/archive/master.zip) of the
`master` branch, which you can download and extract.


This example has been tested with Python 2.7 and 3.4.

#### Set Up to Authenticate With Your Project's Credentials

Next, set up to authenticate with the Cloud Vision API using your project's
service account credentials. E.g., to authenticate locally, set
the `GOOGLE_APPLICATION_CREDENTIALS` environment variable to point to your
downloaded service account credentials before running this example:

```export GOOGLE_APPLICATION_CREDENTIALS=/path/to/your/credentials-key.json```

If you do not do this, you will see an error that looks something like this when
you run the script:
`HttpError 403 when requesting
https://vision.googleapis.com/v1/images:annotate?alt=json returned
"Project has not activated the vision.googleapis.com API. Please enable the API
for project ..."`


### 3.4.2 Quick Start: Running the Example

![Tweet](https://raw.githubusercontent.com/jorditorresBCN/Assignments-2017/master/tweetMN.png "Tweet")


If you'd like to get the example up and running before we dive into the
details, here are the steps to analyze the image of this [tweet](https://twitter.com/JordiTorresBCN/status/876706346229477376).

1. Enable your virtualenv and go to the repository folder:
```
$ source <path-to-virtualenv>/bin/activate
$ cd google-cloud-vision-example
```

2. Install the requirements:
```
$ pip install -r requirements.txt
```

2. Run the `label.py` passing the downloaded tweet image as argument:
```
$ python label.py <path-to-image>
```

3. Wait for the script to finish. It will show 5 possible classfications for your image:
```
Results for image Marenostrum.jpg:
electricity - 0.770
computer cluster - 0.745
display device - 0.735
electrical supply - 0.671
electrical wiring - 0.667
```





# How to Submit this Assignment:  
Be sure that you have updated your remote github repository with  the Lab `.ipynb` file generated along this Lab. Submit **before the deadline** to the *RACO Practicals section* a "Lab3.txt" file including: 

1. Group number
2. Name and email of the members of this group
3. Include `.pynb`, `.py` and `.html` files created for this lab.
4. Add screenshots of maps created in task 3.3.
5. In case you finished optional task 3.4, include a report explaining it.
6. Add any comment that you consider necessary.
