Tidytext for textual analysis
================

[Intro to
tidytext.](https://cran.r-project.org/web/packages/tidytext/vignettes/tidytext.html)

[Whole book.](https://www.tidytextmining.com/)

As always, let’s install/activate our packages.

``` r
pacman::p_load(tidytext,tidyverse,cld3,textdata)
```

# Why Primitive Sentiment analysis (SA) is generally a bad idea and why study it?

-   Primitive Sentiment Analysis is: associating relevant words in a big
    or a small document to a binary or multipoint scale of values, then
    making a inference about a *fixed* parametric state about that
    document.

------------------------------------------------------------------------

Very stupid example:

------------------------------------------------------------------------

I have a document of 500 words. I count 46 words that I associate with
happiness and 23 that I associate with sadness. I conclude that (MOMENT
OF INFERENCE) “overall who wrote the document was happy”.

------------------------------------------------------------------------

This is not how modern SA works but at his core this is the general
idea. KEEP IN MIND THIS DISTINCTION between modern SA and Primitive
Sentiment Analysis (PSA).

PSA associates mood to documents through pre-validated common lexicons.
A lexicon is a list of paired association between tokens (words, some
times, roots of words) and values.

We download lexicons through `textdata`. Often text is in English or
Spanish, these languages are well-covered by lexicons.

## So why should we learn PSA if it’s so… Primitive?

It’s a good way to build your muscles with `tidytext`… that is a good
way to drive your way into fancy `ggplot` plots for your academic
papers. Generally it will make you understand very well the essence of
methodological programming for social sciences.

------------------------------------------------------------------------

## Problems with PSA

-   PSA cannot really deal with valence shifters a.k.a. negations:
    `f("not good")` is not `score(not) + score(good)`, is
    `score(good|not)`
-   Semantic problems: the word “miss” means both young lady (arguably a
    polite mood) and “feeling deprivation” (arguably negative mood).
-   People lie and their documents lie too.
-   Very, very hard to deal with irony in texts unless one setup
    advanced methods (Deep Learning). Social media are full of irony.
-   Number of words in the document matters: short documents have a
    “small sample size problem”, very long documents have a (possibly
    more serious) “regression to the mean” problem.
-   Estrinsic value issue: PSA for one document brings almost no value
    to any kind of textual analysis. But once you collect many
    documents, at that point it’s worth to go into something more
    advanced.

------------------------------------------------------------------------

Regression to the mean problem: when you ask to evaluate a class with
only 3 words, the respondent says: “It was good”. Good, clear cut
signal. Then, you ask a detailed long form and the respondent start to
fill it with personal impressions: “I was worried that I was not good
enough for this class, but I got helped and overall it was a positive
experience”. Here our SA engine could be confused by the presence of the
word “worried”. For the human, this information is valuable. For the
machine, it’s a source of confusion (noise).

------------------------------------------------------------------------

Think out of the box about SA. Are there methodological differences
between observational textual data and survey textual data in
application of SA?

------------------------------------------------------------------------

# How to code SA in R with tidytext

Import a dataset of reviews on the website Metacritic on controversial
items.

``` r
load("Classes/3 tidytext for Primitive Sentiment Analysis/Controversial_Metacritic.RData")
```

From this, for now let’s focus on Kanye West. The db is already
`grouped` for users. We only want more the `text`, and the `score`.

``` r
pacman::p_load(tidyverse)
db %>% filter(str_detect(Item,"Kanye West")) %>%
  select(Text,Score,Item) %>%
  arrange(User) -> KWdb
```

We need to cleanse the text.

``` r
KWdb %>% mutate(Expand = str_detect(Text,"Expand\n"),
                Language = cld3::detect_language(Text),
                Text = str_remove_all(Text,
                                      "This review contains spoilers, click expand to view."),
                Text = str_remove_all(Text,
                                      "Expand/n"),
                Text = tm::removePunctuation(Text),
                Text = str_squish(Text),
                Text = trimws(Text, whitespace = "Expand", which = "right")
                ) %>%
    filter(Language == "en",
          !is.na(Language) #Separate lines for cleansing allow us to activate/deactivate a specific operation just by putting # before the line
          ) ->  KWdb
```

[Hey Giulio pls check this](https://www.metacritic.com/user/Yopopo)

Now, the idea behind tidytext is to “unnest the text in tokens”… that
are just words.

``` r
# Reminder. You got this pipeline. To understand it, you can
# put a # before each line of the pipeline to reverse-engeneer it.

KWdb %>%
  select(User,Text,Score,Item) %>%
  group_by(User,Item) %>%
  tidytext::unnest_tokens(word,Text) %>%
  anti_join(stop_words, by = "word") %>%
  inner_join(get_sentiments("bing")) %>%
  summarise(Negatives = sum(sentiment == "negative"),
            Positives = sum(sentiment == "positive")) %>%
  right_join(KWdb, by = c("Item","User")) %>%
  arrange(User) %>%
  mutate(Negatives = replace_na(Negatives,0),
         Positives = replace_na(Positives,0))
```

Well, we reached a nice point right?

At this point you can you your own knowledge to combine these values.

Ideally, you want a function that summarise or combine information from
`Positives` and `Negatives`.

But before… I want to show you something:

``` r
KWdb %>%
  select(User,Text,Score,Item) %>%
  group_by(User,Item) %>%
  tidytext::unnest_tokens(word,Text) %>%
  anti_join(stop_words, by = "word") %>%
  inner_join(get_sentiments("afinn")) %>%
  summarise(Sentiment = mean(value)) %>%
  right_join(KWdb, by = c("Item","User")) %>%
  arrange(User) %>%
  mutate(Sentiment = replace_na(Sentiment,0)) %>% # THIS IS ACTUALLY WRONG FROM THE STANDPOINT OF A STATISTICIAN OR AN INFORMATION SCIENTIST
  lm(data = .,
     scale(Score) ~ scale(Sentiment)) %>% broom::tidy() %>%
  mutate_if(is.numeric,round,3) # And this is wrong too... but maybe
```

Notice that there is a `Score`, associated to the “document” (the row),
that in this case is the combination of `User` and `Item`. This is
valuable, because it’s ok that you take it as a *ground truth value* for
many tasks:

-   Validation of a lexicon
-   Validation of a technique of SA (that is… the function)

------------------------------------------------------------------------

Validation: do you expect that a small error between the inferred
sentiment and the *ground truth*?

------------------------------------------------------------------------

But you should be free to integrate `Score` for a complex indicator, if
that’s what you are into.

------------------------------------------------------------------------

Hint: negative and positive counts can be used to specify a Beta prior
if you are into Bayes analysis (which I don’t expect you to be, gladly).
The benefit of this: it would help to catch uncertainty about the
Sentiment in a very elegant way.

------------------------------------------------------------------------

Open question, do you expect a PSA technique to be internally validated
on Kanye West data to be externally validated on another popstar? Yes?
Not? Why? (tricky question)

------------------------------------------------------------------------

# What’s next?

## Advanced SA

Now, you probably don’t want to adopt PSA, you want advanced SA. Just
try packages `SentimentAnalysis` and `sentimentR`.

How tidytext has been helpful here? To show how to reproduce different
techniques of SA.

## Make your own lexicon!

[You can follow this
video!](https://www.youtube.com/watch?v=whE85O1XCkg)

It will be hard? Yes. But just do it. It will introduce you also to tidy
modeling, application of Machine Learning (that actually make sense!)
with LASSO regressions, and more generally to a user-friendly way to do
data science.
