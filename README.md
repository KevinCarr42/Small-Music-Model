# Small Music Model

A generative pretrained transformer used to generate chord progressions.

## Project Outline
1. Chord progression data downloaded using a modified version of the scraper from this repo: [Progressions by Ljfernando](https://github.com/Ljfernando/Progressions)
2. [Chord progression data cleaned](chord_progressions.ipynb)
3. [Training and validation sets created, as well as encodings](data_preparation.ipynb)
4. Generative pretrained transformer models created and trained
    * Create the [transformer architecture](model.py) based on tutorials from [Andrej Karpathy](https://github.com/karpathy)
    * [Train the transformer models](train_chord_transformer.ipynb)
5. [Datasets created](SMM_create_datasets.ipynb) using each of the trained models
6. [Exploratory Data Analysis](SMM_explortatory_data_analysis.ipynb) performed to visualise the predictions from the transformer models and compare to the data from UltimateGuitar.com

## Project Details

### [1. Data Source](data/UltimateGuitarTabs_chords.csv)

This model was trained using data from [Ultimate-Guitar.com](https://www.ultimate-guitar.com/), which was scraped using a modified version of the scraper from following GitHub repository:

[Progressions by Ljfernando](https://github.com/Ljfernando/Progressions)

The chords for every available song was downloaded and added to the initial dataset.

### [2. Data Cleaning](chord_progressions.ipynb)

Using pandas and python, chord progressions were extracted based on repetition within songs. Each progression was split into a separate instance, and cleaned to avoid repetition of chords, or groups of chords.

All chord progressions were converted into the key of C / Am in order to eliminate variation irrelevant to the chord progression itself. These transformations included some simplifying assumptions such as converting diminished chords to minor chords while determining keys (detailed chords were maintained in datasets, simplified chords were only used to determine keys). 

Not all chord progressions were able to be transposed, because not all chord progressions can be labelled with one single key. Although these non-standard progressions are relatively common in popular music, they were not included in the training or validation data. This resulted in excluding approximately 15% of the chord progressions. This limitation is discussed in the Discussion and Conclusion section below.

### [3. Train Test Split / Encoding](data_preparation.ipynb)

Every chord progression was appended with an "end of sequence" token to identify the end of a chord progression. 

Each potential chord was added to a set of tokens for encoding and decoding (there ended up being exactly 128 tokens, which was just a happy accident). 

Data were split into train and test sets using a ratio of 10% for validation data. Data were shuffled (default for sklearn train_test_split) prior to splitting.

### [4. Transformer Model](train_chord_transformer.ipynb)

The [transformer architecture](model.py) was based on a simplified version of GPT2 as demonstrated in the excellent tutorial by Andrej Karpathy: [Let's build GPT: from scratch, in code, spelled out.](https://youtu.be/kCc8FmEb1nY?si=4a5qvl9HrctZAQcs)

[Four versions of the model were trained](train_chord_transformer.ipynb) to evaluate learning rate as well as under and overtraining. Learning rates of 1e-3 and 1e-4 were each evaluated with both 1,000 and 10,000 training steps. 

The model with a learning rate of 1e-3 and 10,000 training steps showed divergence between training and validation losses near the end of the training, with validation losses increasing, indicating overtraining. This model is referred to as the overtrained model.

Conversely, the model with a learning rate of 1e-4 and 1,000 training steps was still showing marked improvements when training was terminated. This model is referred to as the undertrained model.

### [5. Datasets Created](SMM_create_datasets.ipynb)

Using the trained models from the previous step, four datasets were created, each with the approximate size of the initial training dataset created in steps 1 and 2.

### [6. Exploratory Data Analysis](SMM_explortatory_data_analysis.ipynb)

Comparing the dataset from step 2 to the predicted data from step 5, chord progressions were compared and visualised.

Predicted chord progressions were first cleaned as in step 2 to eliminate repetition and condense larger compound chord progressions into the smallest possible chord progressions. Any progressions less than 2 chords long were eliminated (although 1 chord progressions are possible in music, these results don't seem meaningful). All datasets were sliced to the same number of rows, to simplify comparisons.

#### Common Chord Progressions

Common chord progressions were compared between each of the datasets. Unsurprisingly, the most common chord progressions from the trained models were similar between the models, with the most common progressions being the extremely common [I V vi IV  /  vi IV I V progression](https://en.wikipedia.org/wiki/I–V–vi–IV_progression). Interestingly, the overtrained model generated the I V vi IV progressions more often than the base dataset, whereas the undertrained model generated them significantly less often.

#### Uncommon Chord Progressions

By comparing all datasets, a subset of chord progressions which do not appear in the training data was generated. Not surprisingly, these 'uncommon' chord progressions are dominated by the undertrained generative transformer model.

A limitation to this analysis is that a number of these chord progressions are valid in multiple keys, so if the tonic of the chord progression is not present in the progression, it is more likely to be mis-transposed during step 2. Since keys cannot easily be validated using vocal melodies or similar, this issue has no easy solution, and it is likely that a number of these uncommon progressions are actually more common than this Exploratory Data Analysis indicates.

## Discussion and Conclusions

Although this was an interesting project, the generative pretrained transformer model does not seem to be the best method for generating chord progressions. 

The main limitation being the training dataset, and that chord progressions that are not easily transposed are often more interesting musically. Often this is because the progression will modulate between multiple keys. An option to deal with this limitation could be to manually add chord progressions with modulation in the training data set. This would bias the training set in a selected direction, but would require quite a bit of manual work.

A future extension to this project would be to include timing data with the chord data (strumming patterns or similar). That method would likely be far more difficult due to difficultly obtaining the training data, and may need to be compiled manually. A brief investigation did not turn up any options for easily downloading more detailed musical data in bulk.

Music theory and Markov Chains are likely to eliminate most of the above issues, and could lead to interesting results. These methods allow for easily including methods for modulation, more obscure chords, and an Object-Oriented approach could even allow for the inclusion of timing data / strumming patterns.
