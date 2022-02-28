---
title: "Gender Gap in Nature History Books"
date: 2022-02-28
categories: [Code]
tags: [gender, books, nature-history, python] # TAG names should always be lowercase
toc: true
comments: false
image:
  src: /attachments/images/ben-white-W8Qqn1PmQH0-unsplash.jpg
  width: 1000   # in pixels
  height: 400   # in pixels
  alt: an image of an open book
---

```python
import requests
import pandas as pd
import datetime
from bs4 import BeautifulSoup
```

## 1. About This Project

I try to read books on nature, nature history and popular science as much as I can. Then I wondered is there a gender gap in the amount of published books in these fields. I became curious about the percentage of women, trans and non-binary nature history writers. I found some good articles about gender gap in publishing. Especially [this](https://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.2004956) research on gender gap in academia and its [webpage](https://lukeholman.github.io/genderGap/) is worth looking.

## 2. Is there a place to find information on authors' gender identity?
There are some services that libraries can use like Novelist (which is a division of EBSCO now) that provides detailed information about books and their authors. In 2020 Novelist broadened their database to include trans and non-binary to their database (Reno, 2020). This service is available in Novelist Plus but my university is not subscribed to that. So is there another database for author gender identities? Enters VIAF.

### 2.1 What is VIAF?
You can read more about VIAF [here](https://en.wikipedia.org/wiki/Virtual_International_Authority_File). It is a database that combines information from multiple authorities inclusing libraries.

**Why I use it?**
There are personal pages for many authors in VIAF. Each page includes information about the authors' publication and personal data, including their gender. Check Urusla K. Le Guin's page [here](http://viaf.org/viaf/101734435/#Le_Guin,_Ursula_K.,_1929-2018) for example.

**Downside?**
The gender category for authors is binary. Check for instance one of my favorite non-binary authors page, [Annalee Newitz]( http://viaf.org/viaf/16492757/#Newitz,_Annalee,_1969-....). Annalee is categorised as "Female". So here is a shout out to VIAF:
> Please update your database to include more than two gender! 

...
(Not that anyone will here me from this tiny blog buuut....🤨. Yeap, my rant is finished now. Moving on.) 
...

**Where to download?**
I downloaded their date (access [here](http://viaf.org/viaf/data/ ) ). The txt.gz file is 1,33 GB and when its unzipped the csv file is 9,37 GB. In another notebook I simplified this file by getting the rows that only contained "en.wikipedia.org" as a string. Because most authors have a wikipedia page. 

This method ofcourse has limitations such as eliminating authors that doesnt have a wikipedi page or who has it but is in another language. ==So this can and should be improved.== But at least this way I deacresed the file size (from ~ 9 GB) to ~ 94 MB.

## 3. Finding the Nature History Writers
There are many book retailer webpages with decent categorisarion of millions of books. So
creating a book dataset with author names and book titles is relatively easy. I used [Book Depository](https://www.bookdepository.com/category/2985/Natural-History/browse/viewmode/all?page=2) and scraped its pages with python. 

They have a category called  "Natural History" perfect. I fetched all the books under this category. The result was ~ 10,000 books with titles, authors and publishing year. I filtered the result to only get the natural history books that were published between "2021 - Present" which yielded ~ 12,500 books. 

There are no gender identity information for authors in Book Depository nor in similar platforms. And this is why I need VIAF dataset.

## 4. Code Workflow
- Import & edit the Book Depository data
- Import & edit the VIAF data
- Cross check and match if an author listed in the Book Depository data exists in VIAF data.
- If yes: Go to authors page and get their gender info
- If no:  Try to predict the authors' gender from their first name by using Natural Language Toolkit
- Summarise the findings

## 5. Result
- There are 1230 books published from 2021 until now within "Nature History" category.
- I retrieved the author gender of~ 37% of these books from VIAF page.
- For the remaining ~63% of the books/authors I used Natural Language Processing to predict their gender from their first names.
- The results show that 6 out of 10 natural histoy books that were published after 2021 are written by male authors. 

However it's difficult to fully rely on these results. There are numerous things to improve in this project besides finding a database with broader gender identities. These are:
- Find a better way to systematically access authors' VIAF pages
- Perhaps, eliminating VIAF and simply relying on gender prediction can simplify the project
- Improving the Natural Language Toolkit (NTLK) results to increase the binary-gender prediction accuracy

## 6. Code 
### Bookdepositort Dataset

```python
# Import Bookdepository CSV
books = pd.read_csv(".../NaturalHistory-Bookdepository-2021.csv", dtype=str)
books.head(5)
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>authors</th>
      <th>titles</th>
      <th>date</th>
      <th>year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>17</td>
      <td>Merlin Sheldrake</td>
      <td>Entangled Life</td>
      <td>02 Sep 2021</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>1</th>
      <td>35</td>
      <td>Jeremy Clarkson</td>
      <td>Diddly Squat</td>
      <td>11 Nov 2021</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>2</th>
      <td>38</td>
      <td>Lia Leendertz</td>
      <td>The Almanac</td>
      <td>02 Sep 2021</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>3</th>
      <td>43</td>
      <td>Sosuke Natsukawa</td>
      <td>The Cat Who Saved Books</td>
      <td>16 Sep 2021</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>4</th>
      <td>60</td>
      <td>James Stewart</td>
      <td>Dinosaur Therapy</td>
      <td>24 Aug 2021</td>
      <td>2021</td>
    </tr>
  </tbody>
</table>
</div>

```python
# Fill in the NaN values to prevent future errors
for i in range(len(books['authors'])):
    author = books['authors'][i]
    boolean = pd.isna(author) # true or false
    
    if boolean == False:
        books['authors'][i] = books['authors'][i]
    else:
        books['authors'][i] = "No Author"

# Before filling the NaN values the below cell was empty
#books['authors'][854]
```

### Virtual International Authority File (VIAF) Dataset

```python
# Import VIAF CSV
viaf_db = pd.read_csv(".../viaf-simple.csv", dtype=str)

# Get the name of the person from the Wiki links
viaf_db['Name'] = viaf_db['info'].str.split('wiki/').str[1]
viaf_db['Name'] = viaf_db['Name'].str.split('_\(').str[0]
viaf_db['Name'] = viaf_db['Name'].str.replace('_',' ')
viaf_db.drop('Unnamed: 0', axis=1, inplace=True)

viaf_db.head(5)
```
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>viaf</th>
      <th>info</th>
      <th>Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>http://viaf.org/viaf/100177876</td>
      <td>Wikipedia@https://en.wikipedia.org/wiki/Guilla...</td>
      <td>Guillaume Caoursin</td>
    </tr>
    <tr>
      <th>1</th>
      <td>http://viaf.org/viaf/100208187</td>
      <td>Wikipedia@https://en.wikipedia.org/wiki/César-...</td>
      <td>César-Pierre Richelet</td>
    </tr>
    <tr>
      <th>2</th>
      <td>http://viaf.org/viaf/100232568</td>
      <td>Wikipedia@https://en.wikipedia.org/wiki/Paul_B...</td>
      <td>Paul B. Weisz</td>
    </tr>
    <tr>
      <th>3</th>
      <td>http://viaf.org/viaf/100232612</td>
      <td>Wikipedia@https://en.wikipedia.org/wiki/Paul_B...</td>
      <td>Paul B. Thompson</td>
    </tr>
    <tr>
      <th>4</th>
      <td>http://viaf.org/viaf/100319661</td>
      <td>Wikipedia@https://en.wikipedia.org/wiki/John_G...</td>
      <td>John Gosling</td>
    </tr>
  </tbody>
</table>
</div>
### Check VIAF links by using the Bookdepository author names

So VIAF file may contain many people and not necesseraly only authors. But we already have our author names from bookdepository. All we need to do is to cross check and link the book depository authors to their VIAF pages (if it exists). 

```python
links = []
author_with_viaf = []
books_with_viaf = []

names_to_predict = []
author_without_viaf = []
books_without_viaf = []

for i in range(len(books['authors'])):
    
    # Find whether the name in bookdeposit dataset exists in VIAF dataset
    name = books['authors'][i]
    #print(i, name)
    boolean_finding = viaf_db['Name'].str.contains(name, case=False).any() # True or False
    
    # If its true get the viaf link
    if boolean_finding == True:
        
        df2 = viaf_db[(viaf_db['Name'].str.contains(name, case=False))]
        viaf_link = df2['viaf'].loc[df2.index[0]]
        links.append(viaf_link)
        
        author_with_viaf.append(books['authors'][i])
        books_with_viaf.append(books['titles'][i])
    
    else:
        names_to_predict.append(name)
        author_without_viaf.append(books['authors'][i])
        books_without_viaf.append(books['titles'][i])
```


```python
# Convert lists to dataframe and then save locally (Saving the files makes it easier to test)
# Create dataframe from the lists
zipped1 = list(zip(author_with_viaf, books_with_viaf, links))
zipped2 = list(zip(author_without_viaf, books_without_viaf, names_to_predict))

viaf_authors = pd.DataFrame(zipped1, columns=['Author', 'Book', 'Links'])
predict_authors = pd.DataFrame(zipped2, columns=['Author', 'Book', 'Names'])

# storing these dataframes in a csv file
viaf_authors.to_csv(r'.../viaf_author_links.csv') #, index = None
predict_authors.to_csv(r'.../author_names_to_predict.csv') 
```


```python
b = len(books)
v = len(viaf_authors)
p = len(predict_authors)
print("There are {} books published from 2021 until now within Nature History category. This code managed to link {} of these book authors to their personal VIAF pages from which we will retrieve author gender information. We need to predict the gender for the remaining {} authors.".format(b,v,p))

```

There are 1230 books published from 2021 until now within Nature History category that are in bookdepository. This code managed to link 457 of these book authors to their personal VIAF pages from which we will retrieve author gender information. We need to predict the gender for the remaining 773 authors. 

In other words with this method we accessed ~ 37% of the authors gender data from VIAF page. For the remaining ~63% of the authors we need to use Natural Language Processing to predict their gender from their first names.

### Fetching gender data from VIAF pages with Beautifulsoup 

```python
# Import CSV
viaf_authors = pd.read_csv(".../viaf_author_links.csv", dtype=str)
# Create an empty column for gender
viaf_authors["Gender"] = ""
viaf_authors.head(5)
```

```python

for i in range(len(viaf_authors)):
    name = viaf_authors["Author"][i]
    
    url = viaf_authors['Links'][i]
    response = requests.get(url)
    html = response.content
    soup = BeautifulSoup(html, "lxml")
    
    gender = []
    for item in soup.select("div.subsection"):
        text = item.text.strip()
        text = text.replace('\n','')
        text = text.replace('\t',' ')
        text = text.replace('   ','')
        gender.append(text)

        text = text[0].split('Nationality')
        text = str(text[0])
        
    f = "Female" in gender[0]
    m = "Male" in gender[0]

    if f == True:
        viaf_authors["Gender"][i] = "Female"
        #print(name, "Female")  
    elif m == True:
        viaf_authors["Gender"][i] = "Male"
        #print(name, "Male")   
    else:
        viaf_authors["Gender"][i] = "Unknown"
        #print(name, "Unknown")

```

```python
# Export to CSV 
viaf_authors.to_csv('.../Viaf_authors_gender.csv')

# Import CSV
authors = pd.read_csv(".../Viaf_authors_gender.csv", dtype=str)
authors.head(5)
```

### Statistics (VIAF)
```python
f1 = authors['Gender'].value_counts()['Female'] # 113 <class 'numpy.int64'>
m1 = authors['Gender'].value_counts()['Male'] # 202
u1 = authors['Gender'].value_counts()['Unknown'] # 24

data1 = pd.DataFrame({
    'female': [f1],
    'male': [m1],
    'unknown': [u1]
})
data1
```

### Predicting gender from first name by using Natural Language Processing

For those bookdepository author names that didnt match with any VIAF data, we can use NLTK to train and predict the binary gender of the authors from their first names. 

Of course the method is problematic. Not just because it predicts and assigns the wrong  gender to authors' first name but also because of the binary gender assumption. In this method there is no room for non-bianry and trans people. I tested the trained NLTK with my name and with 80% accuracy the code said I was a male. Well ... I'm not. 

```python
# Source: https://www.geeksforgeeks.org/python-gender-identification-by-name-using-nltk/

import random
from nltk.corpus import names
import nltk
```

```python
# Import CSV
predict_authors = pd.read_csv(".../author_names_to_predict.csv", dtype=str)

# Create a colum for first names
predict_authors["FirstName"] = ""
predict_authors['FirstName'] = predict_authors['Names'].str.split(' ', expand=True)

# Create an empty column for gender
predict_authors["Gender"] = ""

predict_authors.head(5)
```

```python
import random
from nltk.corpus import names
import nltk

# Source1: ntlk prediction: https://www.geeksforgeeks.org/python-gender-identification-by-name-using-nltk/

def gender_features(word):
    return {'last_letter':word[-1]}
  
# preparing a list of examples and corresponding class labels.
#labeled_names = ([(name, 'Female') for name in names.words('/Users/nat/Desktop/FemaleMix.rtf')]+
             #[(name, 'Male') for name in names.words('/Users/nat/Desktop/MaleMix.rtf')]) # 133 f

labeled_names = ([(name, 'Female') for name in names.words('/Users/nat/Desktop/female.txt')]+
             [(name, 'Male') for name in names.words('/Users/nat/Desktop/male.txt')])

random.shuffle(labeled_names)

# we use the feature extractor to process the names data.
featuresets = [(gender_features(n), gender) 
               for (n, gender)in labeled_names]
  
# Divide the resulting list of feature
# sets into a training set and a test set.
train_set, test_set = featuresets[500:], featuresets[:500]
  
# The training set is used to 
# train a new "naive Bayes" classifier.
classifier = nltk.NaiveBayesClassifier.train(train_set)

predicted_gender = []

# Use the new dataframe with Nan instead of "book_database" dataframe because emoty cells cause issues.

for i in range(len(predict_authors)): #iterate over rows
    
    # Get the name
    name = predict_authors['FirstName'][i]
    #print(name)
    
    # If the name is in the database check gender
    if name in open('/Users/nat/Desktop/Allnames').read():
        gender = classifier.classify(gender_features(name))
        predict_authors["Gender"][i] = str(gender)
        #print(gender)
    
    # If the name is empty print unknown
    elif name == 0:
        predict_authors["Gender"][i] = "Unknown"
        #print("Empty")
        
    # If the name is not in the database print unknown
    else:
        #print ('Unknown')
        predict_authors["Gender"][i] = "Unknown"
    
    
```


```python
#pd.set_option('display.max_rows', None)
predict_authors.head(5)
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>Author</th>
      <th>Book</th>
      <th>Names</th>
      <th>FirstName</th>
      <th>Gender</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Lia Leendertz</td>
      <td>The Almanac</td>
      <td>Lia Leendertz</td>
      <td>Lia</td>
      <td>Female</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Sosuke Natsukawa</td>
      <td>The Cat Who Saved Books</td>
      <td>Sosuke Natsukawa</td>
      <td>Sosuke</td>
      <td>Unknown</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>Lelia Wanick Salgado</td>
      <td>Sebastiao Salgado. GENESIS</td>
      <td>Lelia Wanick Salgado</td>
      <td>Lelia</td>
      <td>Female</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Rodney Habib</td>
      <td>The Forever Dog</td>
      <td>Rodney Habib</td>
      <td>Rodney</td>
      <td>Female</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Andrew Cotter</td>
      <td>Dog Days</td>
      <td>Andrew Cotter</td>
      <td>Andrew</td>
      <td>Male</td>
    </tr>
  </tbody>
</table>
</div>

### Statistics (NTLK)
```python
f2 = predict_authors['Gender'].value_counts()['Female'] # 133 OR 186
m2 = predict_authors['Gender'].value_counts()['Male'] # 296
u2 = predict_authors['Gender'].value_counts()['Unknown'] # 85

data2 = pd.DataFrame({
    'female': [f2],
    'male': [m2],
    'unknown': [u2]
})
data2
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>female</th>
      <th>male</th>
      <th>unknown</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>277</td>
      <td>362</td>
      <td>134</td>
    </tr>
  </tbody>
</table>
</div>
### Summing it all up

```python
# Total of VIAF gender & predicted gender
total_f = f1 + f2
total_m = m1 + m2
total_u = u1 + u2

total = pd.DataFrame({
    'Female': [total_f],
    'Male': [total_m],
    'Unknown': [total_u]
})
total
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Female</th>
      <th>Male</th>
      <th>Unknown</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>429</td>
      <td>626</td>
      <td>175</td>
    </tr>
  </tbody>
</table>
</div>

```python
mylabels = ["Female", "Male", "Unknown"]
mycolors = ["#34595a", "#a0c293", "#f0cfc3"]

#controls default text size
plt.rc('font', size=15)

#set title font to size 50
plt.rc('axes', titlesize=50) 

plt.pie(total, 
        labels = mylabels, 
        autopct ='%1.1f%%',
        colors = mycolors,
        wedgeprops = {"edgecolor" : "black", 'linewidth': 2, 'antialiased': True})

plt.legend(loc='upper left')
plt.title('Gender Gap')

# Save figure
#plt.savefig('/Users/nat/Desktop/gender-gap.png', dpi = 100)

# Display the graph onto the screen
#plt.show() 
```

![Gender Gap](/attachments/images/2022-02-28-Gender_Gap_Nature_History_Books.jpg) 



## References
Reno, A. (2020) _Author gender identity added to NoveList_, EBSCO Information Services, Inc. Available at: [https://www.ebsco.com/blogs/novelist/author-gender-identity-added-novelist](https://www.ebsco.com/blogs/novelist/author-gender-identity-added-novelist) (Accessed: 25 February 2022).

## Linked Sources