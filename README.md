# Named Entity Recognition

## AIM

To develop an LSTM-based model for recognizing the named entities in the text.

## Problem Statement and Dataset
This experiment aims to build an LSTM-based neural network model for named entity recognition utilizing bidirectional LSTM-based model. The dataset contains numerous sentences, each with numerous words and their corresponding tags. To train our model, we vectorize these phrases utilizing Embedding layer method.Recurrent neural networks that operate in both directions (bi-directional) and can link two or more hidden layers to a single output. The output layer can simultaneously receive input from past and future states to predict the next word.



## DESIGN STEPS

![372019022-9e273dbd-0d93-4281-ae93-e12f27da8ec6](https://github.com/user-attachments/assets/4df3ca04-8843-460b-98af-a52b249af7e6)

## STEP 1:
Import the necessary packages.

## STEP 2:
Read the dataset, and fill the null values using forward fill.

## STEP 3:
Create a list of words, and tags. Also find the number of unique words and tags in the dataset.

## Step 4 :
Create a dictionary for the words and their Index values. Do the same for the tags as well,Now we move to moulding the data for training and testing.

## Step 5 :
We do this by padding the sequences,This is done to acheive the same length of input data.

## Step 6 :
We build a build a model using Input, Embedding, Bidirectional LSTM, Spatial Dropout, Time Distributed Dense Layers.

## Step 7 :
We compile the model and fit the train sets and validation sets,We plot the necessary graphs for analysis,A custom prediction is done to test the model manually.

## PROGRAM
### Name:BALAJI J
### Register Number:212221243001

```
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
from tensorflow.keras.preprocessing import sequence
from sklearn.model_selection import train_test_split
from keras import layers
from keras.models import Model

data = pd.read_csv("ner_dataset.csv", encoding="latin1")
data.head(50)
data = data.fillna(method="ffill")
data.head(50)
print("Unique words in corpus:", data['Word'].nunique())
print("Unique tags in corpus:", data['Tag'].nunique())
words=list(data['Word'].unique())
words.append("ENDPAD")
tags=list(data['Tag'].unique())
print("Unique tags are:", tags)
num_words = len(words)
num_tags = len(tags)
num_words
class SentenceGetter(object):
    def __init__(self, data):
        self.n_sent = 1
        self.data = data
        self.empty = False
        agg_func = lambda s: [(w, p, t) for w, p, t in zip(s["Word"].values.tolist(),
                                                           s["POS"].values.tolist(),
                                                           s["Tag"].values.tolist())]
        self.grouped = self.data.groupby("Sentence #").apply(agg_func)
        self.sentences = [s for s in self.grouped]

    def get_next(self):
        try:
            s = self.grouped["Sentence: {}".format(self.n_sent)]
            self.n_sent += 1
            return s
        except:
            return None
getter = SentenceGetter(data)
sentences = getter.sentences
len(sentences)
sentences[0]
word2idx = {w: i + 1 for i, w in enumerate(words)}
tag2idx = {t: i for i, t in enumerate(tags)}
word2idx
print("BALAJI ")
print("2122212343001")
print("Length of each sentences")
plt.hist([len(s) for s in sentences], bins=50)
plt.show()X1 = [[word2idx[w[0]] for w in s] for s in sentences]
type(X1[0])
X1[0]
max_len = 55
X = sequence.pad_sequences(maxlen=max_len,
                  sequences=X1, padding="post",
                  value=num_words-1)
X[0]
y1 = [[tag2idx[w[2]] for w in s] for s in sentences]

y = sequence.pad_sequences(maxlen=max_len,
                  sequences=y1,
                  padding="post",
                  value=tag2idx["O"])
X_train, X_test, y_train, y_test = train_test_split(X, y,
                                                    test_size=0.3, random_state=1)
X_train[0]

y_train[0]
input_word = layers.Input(shape=(max_len,))
embedding_layer=layers.Embedding(input_dim=num_words,output_dim=55,input_length=max_len)(input_word)
dropout_layer=layers.SpatialDropout1D(0.2)(embedding_layer)
bidirectional_lstm=layers.Bidirectional(
    layers.LSTM(units=100,return_sequences=True))(dropout_layer)
output=layers.TimeDistributed(
    layers.Dense(num_tags,activation="softmax"))(bidirectional_lstm)
ent_name = Model(input_word, output)
ent_name.summary()
ent_name.compile(optimizer="adam",
              loss="sparse_categorical_crossentropy",
              metrics=["accuracy"])
history = ent_name.fit(
    x=X_train,
    y=y_train,
    validation_data=(X_test,y_test),
    batch_size=32,
    epochs=5,
)
metrics = pd.DataFrame(ent_name.history.history)
metrics.head()
print("BALAJI")
print("212221243001")
print("Accuracy VS validation accuracy")
metrics[['accuracy','val_accuracy']].plot()
print("BALAJIJ")
print("212221243001")
print("Loss VS Validation loss")
metrics[['loss','val_loss']].plot()
i = 30
p = ent_name.predict(np.array([X_test[i]]))
p = np.argmax(p, axis=-1)
y_true = y_test[i]
print("=" *40)
print("BALAJI\n212221243001")
print("=" *40)
print("{:15}{:5}\t {}\n".format("Word", "True", "Pred"))
print("-" *40)
for w, true, pred in zip(X_test[i], y_true, p[0]):
    print("{:15}{}\t{}".format(words[w-1], tags[true], tags[pred]))
```


## OUTPUT

### Training Loss, Validation Loss Vs Iteration Plot


![D46](https://github.com/user-attachments/assets/d7948226-5c6b-4356-bac8-44e1587580cb)

### Sample Text Prediction


![D47](https://github.com/user-attachments/assets/35605939-47a8-44b1-be3f-70328ec40b28)
s/assets/6e0d6552-11af-4ceb-9c05-c06eeead33d8)

![D48](https://github.com/user-attachments/assets/f973dd0c-2195-4115-bb8e-b21876634b6e)

## RESULT

Thus, an LSTM-based model (bi-directional) for recognizing the named entities in the text is developed Successfully.
