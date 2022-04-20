# Democrat-and-Republican-politicians-sentiments-in-their-perception-of-disinformation-and-fake-news
This report scrapes tweets from two politicians and then conducts sentiment analysis to analyse the aspect differences between Democrat and Republican politicians’ sentiments in their perception of disinformation. (Grade 75%)

# Introduction
Donald Trump's rhetoric of 'fake news' and Russia's disinformation campaign during the 2016 U.S. presidential election highlighted the prevalent issue of disinformation online. 

Subsequently, various scholars such as Mo Jones-Jang (2020) and Joshua Tucker (2018) assert that disinformation threatens democracy by generating political cynicism, undermining political debates and increasing polarisation. Karishma Sharma (2021) maintains that identifying and reducing disinformation is critical to ensuring the integrity of elections and democratic processes. Hence, Deen Freelon (2021) suggests that ideological asymmetries between left- and right-wing activism hold critical implications for democratic practice and social media governance.

Consequently, this report aims to analyse the aspect differences between Democrat and Republican politicians' sentiments in their perception of disinformation. The two politicians' Tweets analysed to represent the Republican and Democrat sentiments are Senator Ted Cruz (Republican) and Senator Alex Padilla (Democrat). Both politicians are prominent figures within their party, and Statista (2022) reports that Cruz and Padilla are the most active Twitter users for their respective parties. Therefore, Cruz and Padilla have enough text data to analyse and political clout to represent their parties' perception of disinformation.

Section 1.0 scrapes and describes the text data from politicians' Tweets. Section 2.0 processes and cleans the text data for analysis. Additionally, section 2.0 examines the text data by investigating the corpus of each politician, frequency of tweets and words used. Lastly, Section 3.0 utilises sentiment analysis to highlight the aspect differences between Republican and Democrat politicians' perceptions of disinformation.  

# Initial SetUp

```{r, include=FALSE}
# include=FALSE means that this particular R code chunk will not be included in the final report. Useful for global settings or bits of code that might not be necessary to show in the final report (settings/set-up lines of code).
if (!("knitr" %in% installed.packages())) {
  install.packages('knitr', repos='http://cran.rstudio.org')}
library(knitr)
if (!("formatR" %in% installed.packages())) {
  install.packages("formatR")}
library(formatR)
knitr::opts_chunk$set(echo = TRUE, error = FALSE, warning = FALSE, message = FALSE, fig.align = "center",
                      tidy.opts=list(width.cutoff=80), tidy=TRUE) # To avoid source code going out of bounds
                                                                  # Does not work if single var name is too
                                                                  # Long though, as in point 5 below!  
# This line is used to specify any global settings to be applied to the R Markdown script. The example sets all code chunks as “echo=TRUE”, meaning they will be included in the final rendered version, whereas error/warning messages or any other messages from R will not be displayed in the final, 'knitted' R Markdown file.
# Permanently setting the CRAN mirror (avoids error "trying to use CRAN without setting a mirror")
local({r <- getOption("repos")
       r["CRAN"] <- "http://cran.r-project.org"
       options(repos=r)})
```

## Set Working Directory 

```{r, include=F}
# User should set the relevant working directory.
# Either by Session --> Set Working Directory --> To Source File Location 
# OR setwd("")                                                            
# This code used the working directory: 
 setwd("~/Desktop/own data assesment 2") 
```

## Install Required Packages & Call Relevant Libraries

```{r}
# Installing the Relevant Packages:
if (!("quanteda" %in% installed.packages())) {
  install.packages("quanteda")
}
# quanteda extensions:
if (!("quanteda.textmodels" %in% installed.packages())) {
  install.packages("quanteda.textmodels")
}
if (!("quanteda.textplots" %in% installed.packages())) {
  install.packages("quanteda.textplots")
}
if (!("quanteda.textstats" %in% installed.packages())) {
  install.packages("quanteda.textstats")
}
if (!("devtools" %in% installed.packages())) {
  install.packages("devtools")
}
if (!("quanteda.corpora" %in% installed.packages())) {
  devtools::install_github("quanteda/quanteda.corpora")
}
if (!("quanteda.dictionaries" %in% installed.packages())) {
  devtools::install_github("kbenoit/quanteda.dictionaries")
}
# Additional packages: 
if (!("readtext" %in% installed.packages())) {
  install.packages("readtext")
}
if (!("spacyr" %in% installed.packages())) {
  install.packages("spacyr")
}  
  if (!("caret" %in% installed.packages())) {
  install.packages("caret", dependencies = TRUE)
}
if (!("e1071" %in% installed.packages())) {
  install.packages('e1071', dependencies=TRUE)
}
if (!("ROAuth" %in% installed.packages())) {
  install.packages("ROAuth")
}
if (!("readr" %in% installed.packages())) {
  install.packages("readr")
}
# Data manipulation: 
if (!("dplyr" %in% installed.packages())) {
  install.packages("dplyr")
}
if (!("lubridate" %in% installed.packages())) {
  install.packages("lubridate")
}
if (!("tidytext" %in% installed.packages())) {
  install.packages("tidytext")
}
if (!("ggplot2" %in% installed.packages())) {
  install.packages("ggplot2")
}
if (!("rtweet" %in% installed.packages())) {
  install.packages("rtweet")
}
require(quanteda)
require(quanteda.textmodels)
require(quanteda.textplots)
require(quanteda.textstats)
require(quanteda.corpora)
require(readtext)
require(dplyr)
require(tidytext)
require (ggplot2)
require(rtweet)
require(quanteda.dictionaries)
require(spacyr)
require(caret)
require(e1071)
require(dplyr)
require(lubridate)
require(ROAuth)
require(readr)
```
# Section 1.0 Tweet Scraping 
## 1.1 Twitter Authorization Step

```{r}
my_oauth <- rtweet::create_token(app = "MA5953_MW636", 
                                  consumer_key = "**********",
                                  consumer_secret = "**********",
                                  access_token="*************",
                                  access_secret = "**************")

# A Twitter developer account is required to scrap tweets from Twitter's application programming interfaces (APIs) via the R wrapper function. Having access to Twitter's Rest API allows the developer to retrieve static information of an individual Twitter user's data, friends and followers. Moreover, Twitter's Streaming API retrieves real-time information of tweets as they happen and access the global data stream. The code above shows API credentials, access codes, and the name of the Twitter app to gain access to the Twitter APIs. 
```

## 1.2 Scraping tweets of Ted Cruz and Alex Padilla  

```{r}
# Alex Padilla:
Padillatweets <- get_timelines(c("AlexPadilla4CA"), n = 3200, parse=T, token=my_oauth) # The get_timelines wrapper function captures the last 3200 tweets by Alex Padilla. The politicians Twitter handle (i.e. @AlexPadilla4CA) is needed to access their tweets.  
save_as_csv(Padillatweets, "Padilla_tweets.csv", prepend_ids = TRUE, na = "", fileEncoding = "UTF-8") # Creates a csv file of Padilla's tweets. 

View(Padillatweets) # Shows the last 3200 Tweets from Alex Padilla and information relating to his tweets i.e retweet count, mentions and when the tweet was posted. 

# Ted Cruz: 
Cruztweets <- get_timelines(c("tedcruz"), n = 3200, parse=T, token=my_oauth) # The get_timelines wrapper function captures the last 3200 tweets by Ted Cruz. The politicians Twitter handle (i.e. @tedcruz) is needed to access their tweets.  
save_as_csv(Cruztweets, "Cruz_tweets.csv", prepend_ids = TRUE, na = "", fileEncoding = "UTF-8") # Creates a csv file of Cruz's tweets. 

View(Cruztweets) # Shows the last 3200 Tweets from Ted Cruz and information relating to his tweets i.e retweet count, mentions and when the tweet was posted. 
```
# Section 2.0 Text Pre-Processing 
## 2.1 Reading Text Data into R & Transforming to a Corpus Object

```{r}
# The following code transforms the data files containing the politicians' texts and meta-data into a corpus object for text mining. A "Corpus" is a term from linguistics, and it indicates a collection of documents. A Corpus class object in R organises the text data and all the relevant meta-data, allowing subsequent easy processing of texts and text-descriptive variables (meta-data, i.e. author, dates). 

### Ted Cruz:
CruzTweets<-readtext("Cruz_tweets.csv", text_field = "text", encoding = "ISO-8859-1") # Instead of using read.csv the readtext function is used as it specifies what is the text column in our .csv file. This will make sure that R will distinguish the text column from the other columns in the file containing meta-data information.

# Twitter-specific step: remove emoticons (using regular expression for emojis/non-ASCII characters):
CruzTweets$text <-gsub("[^\x01-\x7F]", "", CruzTweets$text)
CruzCorpus<-corpus(CruzTweets)

### Alex Padilla: 
PadillaTweets<-readtext("Padilla_tweets.csv", text_field = "text", encoding = "ISO-8859-1") # Instead of using read.csv the readtext function is used as it specifies what is the text column in our .csv file. This will make sure that R will distinguish the text column from the other columns in the file containing meta-data information.

# Twitter-specific step: remove emoticons (using regular expression for emojis/non-ASCII characters):
PadillaTweets$text <-gsub("[^\x01-\x7F]", "", PadillaTweets$text)
PadillaCorpus<-corpus(PadillaTweets)
```
## 2.2 Text Descriptives

```{r}
# The code below saves Cruz and Padilla's summary(corpus) as an object for extracting relevant variables to retrieve useful information summarising the total number of words used (tokens) or the total number of unique words used (types) or total sentences you should first.

# Total number of documents in each corpus:
ndoc(CruzCorpus)
ndoc(PadillaCorpus)
```
<img width="137" alt="Screenshot 2022-04-20 at 15 27 06" src="https://user-images.githubusercontent.com/97530878/164253415-157833a5-a32f-47e9-8c2b-c404839a71bc.png">

```{r}
# Types, tokens, sentence summaries:
sumCruzcorp<-summary(CruzCorpus, n=3200)
summary(sumCruzcorp$Tokens)
summary(sumCruzcorp$Types)
summary(sumCruzcorp$Sentences)
sumPadillacorp<-summary(PadillaCorpus, n=3200)
summary(sumPadillacorp$Tokens)
summary(sumPadillacorp$Types)
summary(sumPadillacorp$Sentences)
```
<img width="348" alt="Screenshot 2022-04-20 at 15 27 25" src="https://user-images.githubusercontent.com/97530878/164253486-c9cc91b6-baec-4954-8337-53094d68e475.png">

```{r}
# Save the summary outputs in a csv file:
write.csv(sumCruzcorp, file="Cruz_Summary.csv", row.names=FALSE)
write.csv(sumPadillacorp, file="Padilla_Summary.csv", row.names=FALSE)
```
As illustrated above, Cruz had 3193 documents, and Padilla had 3200 documents in their corpus. Therefore, according to David Juckett (2012), both politicians surpass the gold standard corpus of 500 documents, which is predicted to give a capture probability of approximately 0.95. The output above shows that, on average, Padilla has ten more tokens (number of words in a text/corpus) than Cruz, suggesting Padilla has longer tweets than Cruz. However, on average, both politicians have the same number of sentences (two) in their tweets. Furthermore, on average, Padilla has seven more types (unique term/word) than Cruz, illustrating that Padilla has a greater lexical diversity than Cruz.

## 2.3 Dealing with Metadata

```{r}
# The following code cleans some of the metadata variables used later by renaming them. The code below tells R to use a particular corpus metadata object into a proper variable that can be used for analysis, e.g. the count of retweets for each tweet or the date of creation of each tweet. 

# The function **docvars** retrieves desired metadata columns and is used every time a corpus variable needs to be transformed (i.e. renaming/ splitting). Applying docvars will create a new variable in the corpus with the required transformation.
CruzCorpus$retweets<-docvars(CruzCorpus, "retweet_count")
CruzCorpus$timestamp<-docvars(CruzCorpus, "created_at")
PadillaCorpus$retweets<-docvars(PadillaCorpus, "retweet_count")
PadillaCorpus$timestamp<-docvars(PadillaCorpus, "created_at") # The function docvars renames variables as needed.
```
## 2.4 Additional Text Descriptives

```{r}
Fig1 <- ts_plot(CruzTweets, "24 hours") +
  ggplot2::theme_minimal() +
  ggplot2::theme(plot.title = ggplot2::element_text(face = "bold")) +
  geom_line(colour = "#1380A1") +
  ggplot2::labs(
    tag = "Fig.1", 
    x = 'Time', y = 'Number of Tweets',
    title = "Frequency of Tweets by Ted Cruz over time\n",
    subtitle = "Twitter status (Tweet) counts by 24 hour intervals",
    caption = "\nSource: Data collected from Twitter's REST API via rtweet" ) + 
    theme(plot.background = element_rect(fill = "#d5e4eb")) 

Fig2 <- ts_plot(PadillaTweets, "24 hours") +
  ggplot2::theme_minimal() +
  ggplot2::theme(plot.title = ggplot2::element_text(face = "bold")) +
  geom_line(colour = "red3") +
  ggplot2::labs(
    tag = "Fig.2", 
    x = 'Time', y = 'Number of Tweets',
    title = "Frequency of Tweets by Alex Padilla over time\n",
    subtitle = "Twitter status (Tweet) counts by 24 hour intervals",
    caption = "\nSource: Data collected from Twitter's REST API via rtweet" ) + 
    theme(plot.background = element_rect(fill = "#d5e4eb"))

Fig1 # Plots a timer series graph showing the frequency of Tweets by Ted Cruz over time.
```
<img width="599" alt="Screenshot 2022-04-20 at 15 20 35" src="https://user-images.githubusercontent.com/97530878/164252017-f4d312fa-702a-446a-b7cf-41ce5c62379f.png">

```{r}
Fig2 # Plots a timer series graph showing the frequency of Tweets by Alex Padillia over time.
```
<img width="600" alt="Screenshot 2022-04-20 at 15 20 47" src="https://user-images.githubusercontent.com/97530878/164252073-ddd415ec-31b5-4c4c-b337-496e495711d6.png">

The amount Cruz and Padilla tweet differ within 24 hours (figures 1 and 2); Padilla's frequency of tweets dates from late 2020, while Cruz's frequency is only from late 2021 onwards. Moreover, Cruz's greatest number of tweets in 24 hours was just over 60, while Padilla's was only 20. Consequently, these figures exhibit that Cruz is a more prolific tweeter than Padilla daily and across the year.

## 2.5 Keywords in Context

```{r}
# An essential step of descriptive analysis of text data is to explore how politicians use words and concepts relevant to disinformation. Keywords in Context (KWICs) show the word of interest (keyword) embedded in a number of precedent and subsequent words. Consequently, the KWICs identified for this report to explore the aspect differences between Democrat and Republican politicians' perceptions of disinformation are disinformation, misinformation and fake news. The code below highlights if and how Cruz and Padilla use the KWICs. 

```{r}
# Exploring KWICs:
kwic(tokens(PadillaCorpus), pattern = "disinformation", window=3)
```
<img width="209" alt="Screenshot 2022-04-20 at 15 30 08" src="https://user-images.githubusercontent.com/97530878/164254151-89cbbafc-e61f-430e-a387-b0e08a534880.png">

```{r}
kwic(tokens(CruzCorpus), pattern = "disinformation", window=3)
```
<img width="205" alt="Screenshot 2022-04-20 at 15 31 43" src="https://user-images.githubusercontent.com/97530878/164254478-4f45d53c-ddb2-49b0-9902-d4bcd035b1a1.png">

```{r}
kwic(tokens(PadillaCorpus), pattern = "misinformation", window=3)
```
<img width="208" alt="Screenshot 2022-04-20 at 15 32 21" src="https://user-images.githubusercontent.com/97530878/164254604-e8febf6d-5054-40f0-947b-40127fa51725.png">

```{r}
kwic(tokens(CruzCorpus), pattern = "misinformation", window=3)
```
<img width="198" alt="Screenshot 2022-04-20 at 15 32 56" src="https://user-images.githubusercontent.com/97530878/164254701-8114f0c7-b2cc-4eaa-93ec-0589e34a356d.png">

```{r}
# To explore all words containing the pattern:
kwic(tokens(PadillaCorpus), pattern = "disinformation", valuetype="regex", window=3)
```
<img width="206" alt="Screenshot 2022-04-20 at 15 33 33" src="https://user-images.githubusercontent.com/97530878/164254833-ed415d0b-5e41-4d3c-88b7-6c7e6aa3bd7d.png">

```{r}
kwic(tokens(CruzCorpus), pattern = "disinformation", valuetype="regex", window=3)
```
<img width="200" alt="Screenshot 2022-04-20 at 15 34 47" src="https://user-images.githubusercontent.com/97530878/164255051-665bdafe-0a4f-4ac7-8340-c243e1cb5fda.png">

```{r}
kwic(tokens(PadillaCorpus), pattern = "misinformation", valuetype="regex", window=3)
```
<img width="203" alt="Screenshot 2022-04-20 at 15 35 26" src="https://user-images.githubusercontent.com/97530878/164255217-16a92671-355e-4d22-ae1d-2958e0322983.png">

```{r}
kwic(tokens(CruzCorpus), pattern = "misinformation", valuetype="regex", window=3)
```
<img width="198" alt="Screenshot 2022-04-20 at 15 35 44" src="https://user-images.githubusercontent.com/97530878/164255277-f218ff70-0d06-4ede-a24e-a9d94911121e.png">

```{r}
# OR
kwic(tokens(PadillaCorpus), pattern = "*disinformation*", window=3)
```
<img width="203" alt="Screenshot 2022-04-20 at 15 36 09" src="https://user-images.githubusercontent.com/97530878/164255372-b40bba4c-ec2d-4147-aab6-df4b2473e66f.png">

```{r}
kwic(tokens(CruzCorpus), pattern = "*disinformation*", window=3)
```
<img width="203" alt="Screenshot 2022-04-20 at 15 36 22" src="https://user-images.githubusercontent.com/97530878/164255444-e6556bd6-3779-4daf-bd3a-48a9bac905bb.png">

```{r}
kwic(tokens(PadillaCorpus), pattern = "*misinformation*", window=3)
```
<img width="209" alt="Screenshot 2022-04-20 at 15 37 47" src="https://user-images.githubusercontent.com/97530878/164255741-6cd91cd8-c587-41a0-8a41-2953310ec136.png">

```{r}
kwic(tokens(CruzCorpus), pattern = "*misinformation*", window=3)
````
<img width="199" alt="Screenshot 2022-04-20 at 15 38 07" src="https://user-images.githubusercontent.com/97530878/164255830-dee2038c-a326-4bbd-aed1-2914f8289068.png">

```{r}
# Multi-word KWICs:
kwic(tokens(PadillaCorpus), pattern = phrase("fake news"), window=3)
```
<img width="207" alt="Screenshot 2022-04-20 at 15 38 51" src="https://user-images.githubusercontent.com/97530878/164256008-856f85aa-0556-4858-9a83-06f4ad70e23c.png">

```{r}
kwic(tokens(CruzCorpus), pattern = phrase("fake news"), window=3)
```
<img width="204" alt="Screenshot 2022-04-20 at 15 39 12" src="https://user-images.githubusercontent.com/97530878/164256097-36b44858-87f4-4d35-9b36-ac959de0fa00.png">

As shown above, exploring the KWICs by fixed and regular pattern matching revealed no difference in the frequency of KWICs used by each politician. Cruz mentioned disinformation, misinformation and fake news five times each. Conversely, Padilla did not mention fake news but cited disinformation thirty-seven times and misinformation twenty-five times. Consequently, according to Zipf's Law, words mentioned more frequently reflect the most significant concerns/meaning to be conveyed. Thus, these findings reinforce Richard Stengel's (2020) and Dimitar Nikolov's (2021) conclusion that disinformation is spread more among politically right ideologies. Thus, it is a more dominant issue for Democrats, for example, the disinformation on Covid-19, voter fraud and climate change.

## 2.6 Document-Feature Matrices & Text Data Cleaning

```{r}
# The document-feature matrix (dfm) is the building block of any text-mining exercise. The code below converts a corpus object into a dfm object. Moreover, the code below allows to 'pre-process' the text data in the corpus. Dfm object options such as "remove=" or "stem=" allow to, for example, eliminate stop-words, punctuation and to stem words from Tweets.

DFM_Cruz<- tokens(CruzCorpus, 
                   remove_punct = TRUE, 
                   remove_symbols = T, 
                   remove_numbers=T, 
                   remove_url=T) %>%
                   dfm() %>%
                   dfm_tolower()
# Check top words:
topfeatures(DFM_Cruz, n = 20)
````
<img width="435" alt="Screenshot 2022-04-20 at 15 43 31" src="https://user-images.githubusercontent.com/97530878/164257091-4b52b227-d4cc-42bf-8137-5ecb01796248.png">

```{r}
DFM_Padilla<- tokens(PadillaCorpus, 
                   remove_punct = TRUE, 
                   remove_symbols = T, 
                   remove_numbers=T, 
                   remove_url=T) %>%
                   dfm() %>%
                   dfm_tolower()
# Check top words:
topfeatures(DFM_Padilla, n = 20) 
```
<img width="432" alt="Screenshot 2022-04-20 at 15 43 50" src="https://user-images.githubusercontent.com/97530878/164257156-c1b5e7df-747e-4736-8f74-dc77000ef2ac.png">

```{r}
# Stopwords are taking centre-stage and thus eliminated from the dfm, as they're unlikely to be useful in text analysis (may only capture rhetorical style).

# Exploring stopwords: 
head(stopwords("en"), 100)
```
<img width="387" alt="Screenshot 2022-04-20 at 15 44 42" src="https://user-images.githubusercontent.com/97530878/164257348-16d0713c-176e-47a7-aa48-fbe12e087dc6.png">

```{r}
head(stopwords("it"), 100)
```
<img width="433" alt="Screenshot 2022-04-20 at 15 44 56" src="https://user-images.githubusercontent.com/97530878/164257414-6bf0e165-5854-4e9e-8348-22cdea581c78.png">

```{r}
head(stopwords("fr"), 100)
```
<img width="396" alt="Screenshot 2022-04-20 at 15 45 13" src="https://user-images.githubusercontent.com/97530878/164257486-2b1bb03f-fb86-490b-8605-2955dfc04501.png">

```{r}
# Remove stopwords:
DFM_Cruz<- dfm_remove(DFM_Cruz, pattern = stopwords("en"))
# Check top words:
topfeatures(DFM_Cruz, n = 20)
```
<img width="450" alt="Screenshot 2022-04-20 at 15 45 41" src="https://user-images.githubusercontent.com/97530878/164257595-c22ecb0d-a9b2-4b4d-9bb1-4608d3e1faa8.png">

```{r}
DFM_Padilla<- dfm_remove(DFM_Padilla, pattern = stopwords("en"))
# Check top words:
topfeatures(DFM_Padilla, n = 20)
```
<img width="425" alt="Screenshot 2022-04-20 at 15 46 12" src="https://user-images.githubusercontent.com/97530878/164257691-ad5161c0-f140-45f1-89e4-09e14e6efbdf.png">

```{r}
# Removes other words that are irrelevant:
DFM_Cruz <- dfm_remove(DFM_Cruz, pattern = c("cruz", "ted", "amp", "@tedcruz", "now", "just", "new", "get", "@sentedcruz"))
DFM_Padilla <- dfm_remove(DFM_Padilla, pattern = c("must", "can", "time", "now", "need", "one", "us"))

topfeatures(DFM_Cruz, n = 20) # Shows Cruz's 20 most frequent words
```
<img width="456" alt="Screenshot 2022-04-20 at 15 46 39" src="https://user-images.githubusercontent.com/97530878/164257780-a9d5548d-d91e-415a-9e49-2bf365659cfa.png">

```{r}
topfeatures(DFM_Padilla, n = 20) # Shows Padilla's 20 most frequent words
```
<img width="429" alt="Screenshot 2022-04-20 at 15 46 48" src="https://user-images.githubusercontent.com/97530878/164257818-6efadf56-e127-46b4-b21b-a2deabcb07a4.png">

## 2.7 Stemming

```{r}
# Stemming is a step that brings all tokens to their root form (i.e. worker, working, works = work). The stemmer algorithm accomplishes word-stemming by removing common suffixes/affixes using a lookup table of word roots. Stemming helps in reducing the sparsity of the dfm and improving computational efficiency.

DFM_Cruz_stem <- dfm_wordstem(DFM_Cruz)
DFM_Padilla_stem <- dfm_wordstem(DFM_Padilla)
```

## 2.8 Term Frequency, Inverse Document Frequency Weighting

```{r}
# The tf-idf weighting strategy is employed in the code below to further reduce the dfm to help the computational efficiency of text classifiers and explore Zipf's Law. The tf-idf weighting strategy eliminates low-frequency words and highly common words (unlikely to be discriminant) by computing the frequency of a term in a document as well as its frequency across the total number of documents. Then the tf-idf takes the inverse document frequency of the term and multiples it to its frequency in the document. Therefore, the weight is higher when the term is more discriminating. 

# tf-idf:
DFM_Cruz_tfidf<- DFM_Cruz %>%
  dfm_tfidf( scheme_tf = "count", scheme_df = "inverse",base = 10) 
DFM_Padilla_tfidf<- DFM_Padilla %>%
  dfm_tfidf( scheme_tf = "count", scheme_df = "inverse",base = 10) 
```

## 2.9 Text Descriptives: Word Clouds and Group Analysis

### Transforming Corpus & Text Data Cleaning  

```{r}
# This code below combines the two different corpora and then performs a comparative analysis by splitting the dfm by the author (Cruz and Padilla). 

# Adding two corpus objects together:
MasterCorpus<-CruzCorpus+PadillaCorpus # Using the previous corpus made for Cruz and Padilla in section 2.1. 
MasterCorpus$author<-docvars(MasterCorpus, "screen_name")
MasterCorpus$timestamp<-docvars(MasterCorpus, "created_at")

# Relevant cleaning steps for "Master Corpus" (same as section 2.6):
# Converting and trimming the DFM:
DFM_Master<- tokens(MasterCorpus, 
                   remove_punct = TRUE, 
                   remove_symbols = T, 
                   remove_numbers=T, 
                   remove_url=T) %>%
                   dfm() %>%
                   dfm_tolower()
# Check top words:
topfeatures(DFM_Master, n = 20)
```
<img width="449" alt="Screenshot 2022-04-20 at 15 48 52" src="https://user-images.githubusercontent.com/97530878/164258232-2a597e28-b01d-4eca-8c64-01502bf20ff6.png">


```{r}
# Remove stopwords:
DFM_Master<- dfm_remove(DFM_Master, pattern = stopwords("en"))
# Check top words:
topfeatures(DFM_Master, n = 20)
```
<img width="435" alt="Screenshot 2022-04-20 at 15 49 00" src="https://user-images.githubusercontent.com/97530878/164258266-0cde1f24-ecd7-4415-8bbb-a5e5facd0168.png">

```{r}
# Remove other irrelevant words:
DFM_Master <- dfm_remove(DFM_Master, pattern = c("cruz", "ted", "amp", "@tedcruz", "now", "just", "new", "get", "@sentedcruz", "must", "can", "time", "now", "need", "one", "us" ))
# Check word frequencies by author (keyness):
prop_dfm_byauthor<- tokens(MasterCorpus,
                           remove_punct = TRUE,
                           remove_symbols = T, 
                           remove_numbers=T,    
                           remove_url=T) %>%
                           dfm() %>%
                           dfm_tolower() %>%
                           dfm_remove(pattern = stopwords("en")) %>%
                           dfm_remove(pattern = c("cruz", "ted", "amp", "@tedcruz", "now", "just", "new", "get", "@sentedcruz", "must", "can", "time", "now", "need", "one", "us")) %>%
                          dfm_group(groups = author) %>%
                          dfm_weight(scheme = "prop")
write.csv(convert(prop_dfm_byauthor, to = "data.frame"), file="dfm_group.csv", row.names=FALSE)
```

### Word Clouds 

```{r}
par(bg = "#d5e4eb") # Changes colour of plot background. 

# Cruz Word cloud:
textplot_wordcloud(DFM_Cruz, 
                   min_count = 5,
                   max_words = 150,
                   min_size = 0.5,
                   max_size = 5,
                   random_order = FALSE, 
                   rotation = 0.25,
                   color = "red3")
```
<img width="468" alt="Screenshot 2022-04-20 at 15 49 58" src="https://user-images.githubusercontent.com/97530878/164258451-4c666cab-f8a6-4541-b263-34d70a19438a.png">
                
```{r}                   
# Padilla Word cloud:
textplot_wordcloud(DFM_Padilla, 
                   min_count = 5,
                   max_words = 150,
                   min_size = 0.25,
                   max_size = 4,
                   random_order = FALSE, 
                   rotation = 0.25,
                   color = "#1380A1")
```
<img width="465" alt="Screenshot 2022-04-20 at 15 50 19" src="https://user-images.githubusercontent.com/97530878/164258554-a4db3aff-be86-4dfa-a1e9-301705f87933.png">

### Group Analysis

```{r}
# To inspect and plot differences in word usage between two authors, see the lines of code below.
# Sort and inspect groups:
DFM_Master_Group<-dfm_sort(DFM_Master)
# Use the top 20 words: 
DFM_Master_Group<-textstat_frequency(
  DFM_Master_Group,
  n = 20,
  groups = author)
# Plot for comparison (using dfm with top 20th ranked words only): 
Fig3 <- ggplot(data = DFM_Master_Group, aes(x = factor(nrow(DFM_Master_Group):1), y = frequency)) +
    geom_point(colour = "#1380A1") +
    facet_wrap(~ group, scales = "free") +
    coord_flip() +
    scale_x_discrete(breaks = nrow(DFM_Master_Group):1,
                       labels = DFM_Master_Group$feature) +
    labs(title = "Differences in word usage between Padilla and Cruz\n",tag = "Fig.3", x = NULL, y = "Relative frequency") +   theme(plot.background = element_rect(fill = "#d5e4eb")) 

Fig3
```
<img width="468" alt="Screenshot 2022-04-20 at 15 50 51" src="https://user-images.githubusercontent.com/97530878/164258652-2dbeb786-1c29-46f0-b39e-ca1d4efd9e58.png">

The word clouds and figure 3 and section 2.6 show apparent differences between Cruz and Padilla's most used words. Cruz's most popular words included Democrats, border, vaccine, illegal and Joe Biden. These words were not in Padilla's top twenty words, and his most frequent words were people, democracy, rights, filibuster, voting and climate. Consequently, according to Zipf's Law, the difference in the politicians' top words differentiated issues and concerns Democrats and Republicans have. However, words concerning disinformation were not in either politician's top words. Thus, Zipf's Law contends that disinformation may not be a significant concern for both politicians.      

# Section 3.0 Sentiment Analysis 

Sentiment analysis is suitable over topic modelling for this report. According to Rudy Prabowo (2009), sentiment analysis is a classification method where each category represents a sentiment. These sentiments can be categorised either positively and negatively or a scale, i.e. good, satisfactory, or bad. Julia Silge (2022) claims that topic modelling is a method for classification that groups items from a document into topics. This report aims to analyse the aspect differences between Democrat and Republican politicians' sentiments in their perception of disinformation. Therefore, sentiment analysis is more applicable than topic modelling as disinformation is the chosen topic and is only interested in the politicians' perception of the topic.     

However, sentiment analysis can be carried out via dictionaries or machine learning. Dictionaries have specific categories and keywords that signal whether a document is positive or negative. In contrast, machine learning is an automated process that does not need to develop a list of keywords. Documents have been pre-labelled positive or negative and then inputted into an algorithm. The algorithm looks at those documents that the humans have pre-labelled and determines what words are associated with the category. Due to the nature of this report (not having time or pre-label documents), dictionaries will be utilised for the sentiment analysis below.

## 3.1 Sentiment Analysis with a Pre-Existing Dictionary 

```{r}
# Firstly, the pre-existing dictionary Lexicoder Sentiment Dictionary (Soroka, 2013) will be applied to the dfm, scaling texts according to their degree of positivity or negativity. However, the dictionary should only identify tweets that focus on the positive and negative views on the topic of disinformation. Thus, the code below subsets tweets using keywords related to disinformation and then applies the Lexicoder Sentiment Dictionary.

# Lexicoder Sentiment Dictionary created by Young and Soroka: Negative vs. Positive Sentiment: 
dict<-dictionary(data_dictionary_LSD2015[1:2])
# The below codeapplies the dictionary to **each individual tweet**. valuetype = glob means pattern-matching via wildcard expressions (e.g. work* will capture all variations): 
DFM_Master_Lexi<-dfm_lookup(DFM_Master, dict, valuetype = "glob")
DFM_Master_Lexi
```
<img width="481" alt="Screenshot 2022-04-20 at 15 52 26" src="https://user-images.githubusercontent.com/97530878/164259054-272ccbe0-4cf6-4163-8981-1bed1ba7ff40.png">

```{r}
DFM_Master_Lexi<-convert(DFM_Master_Lexi, to ="data.frame")
# Generates the author variable in this new DFM: 
DFM_Master_Lexi$author<- ifelse(grepl("Cruz", DFM_Master_Lexi$doc_id), "Cruz", 
                         ifelse(grepl("Padilla", DFM_Master_Lexi$doc_id), "Padilla", "NA"))

# Need to use the corpus object to do this - and apply cleaning/trimming to it:
Tokens_Master <- tokens(MasterCorpus, remove_punct = TRUE, 
                  remove_symbols = T, 
                  remove_numbers=T, 
                  remove_url=T) %>%
                  tokens_tolower() %>%
                  tokens_select(pattern = stopwords("en"), selection = "remove") %>%
                  tokens_select(pattern = c("cruz", "ted", "amp", "@tedcruz", "now", "just", "new", "get", "@sentedcruz", "must", "can", "time", "now", "need", "one", "us"), selection = "remove")

# Search for all variations of a root word - do it with the regular expression (wildcard "*") below:
info <-c('information', 'misinformation', 'disinformation', 'fake news*', 'mislead*', 'false information', 'false', 'propaganda', 'conspiracy theories*')
toks_info <- tokens_keep(Tokens_Master, pattern = phrase(info), window = 10)
# Use the toks_env object only, transform to DFM and apply the Lexicoder dictionary saved above to it: 
DFM_Info_Lexi <-dfm(toks_info)
DFM_Info_Lexi<-dfm_lookup(DFM_Info_Lexi, dict, valuetype = "glob")
DFM_Info_Lexi
```
<img width="483" alt="Screenshot 2022-04-20 at 15 52 59" src="https://user-images.githubusercontent.com/97530878/164259170-dfd70f3a-c8db-456d-8d07-9d6b7770910f.png">

```{r}
DFM_Info_Lexi<-convert(DFM_Info_Lexi, to ="data.frame")

# Generates the author variable in this new DFM:
DFM_Info_Lexi$author<- ifelse(grepl("Cruz", DFM_Info_Lexi$doc_id), "Cruz", 
                         ifelse(grepl("Padilla", DFM_Info_Lexi$doc_id), "Padilla", "NA"))
# Plots and summaries (pos):
InfoPosTweets1<-ggplot(DFM_Info_Lexi,aes(positive, group = author)) + 
          geom_bar(aes(y = ..prop.., fill = factor(..x..)), stat="count") + 
          scale_y_continuous(labels=scales::percent) +
          ylab("Relative frequencies\n") +
          xlab("N of positive mentions per Tweet\n") +
          ggtitle ("Mis-Disinformation Topic\n", subtitle = 'Number of positive mentions per Tweet\n') +
          labs(tag = 'Fig.6') + 
          scale_fill_brewer(palette = 'Paired') +
          theme(plot.background = element_rect(fill = "#d5e4eb"))+
          facet_grid(~author)

# Plots and summaries (neg):
InfoNegTweets1<-ggplot(DFM_Info_Lexi,aes(negative, group = author)) + 
          geom_bar(aes(y = ..prop.., fill = factor(..x..)), stat="count") + 
          scale_y_continuous(labels=scales::percent) +
          ylab("Relative frequencies\n") +
          xlab("N of negative mentions per Tweet\n") +
          ggtitle ("Mis-Disinformation Topic\n", subtitle = 'Number of negative mentions per Tweet\n') +
          labs(tag = 'Fig.7') + 
          scale_fill_brewer(palette = 'Paired') +
          theme(plot.background = element_rect(fill = "#d5e4eb"))+
          facet_grid(~author)

InfoPosTweets1
```
<img width="466" alt="Screenshot 2022-04-20 at 15 54 20" src="https://user-images.githubusercontent.com/97530878/164259455-e390bcc6-8501-4d6c-8858-08adfe2acf21.png">

```{r}
DFM_Info_Lexi %>% 
  group_by(author) %>% 
  summarize(mean = mean(positive),
            sum = sum(positive))
```
<img width="147" alt="Screenshot 2022-04-20 at 15 54 40" src="https://user-images.githubusercontent.com/97530878/164259537-9f78bc73-39ab-44c8-b15f-d7ff00a10a82.png">

```{r}
InfoNegTweets1
```
<img width="467" alt="Screenshot 2022-04-20 at 15 55 05" src="https://user-images.githubusercontent.com/97530878/164259629-2a5691bf-fc3a-4674-ab26-a5a09aac344b.png">

```{r}
DFM_Info_Lexi %>% 
  group_by(author) %>% 
  summarize(mean = mean(negative),
            sum = sum(negative))
```
<img width="152" alt="Screenshot 2022-04-20 at 15 55 24" src="https://user-images.githubusercontent.com/97530878/164259685-31b7aa14-a840-4765-a04a-9ab989ede8d2.png">

```{r}
# Figures 6 and 7 are difficult to interpret as both politicians have a high proportion of tweets not featuring any positive or negative words regarding disinformation. Consequently, figures 8 and 9 coded below show the number of positive and negative mentions per tweet but only contains non-zero entries. 

# Only considering non-zero entries in either sentiment variable (e.g. only considering disinformation tweets where (any) sentiment expressed): 
DFM_Info_Lexi_Non0<- filter(DFM_Info_Lexi, negative > 0 | positive >0)

# Plots and summaries (pos):
InfoPosTweets2<-ggplot(DFM_Info_Lexi_Non0,aes(positive, group = author)) + 
          geom_bar(aes(y = ..prop.., fill = factor(..x..)), stat="count") + 
          scale_y_continuous(labels=scales::percent) +
          ylab("Relative frequencies") +
          xlab("N of positive mentions per Tweet") +
          ggtitle ("Mis-Disinformation Topic\n", subtitle = 'Number of positive mentions per Tweet (non-zero entries)\n') +
          labs(tag = 'Fig.8') + 
          scale_fill_brewer(palette = 'Paired') +
          theme(plot.background = element_rect(fill = "#d5e4eb"))+
          facet_grid(~author)

# Plots and summaries (neg):
InfoPosTweets3<-ggplot(DFM_Info_Lexi_Non0,aes(negative, group = author)) + 
          geom_bar(aes(y = ..prop.., fill = factor(..x..)), stat="count") + 
          scale_y_continuous(labels=scales::percent) +
          ylab("Relative frequencies\n") +
          xlab("N of negative mentions per Tweet\n") +
          ggtitle ("Mis-Disinformation Topic\n", subtitle = 'Number of negative mentions per Tweet (non-zero entries)\n') +
          labs(tag = 'Fig.9') + 
          scale_fill_brewer(palette = 'Paired') +
          theme(plot.background = element_rect(fill = "#d5e4eb")) +
          facet_grid(~author)

InfoPosTweets2
```
<img width="466" alt="Screenshot 2022-04-20 at 15 55 57" src="https://user-images.githubusercontent.com/97530878/164259814-ed3b024e-d36a-4915-aeca-8f11faf5669d.png">

```{r}
DFM_Info_Lexi_Non0 %>% 
  group_by(author) %>% 
  summarize(mean = mean(positive),
            sum = sum(positive))
```
<img width="148" alt="Screenshot 2022-04-20 at 15 56 18" src="https://user-images.githubusercontent.com/97530878/164259910-28a807a7-1c9b-41f1-a67f-83b5676d3182.png">

```{r}
InfoPosTweets3
```
<img width="465" alt="Screenshot 2022-04-20 at 15 56 45" src="https://user-images.githubusercontent.com/97530878/164260004-0e19e786-467e-4da6-8bad-86d7f5c730d1.png">

```{r}
DFM_Info_Lexi_Non0 %>% 
  group_by(author) %>% 
  summarize(mean = mean(negative),
            sum = sum(negative))
```
<img width="138" alt="Screenshot 2022-04-20 at 15 57 14" src="https://user-images.githubusercontent.com/97530878/164260095-cc61b503-0fd4-499e-b6c6-d5e3c1ae7285.png">

```{r}

# Exploring a specific texts: 

# Cruz 5 most negative texts:
CruzTweets$text[2041]
CruzTweets$text[2050] 
CruzTweets$text[2403] 
CruzTweets$text[2542] 
CruzTweets$text[923] 
```
<img width="545" alt="Screenshot 2022-04-20 at 15 58 13" src="https://user-images.githubusercontent.com/97530878/164260370-28384823-9849-446b-99ed-b595820e59ba.png">

```{r}
# Padilla 5 most negative texts:
PadillaTweets$text[1248] 
PadillaTweets$text[555] 
PadillaTweets$text[1348] 
PadillaTweets$text[350] 
PadillaTweets$text[1230] 
```
<img width="543" alt="Screenshot 2022-04-20 at 15 59 08" src="https://user-images.githubusercontent.com/97530878/164260553-07b7b8e5-e395-404f-bb77-f9d3b68f1505.png">

```{r}
# Cruz 5 most positive texts:
CruzTweets$text[1457] 
CruzTweets$text[2994] 
CruzTweets$text[404]
CruzTweets$text[2909] 
CruzTweets$text[2041] 
CruzTweets$text[2050]
```
<img width="544" alt="Screenshot 2022-04-20 at 15 59 28" src="https://user-images.githubusercontent.com/97530878/164260627-064de12e-01ba-48b4-9984-f0d6cd1405b8.png">

```{r}
# Padilla 5 most positive texts:
PadillaTweets$text[13] 
PadillaTweets$text[2536] 
PadillaTweets$text[1054] 
PadillaTweets$text[621] 
PadillaTweets$text[2999] 
```
<img width="541" alt="Screenshot 2022-04-20 at 15 59 52" src="https://user-images.githubusercontent.com/97530878/164260726-b8f1ccbe-4870-4a1d-933e-fa33da7bde9f.png">

Figure 8 illustrates that both politicians have a high proportion of tweets not featuring any positive words, whereas figure 9 exhibits a low portion of tweets not containing any negative words concerning disinformation. However, figures 8 and 9 show that Padilla tweets more positive and negative words (91 positive,188 negative) than Cruz (43 positive, 82 negative). Additionally, on average, Padilla tweets more positive and negative words per tweet (1.2 positive, 2.4 negative) than Cruz (0.98 positive, 1.9 negative). Therefore, it can be contended that Padilla has more emotive language than Cruz by having more positive and negative words per tweet. 

Furthermore, both politicians have a higher proportion of tweets featuring negative words than positive words concerning disinformation. Therefore, both politicians' texts can be classified as substantially negative towards disinformation by using ominous language. There is a clear message that disinformation needs to be combated. A Pew Research (2019) report reveals that 68% of Americans consider disinformation significantly impacts Americans' confidence in government institutions, forming one of the biggest problems in the U.S., ranking above climate change, terrorism and racism. Thus, the hostile stance on disinformation is expected as politicians take popular stances on issues to seek re-election.   

However, analysing both politicians' most positive and negative Tweets, there is a difference in context between the politicians on the issue of disinformation. Cruz appears to use positive and negative texts to criticise actors he believes are spreading disinformation. Furthermore, Hunt Allcott (2017) proclaims that Trump would not have been elected president without disinformation. Therefore, it can be argued that Cruz employs similar tactics as Trump by declaring opposition information as fake news and for political point-scoring. 


Conversely, Padilla uses both negative and positive text to highlight the issues that disinformation causes and that the radical right is causing the disinformation. Padilla's texts echo academics' and scientists' sentiments. Numerous academics and scientists (Stengel, 2020; Nikolov, 2021; Allcott, 2017) have proclaimed that disinformation is primarily spread among politically right ideologies, causing harm to democracy and public health. For instance, Pereira (2020) and Bell (2022) assert that disinformation on Covid-19 and vaccines inhibited the Democrat government's efforts to combat Covid-19, leading to more deaths. Therefore, Padilla and other Democrats see disinformation impeding their ability to tackle prominent issues, thus is a prevalent issue for Democrats to combat.

Consequently, using the pre-existing dictionary that uses general negative and positive is efficient to analyse the aspect differences between Democrat and Republican politicians' sentiments on disinformation. Overall, Cruz is more confrontational regarding disinformation by calling out and labelling disinformation. Whereas Padilla is more observational by highlighting the issues disinformation causes. Evidently, disinformation is a bipartisan issue as both politicians see disinformation as being negative—for instance, 2016 Countering Foreign Propaganda and Disinformation and 2021 Covid-19 Disinformation Research and Reporting Acts. Therefore, in theory, disinformation is more likely to be opposed. Nevertheless, the rise in polarisation creates the problem of determining disinformation. Hence in practice, disinformation may be harder to combat. 

# Bibliography
Deen Freelon, A. M. D. K., 2021. False equivalencies: Online activism from left to right. Science, 6508(369), pp. 1197-1201.

Dimitar Nikolov, A. F. ,. F. M., 2021. Right and left, partisanship predicts (asymmetric) vulnerability to misinformation. Harvard Kennedy School Misinformation Review, 1(7), pp. 1-13.

Gentzkow, H. A. a. M., 2017. Social Media and Fake News in the 2016 Election. Journal of Economic Perspectives, 31(2), pp. 211-236.

Jang, M. J.-., 2020. Perceptions of mis-or disinformation exposure predict political cynicism: Evidence from a two-wave survey during the 2018 US midterm elections. New Media & Society , pp. 1-21.

Joshua A. Tucker, A. G. P. B. C. V. A. S. S. S. D. S. a. B. N., 2018. Social Media, Political Polarization, and Political Disinformation: A Review of the Scientific Literature, s.l.: Hewlett Foundation.

Juckett, D., 2012. A method for determining the number of documents needed for a gold standard corpus. Journal of Biomedical Informatics, 45(3), pp. Pages 460-470.

Karishma Sharma, E. F. Y. L., 2021. Characterizing Online Engagement with Disinformation and Conspiracies in the 2020 U.S. Presidential Election, s.l.: University of Southern California.

Pedro Silveira Pereira, A. d. S. S. a. A. P., 2020. Disinformation and Conspiracy Theories in the Age of COVID-19. frontiers in Sociology.

Pew Research Center, 2019. Many Americans Say Made-Up News Is a Critical Problem That Needs To Be Fixed. [Online] 
Available at: https://www.pewresearch.org/journalism/2019/06/05/many-americans-say-made-up-news-is-a-critical-problem-that-needs-to-be-fixed/ [Accessed 9 Febuary 2020].

Robinson, J. S. a. D., 2022. Topic modeling. [Online] Available at: https://www.tidytextmining.com/topicmodeling.html [Accessed 1 March 2022].

Rudy Prabowo, M. T., 2009. Sentiment analysis: A combined approach. Journal of Informetrics, 3(2), pp. 143-157.

Statista , 2022. Fake news in the U.S. - statistics & facts. [Online] Available at: https://www.statista.com/topics/3251/fake-news/#dossierKeyfigures [Accessed 9 Febuary 2022].

Statista, 2022. Most active members of the U.S. Senate on Twitter in 2021, by number of tweets. [Online] Available at: https://www.statista.com/statistics/958868/most-active-members-senators-twitter-usa/#statisticContainer [Accessed 1 March 2022].

Stengel, R., 2020. Domestic Disinformation Is a Greater Menace Than Foreign Disinformation. [Online] Available at: https://time.com/5860215/domestic-disinformation-growing-menace-america/ [Accessed 9 Febuary 2022].

The Times , 2022. The Times view on criticism of the Oxford-AstraZeneca vaccine: Medical Misrepresentation. [Online] Available at: https://www.thetimes.co.uk/article/f42d5be6-8910-11ec-a837-0153f5f4adaf?shareToken=e537e1336c3b1b9a88e97f89527a766e [Accessed 9 Febuary 2022].

Young, L. and Soroka, S. 2012. Lexicoder Sentiment Dictionary










