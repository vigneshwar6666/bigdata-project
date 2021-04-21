# Bigdata-project - Vigneshwar 

## Author
- Vigneshwar Reddy Lenkala

## Resource for Text Data
[The Project Gutenberg eBook of Hindu Magic, by Hereward Carrington](https://www.gutenberg.org/files/65121/65121-0.txt)

## Tools and Languages:
- Python Programming Language
- PySpark API
- Databricks cloud environment
- Pandas
- MatPlotLib,
- Urllib
- Regex

## Process

## Gathering Data:

1. We'll use the urllib.request library to request or pull data from the text data's url. Once the data is pulled, the data is stored in a temporary file called 'vigneshwar.txt' which will get the text data from the website https://www.gutenberg.org/ and title is 'The Project Gutenberg eBook of Hindu Magic, by Hereward Carrington'.
```Bash
# Obtain the text data from URL and print it
import urllib.request
stringInURL = "https://www.gutenberg.org/files/65121/65121-0.txt"
urllib.request.urlretrieve(stringInURL, "/tmp/vigneshwar.txt")
```

2. The data gathered need to be saved. , we'll use dbutils.fs.mv to transfer it from the temporary data to a new site called data. The command returns a boolean value.
```Bash
dbutils.fs.mv("file:/tmp/vigneshwar.txt", "dbfs:/data/vigneshwar.txt")
```

3. We'll use sc.textfile to import the data file into Spark's RDD (Resilient Distributed Systems), which is a collection of elements that can be worked on in parallel. RDDs can be made in one of two ways: by parallelizing an existing array in your driver program, or by referencing a dataset in an external storage system such as a shared filesystem, HDFS, HBase, or another data source which supports RDDs.
```Bash
vigneshwarRDD = sc.textFile("dbfs:/data/vigneshwar.txt")
```

## Cleaning the data:

4. The above data consists of capitalized words, sentences, punctuations, and stopwords. In the first step of cleaning the info, we'll use flatMap to break it down, changing any capitalized terms to lower case, removing any spaces, and breaking sentences into words.
```Bash
# flatmap each line to words
wordsRDD = vigneshwarRDD.flatMap(lambda line : line.lower().strip().split(" "))
```

5. The next step is removing the punctuation. We'll use the re(regular expression) library for something that doesn't look like a file.
```Bash
import re
cleanspacesRDD = wordsRDD.map(lambda w: re.sub(r'[^a-zA-Z]','',w))
```

6. Next step is to remove stopwords. To do that we need to import StopWordsRemover library.
```Bash
from pyspark.ml.feature import StopWordsRemover
remover =StopWordsRemover()
stopword = remover.getStopWords()
CleanwordsRDD=cleanspacesRDD.filter(lambda words: words not in stopwords)
```

## Processing the data:

7. Next step after cleaning data is processing data.we will map our words into intermediate key-value pairs. we will create a pair consisting of ('', 1) for each word element in the RDD. We can create the pair RDD using the map() transformation with a lambda() function to create a new RDD. This will look like: (word,1), once we map it.
```Bash
IKVPairsRDD= CleanwordsRDD.map(lambda word: (word,1))
```

8. In this step, we'll perform the Reduce by key process. The response is the expression. Each time, we'll keep track of the first word count. If the same word appears more than once, the most recent one will be deleted and the first word count will be retained.
```Bash
WordcountRDD = IKVPairsRDD.reduceByKey(lambda acc, value: acc+value)
```

9. In this step we will retrieve the elements.collect() action function is used to retrieve all elements from the dataset(RDD/DataFrame/Dataset) as a Array(row) to the driver program.
```Bash
results = WordcountRDD.collect()
```

10. To Graph the data we will use the library mathplotlib. We can display any type of graph by plotting the x and y axis.
```Bash
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from collections import Counter

df = pd.DataFrame.from_records(results, columns =[plt.xlabel, plt.ylabel]) 
print(df)

mostCommon=results[5:10]
word,count = zip(*mostCommon)
import matplotlib.pyplot as plt
fig = plt.figure()
plt.bar(word,count,color="blue")
plt.xlabel("Words")
plt.ylabel("Count")
plt.title("Most used words in eBook of Hindu Magic")
plt.show()
```

## Charting Results:
![](https://github.com/vigneshwar6666/bigdata-project/blob/main/bigdata%20project.png)

## References: 
1. https://www.edureka.co/blog/spark-with-python-pyspark
2. https://docs.databricks.com/
3. https://ahmedrebai.medium.com/what-we-can-do-with-apache-spark-and-stack-overflow-data-8335fa781bb5























