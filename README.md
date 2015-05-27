<!-- <div class="row" style="margin-left:35px; margin-right:35px;">

<div class="row">
 -->
## RoboBrain API Documentation

This document is the full specification of the RoboBrain API. It includes both [Client API](wiki/robobrain-client-api) and the[Query API](wiki/robobrain-query-api). [RoboBrain Client API](wiki/robobrain-client-api) is developed for pushing information and knowledge into the RoboBrain and [Query API](wiki/robobrain-query-api) is developed for issuing queries to the RoboBrain.

For the technical report explaining the technical and algorithmic aspects of the RoboBrain, please refer to: arXiv Link [RoboBrain: Large-Scale Knowledge Engine for Robots](http://arxiv.org/abs/1412.0691)

### Summary

*   **[RoboBrain Client API](#api##client_api)**

	1.  [Installation](#api##install)
	2.  [Client API Credentials](#api##client)
	3.  [Information Structure for RoboBrain Interface](#api##information)
	4.  [Using the RoboBrain Client API (In Your Own Code)](#api##using)
	5.  [Using the RoboBrain Client API (As a Script)](#api##script)

*   **[RoboBrain Query API]((#RaQueL#welcome-to-RaQueL))**

### RoboBrain Client API

RoboBrain supplies a REST API to its collaborators. Through RoboBrain REST API, collaborators can send their multi-modal information to the brain.

RoboBrain Client API is a client developed purely in Python 2.X in order to provides unified and easy way to push information to the brain.

*   It has multiple processing modes and provide both batch upload and serial upload. Batch upload is implemented in parallel.
*   It relies on authentication module of the robobrain.me through HTTP Auth.

#### Installation

In order to use the RoboBrain Client API, you need to install a few Python packages. Our client requires `boto` for s3 access, `request` for sending HTTP requests to the REST API and `filechunkio` for efficient file upload. They can be installed by using pip as

	sudo pip install boto  
	sudo pip install requests  
	sudo pip install filechunkio

After the required packages are installed, cloning the github repo from [Client API - Github](https://github.com/RoboBrainCode/Client) would suffice. For installation problems and requesting an access to the private repo, please send an e-mail to the [robobrain@cs.stanford.edu](mailto:robobrain@cs.stanford.edu).

#### Client API Credentials

In order to access to the RoboBrain Client API, you need a RoboBrain account. For the user account, please use the [Login link](http://robobrain.me/#/login). After creating the account, you need to send an e-mail to the [robobrain@cs.stanford.edu](mailto:robobrain@cs.stanford.edu) explaining your contributor account request. After you request is approved, you will receive an e-mail from us. After that, you can start to use the Client API with your credentials.

#### Information Structure for RoboBrain Interface

RoboBrain accepts information in terms of a json structure from its collaborators. Specifically, we use the following structure:

```javascript
{  
 "feedtype": "Type Of The Feed",  
 "text": "Text Caption of the Feed with HashTags",  
 "source_text": "Your Project Name",  
 "source_url": "Your Project URL",  
 "mediashow": [  
   "Is 0th image for visualization or for the brain graph ?",  
   "Is 1st image for visualization or for the brain graph ?",  
   "True or False",  
   "True means, it is for visualization"  
 ],  
 "media": [  
   "/your/local/path/to/media0",  
   "/your/local/path/to/media1",  
   "/your/local/path/to/media2"  
 ],  
 "mediatype": [  
   "Image",  
   "Video",  
   "Text"  
 ],  
 "keywords": [  
   "Some",  
   "Keywords",  
   "About",  
   "Feed"  
 ],  
 "mediamap": [  
   "#1", (this means 0th media is related to 1st hashtag)  
   "#0#1", (this means 1st media is related to 0th and 1st hashtags)  
   "#0" (this means 3rd media is related to 0th hashtag)  
 ],  
 "graphStructure": [  
   "has_appearance: #1 ->#0"  
   "Edge type and related nodes"  
   "It means there exist has_apparance edge from hashtag1 to hashtag0"  
 ],}
 ```

Here, we go over the each field in the format:

	"feedtype": "Type Of The Feed",

Feedtype is visualized in the bottom left corner of the feed in robobrain.me. It should include a few words and describe the broad category of the feed like Affordance, Grasping, Language etc.

	"text": "Text Caption of the Feed with HashTags",

Text is the caption of your feed. It should represent the information you are giving in a single English sentence. The keywords in the text which corresponds to abstract or physical entities which are useful to robots should be written as hashtags. Hashtag is written as #keyword. If the hashtag has more than one word, it should be written as #hashtag_with_more_than_one_words. An example is; "The position of a #Standing_human while using a #shoe is distributed as #$heatmap_12."

If you want to hashtag a media use #$ instead of #. For example, "#cup has an apperance in #image" is refering to the media not the concept "image"; hence, it should be replaced as "#cup has an apperance in #$image"

	"source_text": "Your Project Name", "source_url": "Your Project URL",

Source text is the name of the project which produces the feed and source url is a link to it. It is both used for visualization and verification if necessary.

	"mediashow": [   "Is 0th image for visualization or for the brain graph ?",   "Is 1st image for visualization or for the brain graph ?",   "True or False",   "True means, it is for visualization" ],

Each feed includes some media files as well. These files can be images, videos, parameters, point clouds etc. RoboBrain requires large amount of data so we suggest pushing as many media as possible. However, visualizing that many media is not preferred on robobrain.me web page. So, we requests the feed provider to choose a few media to be displayed on the robobrain.me and mark them as "True" in the "mediashow". The number of members of the "mediashow" should be equal to the number of media file you push. Indeed, order represent the matching between files and these attributes.

	"mediatype": [   "Image",   "Video",   "Text" ],

Every entry of the mediatype array represents the type of the corresponding media. Medias can be image, video or text if they have True as mediashow. If their mediashow is False, they can be any file.

	"keywords": [   "Some",   "Keywords",   "About",   "Feed" ],

Please write a few keywords representing your feed. These keywords will be used for disambiguation, entity linking etc. by our machine learning algorithms.

	"mediamap": [   "#1", (this means 0th media is related to 1st hashtag)   "#0#1", (this means 1st media is related to 0th and 1st hashtags)   "#0" (this means 3rd media is related to 0th hashtag) ],

Mediamap links each media with one or more hashtags. Hashtags are counted starting from 0 in their order of occurrence in the text. If they occur more than once, each occurrence is a different hashtag and each occurrence is counted. If you have multiple facts about the same object push them separately.

	"graphStructure": [   "has_appearance: #1 ->#0"   "Edge type and related nodes"   "It means there exist has_apparance edge from hashtag1 to hashtag0" ],

We also ask contributes to supply the graph structure in terms of predicate, subject and object. For example; has_appearance: #1 ->#0 is correspond to a predicate has_appearance, subject hashtag 1 and object hastag 0. For the list of predicates please use the following command

`python roboBrain.py getEdgeList`

It will display the currently supported set of edges/predicates. Try to use the available edges/predicates. However, if you need a different edge/predicate type, you can simply include it in your feed. We will automatically add it to the list. However, to make the machine learning algorithm slightly simpler, try to be consistent with the previously used edges.

Here, we also include an example feed:

	{
		"feedtype": "Object Affordance",  
	 	"text": "The position of a #Standing_human while using a #shoe is distributed as #$heatmap_12.",  
	 	"source_text": "Hallucinating Humans",  
	 	"source_url": "http://pr.cs.cornell.edu/hallucinatinghumans/",  
	 	"mediashow": [  
	   					"True",  
	   					"True"  
	 				],  
	 	"media": [  
	   	"/home/ubuntu/images/shoe_.jpg",  
	   	"/home/ubuntu/shoe_12_1.jpg_cr.jpg"  
	 				],  
	 	"mediatype": [  
	   					"Image",  
	   					"image",  
	 					],  
	 	"keywords": [  
	   					"Human",  
	 	  "Affordance",  
	 	  "Object",  
	   		"shoe",  
	   		"Standing"  
	 					],  
	 	"mediamap": [  
	   					"#1",  
	   					"#0#1#2"  
	 					],  
	 	"graphStructure": [  
	   						"spatially_distributed_as: #1 ->#2",  
	   	"spatially_distributed_as: #0 ->#2",  
	   "can_use: #0 ->#1"  
	 ],  
 	}
<!-- ``` -->

In all fields, only use alphanumeric characters, dash ‘-’, and an underscore ’_’ .


#### Using the RoboBrain Client API (In Your Own Code)

For using the RoboBrain Client, we suggest new users to start with `generateFeeds.py` file. It includes the minimal code to use the Client API. Here, we explain the `generateFeeds.py` file line by line:

`from RoboBrainClient.RoboBrainAPI import RoboBrainAPI`

With this line, we include the Client API

	RoboAPIUserName = 'YOUR_USER_NAME'  
 	RoboAPIPass = 'YOUR_PASSWORD'  
 	S3KEY = 'S3_KEY'  
 	S3SECRET = 'S3_SECRET'

Please use the credentials you created for robobrain.me as your `RoboAPIUserName` and `RoboAPIPass`. You will receive the `S3KEY` and `S3SECRET` information in the e-mail confirming your contributor account.  

	rC = RoboBrainAPI(RoboAPIUserName ,RoboAPIPass,S3KEY,S3SECRET,userHandle)

Here, we initialize the RobobrainAPI. Please note that at this point no connection with RoboBrain is made.

	for result in YourResults:
	rC.addFeed(data)

Here, you add feeds to the RoboBrain class by crawling through your own experimental results. Please read the formatting instructions in [Information Structure](wiki/Information%20Structure%20for%20RoboBrain%20Interface) before implementing your code. Please also note that, there is still no request is made to the RoboBrain and all information are stored in the memory. If you are pushing large amount of information, this approach can cause a problem. Hence, if you are pushing a large amount of information, please call the `rC.push2Brain()` function before you reach the memory limit of your system.

	URL = "http://robobrain.me:3000/api/feeds/"  
	rC.push2Brain(URL)

At this stage, we push all the aggregated information to the RoboBrain. This function will return an HTTP response. If the data push is successful, you will recieve `HTTP 201:OK` response. If you receive any other response, please make sure

*   You have no formatting error according to the specifications in [Information Structure](wiki/information-structure-for-robobrain-interface)
*   You have working internet connection and you can reach [http://robobrain.me:3000/api/feeds/](http://robobrain.me:3000/api/feeds/) from your browser.
*   You can login to robobrain.me successfully with your credentials.

If your answer is Yes to all aforementioned questions and still not receiving `HTTP 201:OK` response, please send an e-mail to [robobrain@cs.stanford.edu](mailto:robobrain@cs.stanford.edu)


#### Using the RoboBrain Client API (As a Script)

You can also use RoboBrain Client API as a script to upload all feeds in the current folder. You can use either `batchUpload.py` or `batchUploadforBigDataset.py`. Depending on the size of the information you are pushing. If the amount of information you are pushing (excluding media files) is small enough to fit in the memory, use the `batchUpload.py` since it is faster. However, if your data is too big (e.g. at the scale of WordNet, ImageNet, Freebase, RoboBrain etc.), use the `batchUploadforBigDataset.py`. It is slower but more reliable.

Before using the script, please change the following lines:

	RoboAPIUserName = 'YOUR_USER_NAME'  
	RoboAPIPass = 'YOUR_PASSWORD'  
 	S3KEY = 'S3_KEY'  
 	S3SECRET = 'S3_SECRET'

Use the credentials you created for robobrain.me as your `RoboAPIUserName` and `RoboAPIPass`. You will receive the `S3KEY` and `S3SECRET` information in the e-mail confirming your contributor account.

After editing the credentials, you can aggregate all feeds in the folder of the API. Each file should have an extension .json and it should correspond to a single feed. After collecting all the feeds in the same folder, you can use

`python batchUpload.py`

or

`python batchUploadforBigDataset.py`

At this stage, we push all the aggregated information to the RoboBrain. This script will return an HTTP response. If the data push is successful, you will recieve `HTTP 201:OK` response. If you receive any other response, please make sure

*   You have no formatting error according to the specifications in [Information Structure](wiki/Information%20Structure%20for%20RoboBrain%20Interface)
*   You have working internet connection and you can reach [http://robobrain.me:3000/api/feeds/](http://robobrain.me:3000/api/feeds/) from your browser.
*   You can login to robobrain.me successfully with your credentials.

If your answer is Yes to all aforementioned questions and still not receiving `HTTP 201:OK` response, please send an e-mail to [robobrain@cs.stanford.edu](mailto:robobrain@cs.stanford.edu)


## RoboBrain Query API

RoboBrain introduces [**RaQueL**](#RaQueL#welcome-to-RaQueL) (RoboBrain Query library) for querying the RoboBrain Engine. RaQueL is a combination of powerful functional programming concept with [CyPher graph query language](http://neo4j.com/docs/stable/cypher-query-lang.html) allowing us to build applications using RoboBrain.

