
<style>
.reveal section p {
  color: black;
  font-size: .7em;
  font-family: 'Helvetica'; #this is the font/color of text in slides
}


.section .reveal .state-background {
    background: white;}
.section .reveal h1,
.section .reveal p {
    color: black;
    position: relative;
    top: 4%;}

.wrap-url pre code {
  word-wrap:break-word;
}

</style>


Chat Bots and Language Generation
========================================================
author: Chris Bail 
date: Duke University
autosize: true
transition: fade  
  website: https://www.chrisbail.net  
  github: https://github.com/cbail  
  Twitter: https://www.twitter.com/chris_bail


Agenda for today
========================================================
&nbsp;

1. Task-oriented chatbots
2. Conversational chatbots
3. Language generation using Long Short-Term Memory (LSTM) Network


What is a task-oriented chatbot?
========================================================
&nbsp;

NLP has been used to create a variety of *task-oriented* technologies that provide answers to questions or respond to directions from human speech. Popular examples inclulde Alexa, Siri, and Google Assistant.

Example from Siri
========================================================
&nbsp;

<img src="siri.png" height="400" />

How do they work?
========================================================
&nbsp; 

https://dialogflow.com

<img src="dialogueflow.png" height="400" />



Example workflow of task-oriented chat bot
========================================================
&nbsp; 

1. Identify named entities 
2. Information or relation extraction 
3. Pass results to search API
4. Deliver results of research using templates (e.g. "Here's what I found:")


========================================================

# **Conversational Chat Bots**



What is a conversational chatbot?
========================================================
&nbsp; 


Chat bots can also be built with the goal of engaging in casual dialogue with a human.


The Turing Test
========================================================
&nbsp; 

Alan Turing was a pioneering British computer scientist who played a key role on code-cracking during WWII. He also proposed the well known "Turing test" which he proposed in the 1950s in order to determine whether computers could effectively mimic human beings

========================================================

<img src="goostman.jpg" height="400" />


An Example: Cleverbot
========================================================
&nbsp; 

Created in the 1980s-- rule based, but also learned from >120 million lines of conversation:

https://www.cleverbot.com/



Even creepier
========================================================
&nbsp; 

https://www.eviebot.com/en/


Cleverbot Tested
========================================================
&nbsp;

At a 2011 confernece in Guwahati, India, 30 people voted on how human cleverbot was vs. humans engaging in a chat on a large screen at the conference. Cleverbot was judged to be 59.3% human and humans were judged to be 63.3%


Mitsuku
========================================================
&nbsp; 

https://www.pandorabots.com/mitsuku/


In-class exercise
========================================================
&nbsp; 

Take 5 minutes and try out one of the chatbots: where does it excel, and where does it fail?


Rule-based chatbots
========================================================
&nbsp; 

Rule-based chatbots
========================================================
&nbsp; 

Rule-based chatbots have been around for a very long time. Perhaps the most famous is ELIZA, created by Weizenbaum in 1966 in order to perform "Rogerian" psychology,

Sample Dialogue from ELIZA
========================================================
&nbsp; 

Human: Men are all alike.  
ELIZA: IN WHAT WAY  
Human: They're always bugging us about something or other.  
ELIZA: CAN YOU THINK OF A SPECIFIC EXAMPLE?  
Human: Well, my boyfriend made me come here.  
ELIZA: YOUR BOYFRIEND MADE YOU COME HERE  
Human: He says I'm depressed much of the time.  
ELIZA: I AM SORRY TO HEAR YOU ARE DEPRESSED.

ELIZA's key function
========================================================
&nbsp; 
From Jurafsky and Martin (2018):

<img src="ELIZA.png" height="400" />


If no keyword matches, ELIZA uses some generic response such as "Please go On," or "I See." Also, anytime the human uses the word "my" ELIZA chooses a memory phrase such as "Let's discuss further why your XXXXX"

ELIZA
========================================================
&nbsp; 

ELIZA did not pass the Turing test, but people did form deep bonds with it (people disclosed highly personal information to ELIZA). Several years later, a chatbot called PARRY successful passed the Turing test (but was impersonating people with schizophrenia, and it was psychologists who could not distinguish the bot from the person)


Cleverbot's rules
========================================================
&nbsp;

<img src="rules.png" height="400" />

Corpus-Based chatbots
========================================================
&nbsp;

Chatbots can also produce text by drawing upon corpora of human conversations (e.g. Twitter or Weibo), or from "training data" where humans interact with chatbots.

As Jurafsky and Martin write, corpus-based chatbots typically either draw upon some form of information retrieval or supervised machine learning such as sequence translation.

Information Retrieval in Corpus-based Chatbots
========================================================
&nbsp;

In a corpus of real conversations, find the "response" with the highest cosine similarity to the text inputed by the human, and return the response to that statement in the corpus. Chatbots such as cleverbot use a somewhat more sophisticated strategy where named entities and other information are mined from the statement, and then the matching is conducted upon these arguably more meaningful parts of a human statement.


Sequence to sequence chatbots
========================================================
&nbsp;

Enter machine learning: people first thought machine translation would be useful, but it turns out that most real human responses do not use the same words that might appear in a human statement. Instead, responses are based upon sequences of words. Again, Jurafsky and Martin (2018):

Sequence to sequence chatbots
========================================================
&nbsp;

One issue is the phrases returned from the objective function of a neural net are often very repetitive (e.g. "oh yeah") and therefore a bot based upon sequence2sequence can be boring and repititive. A solution to this problem was developed by allowing learning from previous turns in the conversation (so that the neural net can produce a better model of the prior context of the conversation)


<img src="seq2sec.png" height="400" />



Tutorial for building a rule-based chatbot in R on Slack:
========================================================
&nbsp;

https://www.computerworld.com/article/3171766/application-development/how-to-create-your-own-slack-bots-and-web-apis-in-r.html



========================================================

# **Using Neural Nets to Generate Language**


Let's build a Neural-Net Version of President Trump
========================================================

First, we will need to read in a large number of tweets from Trump's Twitter account:


```r
trumptweets<-read.csv(url("https://cbail.github.io/trumptweets.csv"),
                      stringsAsFactors = FALSE)
```

This dataset includes 32,826 tweets. Unfortunately, your machine likely does not have the capacity to build a neural net large enough to work with this much data, so let's chunk of a smaller part of it (noting, however, that this will decrease the quality of our output):


```r
trumptweets<-trumptweets[31500:32826,]
```


Let's build a Neural-Net Version of President Trump
========================================================

Next, some basic preprocessing and tokenization (here we are drawing from a popular example for LSTM text generation in KERAS):


```r
library(dplyr)
library(stringr)
library(tokenizers)

trumptweets$text<-gsub("http.*|@*|t.co|amp|RT","", trumptweets$text)
text <- trumptweets$text %>%
  str_to_lower() %>%
  str_c(collapse = "\n") %>%
  tokenize_characters(strip_non_alphanum = FALSE, simplify = TRUE)
```


Let's build a Neural-Net Version of President Trump
========================================================

In order to begin seeing reasonable results, we need at least 500,000 words, and ideally more than one million. Let's see how many words we have here:


```r
print(sprintf("corpus length: %d", length(text)))
```


Let's build a Neural-Net Version of President Trump
========================================================

Next, we set the maximum number of characters we want to use to produce our snippets of text and then divide the text into semi-redundant sequences of characters with this maximum length


```r
maxlen <- 140
library(purrr)
dataset <- map(
  seq(1, length(text) - maxlen - 1, by = 3), 
  ~list(sentece = text[.x:(.x + maxlen - 1)], next_char = text[.x + maxlen])
)
```


Let's build a Neural-Net Version of President Trump
========================================================

Next we transpose our matrix, vectorize, and convert our text into numbers 


```r
dataset <- transpose(dataset)
x <- array(0, dim = c(length(dataset$sentece), maxlen, length(chars)))
y <- array(0, dim = c(length(dataset$sentece), length(chars)))

for(i in 1:length(dataset$sentece)){
  x[i,,] <- sapply(chars, function(x){
    as.integer(x == dataset$sentece[[i]])
        })
  y[i,] <- as.integer(chars == dataset$next_char[[i]])
}
```

Note: you may run out of memory on this step (if so, you need to choose a smaller chunk of tweets to work with)

Let's build a Neural-Net Version of President Trump
========================================================

And now we are ready to build our model:


```r
model <- keras_model_sequential()

model %>%
  layer_lstm(128, input_shape = c(maxlen, length(chars))) %>%
  layer_dense(length(chars)) %>%
  layer_activation("softmax")

optimizer <- optimizer_rmsprop(lr = 0.01)

model %>% compile(
  loss = "categorical_crossentropy", 
  optimizer = optimizer
)
```

We are using a Long Short Term Memory layer with 128 memory units. We are trying to learn the probability of each character in a sequence with the code above 


Let's build a Neural-Net Version of President Trump
========================================================

Now we need some functions for training


```r
sample_mod <- function(preds, temperature = 1){
  preds <- log(preds)/temperature
  exp_preds <- exp(preds)
  preds <- exp_preds/sum(exp(preds))
  
  rmultinom(1, 1, preds) %>% 
    as.integer() %>%
    which.max()
}
```

Let's build a Neural-Net Version of President Trump
========================================================

And a function that helps us keep track of the output after each epoch (we are drawing heavily again from the tutorial by Allaire, Chollet, RStudio, and Google):


```r
on_epoch_end <- function(epoch, logs) {
  
  cat(sprintf("epoch: %02d ---------------\n\n", epoch))
  
  for(diversity in c(0.2, 0.5, 1, 1.2)){
    cat(sprintf("diversity: %f ---------------\n\n", diversity))
    start_index <- sample(1:(length(text) - maxlen), size = 1)
    sentence <- text[start_index:(start_index + maxlen - 1)]
    generated <- ""
    
    for(i in 1:400){
      x <- sapply(chars, function(x){
        as.integer(x == sentence)
      })
      x <- array_reshape(x, c(1, dim(x)))
      preds <- predict(model, x)
      next_index <- sample_mod(preds, diversity)
      next_char <- chars[next_index]
      generated <- str_c(generated, next_char, collapse = "")
      sentence <- c(sentence[-1], next_char)
    }
    cat(generated)
    cat("\n\n")
  }
}
```


Let's build a Neural-Net Version of President Trump
========================================================

And finally we run the model (at least 20 epochs are needed before the results become anywhere near reasonable):

```r
print_callback <- callback_lambda(on_epoch_end = on_epoch_end)

out<-model %>% fit(
  x, y,
  batch_size = 400,
  epochs = 30,
  callbacks = print_callback
)
```


And the results :()
========================================================

<img src="trumpnet.png" height="400" />


What would make this better?
========================================================
&nbsp;

1. More training data (maybe not Twitter?)
2. More epochs
3. Run on GPU/Amazon AWS (See notes from previous lecture slides)


