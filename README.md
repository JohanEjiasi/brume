## Dependencies
- python 3.x
- nltk
- numpy
- sklearn
- scipy
- pandas
- spacy

We have included a conda environment yaml. To install, run: 

        conda env create -f environment.yml

## Data

For the purpose of doing research on news items, this project uses a [dataset produced by Signal Media](http://research.signalmedia.co/newsir16/signal-dataset.html) in connection with the Recent Trends in News Information Retrieval 2016 conference. To identify between 'legitimate' and 'false' news sources, we employ [OpenSources.co](http://opensources.co). 

## Feature Generation

The following features are generated from the original article text:

1. A vectorized bigram term frequency-inverse document frequency that has undergone preprocessing to remove identified entities (people, locations, etc.) and replace them with anonymous placeholders (e.g., "Donald Trump" --> "-PERSON-"). Tokenization and entity recognition are handled by Spacy, and TFIDF vectorization is handled by SkLearn.
2. The frequency of parsed syntacical dependencies was normalized. Once more, we employ SkLearn for vectorization and Spacy for parsing. Here is a fantastic interactive visualization of Spacy's dependency parser: https://demos.explosion.ai/displacy/.

## Pipeline

The pipeline contains four sets of python code:

- `model.py`: a class for models
- `model_loop.py`: a class for running a loop of classifiers; takes test-train data splits and various run params
- `run.py`: this code implements the model_loop class; it also implements our re-sampling of the data
- `transform_features.py`: this file executes all feature generation

Running the code is done through `run.py` with the following options:

1. `--models`: models to run
2. `--iterations`: number of tuning parameter iterations to run per model
3. `--output_dir`: a directory name to store the output; system will create the directory
4. `--dedupe`: whether to look for and remove duplicate content
5. `--features`: which feature set to run with, options include:  

    - `both_only`: runs both PCFG and TFIDF
    - `grammar_only`: runs only PCFG
    - `tfidf_only`: runs only TFIDF
    - `all`: runs all the above

## Example Pipeline Run

To execute the pipeline with Logistic Regression and Stochastic Gradient Descent, navigate to the pipeline directory, run:

```
source activate amlpp
python run.py /path/to/data --models LR SGD --iterations 50 --output_dir run_name --dedupe --reduce 500 --features both_only
```
This is encapsulated in the `run.sh` file. 

The first argument is the path to the input datafile. The pipeline assumes that the text of each article is unique. If your texts are not unique, use the flag --dedupe to automatically remove duplicated articles during preprocessing. To see a description of all arguments, run:

```
python run.py --h
```

A simple report on the models run with basic evaluation metrics will be output to the output/ directory (unless another output directory is specified).