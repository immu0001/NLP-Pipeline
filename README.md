# NLP-Pipeline

A Simple Natural language processing pipeline implementation project as part of the udacity data scientist nano degree

## Workflow:

The most frustrating part of doing NLP for me is keeping track of all the
different combinations of cleaning functions, stemmers, lemmatizers,
vectorizers, models, etc. I almost always resort to writing some awful
function that hacks those bits together and then prints out some scoring
piece. To help manage all of this better, I've developed a pipelining system
that allows the user to load all of the pieces into a class and then let the
class do the management for them. 

### Installation

Clone this repo. Go to the directory where it is cloned and run:

```bash
python setup.py install
```

nlp_pipeline_manager will then install to your machine and be available. This project
assumes python 3 and requires NLTK and SkLearn.


### Examples of using the pipeline:

```python
from nlp_pipeline_manager import nlp_preprocessor

corpus = ['BOB the builder', 'is a strange', 'caRtoon type thing']
nlp = nlp_preprocessor()
nlp.fit(corpus)
nlp.transform(corpus).toarray()
```

```bash
array([[1, 1, 0, 0, 0, 1, 0, 0],
       [0, 0, 0, 1, 1, 0, 0, 0],
       [0, 0, 1, 0, 0, 0, 1, 1]])
```

Loading a stemmer into the pipeline (we actually pass in the stemming method):

```python
from nltk.stem import PorterStemmer

nlp = nlp_preprocessor(stemmer=PorterStemmer().stem)
```

The pipeline allows users to set:

* Vectorizer (using SkLearn classes)
* Cleaning Function (user can just provide a function name without the parens
at the end)
* Tokenizer (Either an NLTK tokenizer or a function that takes a string and
returns a list of tokens)
* Stemmer (Can be any function that takes in a word and returns a root form - be it a stemmer or a lemmatizer)

If the user wants to provide a cleaning function, it must accept 3 arguments.
The text, the tokenizer, and the stemmer. Here's an example extra cleaning
function:

```python
def clean_text(text, tokenizer, stemmer):
        """
        A naive function to lowercase all words and clean them quickly
        with a stemmer if it exists.
        """
        cleaned_text = []
        for post in text:
            cleaned_words = []
            for word in tokenizer(post):
                low_word = word.lower()
                if stemmer:
                    low_word = stemmer(low_word)
                cleaned_words.append(low_word)
            cleaned_text.append(' '.join(cleaned_words))
        return cleaned_text
```

# ML with the pipeline

It's quick and easy to create modeling pipelines that wrap around the
preprocessor. Two example pipes are shown, one for classification and one for
topic modeling. Here's an example of using the classification pipe:

```python
from nlp_pipeline_manager import supervised_nlp
from nlp_pipeline_manager import nlp_preprocessor
from sklearn.naive_bayes import MultinomialNB

nlp = nlp_preprocessor()

nlp_pipe = supervised_nlp(MultinomialNB(), nlp)
nlp_pipe.fit(ng_train_data, ng_train_targets)
nlp_pipe.score(ng_test_data, ng_test_targets)
```

