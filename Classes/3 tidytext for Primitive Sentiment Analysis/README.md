Tidytext for textual analysis
================

[Intro to
tidytext.](https://cran.r-project.org/web/packages/tidytext/vignettes/tidytext.html)

[Whole book.](https://www.tidytextmining.com/)

As always, let’s install/activate our packages.

``` r
pacman::p_load(tidytext,tidyverse)
```

Today we will cover two methods:

-   sentiment analysis (or, more correctly inference of mood) and
-   topic modeling with Latent Dirichlet Allocation

# Why Sentiment analysis (SA) is generally a bad idea and how can social scientists improve it?

-   Sentiment Analysis is: associating relevant words in a big or a
    small document to a binary o multipoint scale of values, then making
    a inference about a *fixed* parametric state about that document.

------------------------------------------------------------------------

Very stupid example:

|                                                                                                                                                                                  |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| I have a document of 500 words. I count 46 words that I associate with happyness and 23 that I associate with sadness. I conclude that overall who wrote the document was happy. |

This is not how modern SA works but at his core this is the general
idea. KEEP IN MIND THIS DISTINCTION between modern SA and Primitive
Sentiment Analysis (PSA)

PSA associates mood to documents through pre-validated common lexicons.
A lexicon is a list of paired association between tokens (words, some
times, roots of words) and values. For example within `tidytext` a good
lexicon is `nrc` for English language. Often text is in English or
Spanish, these languages are well-covered by lexicons.

## Problems with PSA

-   PSA cannot really deal with valence shifters a.k.a. negations:
    `f("not good")` is not `score(not) + score(good)`, is
    `score(good|not)`
-   Semantic problems: the word “miss” means both young lady (arguably a
    polite mood) and “feeling deprivation” (arguably negative mood).
-   People lies and their documents too.
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

### Conclusion: it’s very likely that you WILL NEVER use PSA into a high quality academic paper.

However, in the same way that knowing about bivariate linear regression
is a *knowledge block* for other analysis in Social Science, dealing a
bit with PSA is good mental training for people interested in analysis
of textual data.

|                                                                                                                                                      |
|------------------------------------------------------------------------------------------------------------------------------------------------------|
| Think out of the box about SA. Are there methodological differences between observational textual data and survey textual data in application of SA? |

------------------------------------------------------------------------

# How to code SA in R with tidytext

Import a dataset of reviews on the website Metacritic on controversial
items.

``` r
load("C:/Users/prgca/Desktop/Dottorato/Progetti_2022/SICCSBologna2022_ClassMaterials/Classes/3 tidytext for analysis/Controversial_Metacritic.RData")
```

From this, for now let’s focus on Kanye West. We only want the text, and
the score.

``` r
db %>% filter(str_detect(Item,"Kanye West")) %>%
  select(Text,Score) -> KWdb
```

We need to cleanse the text.

``` r
KWdb %>% mutate(Expand = str_detect(Text,"Expand\n"),
                Text = str_remove_all(Text,
                                      "This review contains spoilers, click expand to view."),
                Text = str_remove_all(Text,
                                      "Expand/n"),
                Text = str_remove_all(Text,"\\b\\s*(\\w+)\\s*\\b(?=[^.]*\\b\\1\\b)"),
                Text = str_squish(Text)
                ) -> KWdb
```

``` r
"Insanely good music. Kanye West is GOAT. The sky is blue. Every song is at least an 8. (with 5 of them 10 imo). His best album i dare say, even better than MBDTF. When people listen to it they’ll go mad, great features who all do equally well and sing sick verses/lyrics. Kanye West is a genius, he creates beautiful songs that blend in well and transition into one another, compelling Insanely good music. Kanye West is GOAT. The sky is blue. Every song is at least an 8. (with 5 of them 10 imo). His best album i dare say, even better than MBDTF. When people listen to it they’ll go mad, great features who all do equally well and sing sick verses/lyrics. genius, he creates beautiful songsblendwelltransition into one another, compelling storytelling, the type that hookslistenermakes them not get old of it, Theis 100% Kanye West’s best song OAT (bold statement i know) I’d say they’re all30, a few in his top 10 and 5.999999999/10… Expand" %>% str_remove_all("\\b\\s*(\\w+)\\s*\\b(?=[^.]*\\b\\1\\b)")
```

``` r
"Insanely good music Insanely good music Insanely good music. Kanye West music is GOAT. The sky is blue." %>% str_remove_all("\\b\\s*(\\w+)\\s*\\b(?=[^.]*\\b\\1\\b)")
```