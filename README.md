# Small Music Model

A generative pretrained transformer used to generate chord progressions.

### Project Outline
1. Download data using a modified version of the scraper from this repo: [Progressions by Ljfernando](https://github.com/Ljfernando/Progressions)
2. [Clean the chord progression data](chord_progressions.ipynb)
3. [Create a training and validation set, as well as encoding data](data_preparation.ipynb)
4. Create the generative pretrained transformer models
    * Create the [transformer architecture](model.py) based on tutorials from [Andrej Karpathy](https://github.com/karpathy)
    * [Train the transformer models](train_chord_transformer.ipynb)
5. [Create datasets](SMM_create_datasets.ipynb) using each of the trained models
6. [Exploratory Data Analysis](SMM_explortatory_data_analysis.ipynb) to visualise the results of the transformer models and compare to data from UltimateGuitar.com

## Training Data

### [1. Source](data/UltimateGuitarTabs_chords.csv)

This model was trained using data from [Ultimate-Guitar.com](https://www.ultimate-guitar.com/), which was scraped using a modified version of the scraper from following github repository:

[Progressions by Ljfernando](https://github.com/Ljfernando/Progressions)

The chords for every available song was downloaded and added to the initial dataset.

### [2. Data Cleaning](chord_progressions.ipynb)

Using pandas and python, chord progressions were extracted based on repetition within songs. Each progression was split into a separate instance, and cleaned to avoid repetition of chords, or groups of chords.
All chord progressions were converted into the key of C / Am in order to eliminate variation irrelevant to the chord progression itself. These transformations included some simplifying assumptions such as converting diminished chords to minor chords while determining keys. Not all chord progressions were able to be transposed, because non-standard progressions are relatively common in popular music. These un-transposable progressions were not included in the training or test data.

### [3. Train Test Split / Encoding](data_preparation.ipynb)

