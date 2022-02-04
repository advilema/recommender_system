# Recommender System

## How to set-up the code

#### 1. Clone the repository
Open the command line, go in a directory of your choice, and run:
```bash
$ git clone https://git.unic.com/scm/aismtcu/recommender_system.git
```

#### 2. Create virtual Python environment
You need to have [Python3](https://www.python.org/) first. Follow the instruction corresponding to your operating system.

Install the ```venv.recommender```(any name you like) environment using venv (only for python3)
```bash
$ python3 -m venv your_place/venv.recommender
```
If you encounter a problem, follow the instruction in the error message. You may need to install the `python3-dev` package. Now you can activate the environment
```bash
$ source your_place/venv.scrapy/bin/activate
```
#### 3. Install required packages
Install packages from `requirements.txt` in the *activated* environment

```bash
(venv.recommender)$ cd recommender_system # go to the same folder as requirement.txt
(venv.recommender)$ pip install -r requirements.txt
```
#### 4. Deactivate virtual environment
The virtual environment can be deactivated at any time with

```bash
(venv.recommender)$ deactivate

```
#### 5. Run the app
```bash
(venv.recommender)$ cd recommender_system/ # stay in the same folder as scrapy.cfg
(venv.recommender)$ streamlit run app.py
```

This should open your favourite browser and launch the app on your local host 8501. <br>
Look at the image below to see how the page should look like.
![img.png](images/img1.png)


## How to Use The App

### Use the app
#### 1. Choose the topics
Under the question 'What kind of vacation are you looking for?' you can choose different topics for your vacation style.
The ones available at the moment are:
```python
topics = ['city', 'children', 'museums', 'art', 'music', 
          'traditions', 'luxury', 'nature', 'mountains',
          'relax', 'lake', 'wine', 'food', 'bike', 'ski', 
          'hiking', 'spa', 'summer', 'winter']
```
Look at the tutorial below on the **Change General Settings** section to see how to change the list of possible topics.
#### 2. Choose number of activities and distance
By using the 2 sliders you can choose the maximum number of activities that you want to do in a day, and the maximum
distance in kilometers between activities.

#### 3. Press Done
After you have chosen the topics, the number of activities and the distance and you have pressed **Done**, your page should
look something like this:
![img.png](images/img2.png)

* The combinations of activities recommended are sorted by _Similarity Score_.
* For each combination of activities you can see the titles of the activities. Clicking on the title it will expand
showing you a description of the activity.
* Every combination of activities has a colour (the big circle before the Similarity Score). 
In the map at the end of the page you can find the activities looking at their colours.
* The map is interactive, you can zoom in and out.

#### 4. Choose the combinations of activities that you like the most
Once you have read all the combinations of activities, you can select the ones that you've liked the most by checking
the small boxes on the left side, like this:
![img_1.png](images/img3.png)

#### 5. Update the User preferences
After checking your preferences, you can update the recommender system, so that it gives you better results, depending
on the combinations that you have chosen.

To do so, click on the button **Done** at the end of the page. At this point a button **Update** should appear. Click
on it and the recommendations should refresh and get more accurate.

#### 6. Visualize the User preferences
If you want to get an idea of how the system is tracking the user preferences, you can check on the box **show user vector**
under the slider to choose the maximum distance. A sidebar on the left side should appear:

![img.png](images/img4.png)

Check the box again to make the sidebar disappear.

#### 7. Change some of the initial settings
To change some initial settings, such as topics, number of activities and distance, you can click on the button
**Clear**, and afterwards you can change the settings, as you did on the points 1. and 2. When you are done you simply
click on the button **Done** as you did on the point 3.



## Change General Settings
### Change the topics list
Open the initialize.py file and change the topics list, then run:
```bash
(venv.recommender)$ python initialize.py
```
The topics in this list have been taken from the MySwitzerland.com 
website newsletter and have been integrated with topics generated by the clustering method that you can find in the file
_recommender/Clustering.py_

### Change the NLP model used
The NLP model that has been currently used to generate the words embedding is the "word2vec-google-news-300" that you can 
download using the gensim.downloader.
You can find many other models in the gensim.downloader documentation. <br>
To change this run:
```bash
(venv.recommender)$ python initialize.py --model_name THE_MODEL_YOU_LIKE_THE_MOST
```

## How To Use the Code
For a demo on how to use all the different classes that you can find into the folder _recommender_ look at the
_Notebook.ipynb_ that you can use as a playground to experiment with the code. <br>
This operation is done by the _DataLoader_.

## General Theoretical Overview
### Text Pre-Processing
Before being able to use the articles (that are saved into the _data/data.csv_ file) for our recommender system, we need 
to preprocess the text into the articles. In particular, we remove from the articles all the stop_words (words that do 
not add meaning to the text, such as "and", "or", "then", "therefore", etc.) and words that are misspelled, and we
tokenize the words in the article getting a vector of words for each article.

### Word embedding
The word embedding is a technique that allows the representation of the words as real valued vectors, 
such that the words that are closer in the vector space are expected to be similar in meaning and context.
For more information on the topic check the [Wikipedia page](https://en.wikipedia.org/wiki/Word_embedding).

In our case, we used the _word2vec_ model for word embedding.

### Clustering method for topics generation
A clustering method (KMeans, find more information about how it works [here](https://de.wikipedia.org/wiki/K-Means-Algorithmus))
have been used to cluster the words from all the articles (that are saved into _data/data.csv_) to detect which are the 
predominant topics into the articles. <br>
The class _Clustering_ that has been used to perform the clustering is in the 
file _recommender/Clustering.py_.

### Topics based vector representation of the articles
Once generated the topics with the clustering, eliminated the non-relevant ones, and integrated them with topics from the
MySwitzerland.com newsletter we can generate a vector representation of the articles based on how similar they are to the
given topic. <br>
To generate these topics based vector representation for an article, we follow the following steps:
1. we get the word embedding vector of all the words in the preprocessed article;
2. we get the word embedding vector of all the topics in the topics list
3. for each topic we compute the sum of the distances between the topic vector and all the word vectors in the article
4. at this point, given an article, for every topic we have a score. We normalize these scores with respect
to the sum of the scores, so that for each article their topics based vector representation is a 1-norm vector.
   
Example:
```
Learn to ski in the place where the world's top ski athletes 
compete: on Corviglia above St. Moritz. Your first ski lesson is 
rewarded with a carriage ride to the slope and back.
```
![img.png](images/img5.png)

### Position Based Recommendation



