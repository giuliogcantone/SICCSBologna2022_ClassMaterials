Amazon Scraper
================

# Rvest is for scraping!

So, install and load it with pacman!

``` r
pacman::p_load(tidyverse,rvest)
```

# rvest is a great scraper!

To my knowledge, there are essentially 3 free software for scraping
things on Internet. We are going to use `rvest`. It is quite easy,
intuitive and well-responding. Alternatives are `BeautifulSoup` for
Python and Selenium (`RSelenium`). `BeautifulSoup` is conceptually the
same of `rvest` so it’s only a matter of personal taste, but Selenium
and `rvest` works differently.

`rvest` connects R to the **back-end** code of any address. Technically,
it works like this:

-   You download the back-end of a page.
-   rvest is smart at cleaning it

Selenium will set up a bot crawler on a browser. The bot is than guided
to perform some actions. Generally, it will literally copy-paste stuff
from the **front-end**.

Sometimes (rarely) you want to scrape from front-end instead of
back-end. I can tell you my own experience: I scraped web-pages from the
marketplaces of videogames “Steam”. However, some videogames were Adults
Only and they require to physically push a button to access their
web-page, hence their front-end is protected.

Just for your knowledge, some academic journals protects their articles
from web scraping in this way, but not all of them!

However, I also feel that a correct setup of Selenium is a pain in the
ankle to code, and that `rvest` is an excellent beginning.

Overall, since Selenium have to synchs to a browser, is also slower than
`rvest`.

## rvest main commands

The main commands of rvest are:

-   `read_html(URL)`: R to go to a URL and to start watching for the
    back code of that website addressed at the `URL`.
-   `html_elements("[selector]")`: this is just a filter. To code
    correctly how to write the `("[selector]")` is the **hard part** of
    scraping. In my opinion, is mostly a work of deduction and reverse
    engineering.
-   `html_text()`: this will just convert your selection of elements in
    a vector of `character`. Sometimes you don’t want a `character`, but
    a `numeric`, but in my opinion having **everything scraped as text
    before pre-processing text will help a lot debugging and it’s
    generally a good practice**.

# Scraping pesto!

In this example we want to collect the reviews for a jar of italian
pesto. In particular, we will scrape the reviews from Amazon.it, that is
the Italian Amazon.

So, we need to set the `URL` of `read_html()` as the address of the
webpage for the reviews

``` r
URL = "https://www.amazon.it/product-reviews/B00XUGZRL8/ref=cm_cr_getr_d_paging_btm_next_2?ie=UTF8&filterByStar=all_stars&reviewerType=all_reviews&pageNumber="
read_html(URL)
```

    ## {html_document}
    ## <html lang="it-it" class="a-no-js" data-19ax5a9jf="dingo">
    ## [1] <head>\n<meta http-equiv="Content-Type" content="text/html; charset=UTF-8 ...
    ## [2] <body>\n<span id="cr-state-object" data-state='{"asin":"B00XUGZRL8","devi ...

Now, in theory, we could proceed to select only the part of content of
our interest, for example the text of the reviews:

``` r
read_html(URL) %>%
  html_elements(".review-text-content span") %>% #NOTICE THE SELECTOR!
  html_text() -> reviews
reviews %>% head(2)
```

    ## [1] "Almeno una volta a settimana sono abituato a mangiare un piatto di pasta con il pesto…ne ho provati tantissimi (ho persino provato a farlo a casa con risultati appena sufficienti!).Questo Barilla, un nome altisonante nell’ambito dell’industria alimentare, ha suscitato il mio interesse.Un pesto dentro un barattolo di vetro sigillato che vanta la nomea di essere alla genovese.Devo dire che appena aperto, già dalla consistenza, non mi ha ispirato tantissima simpatica.Sembra più che altro una crema e questo si traspone nel momento andiamo a mescolarlo con la pasta: si avrà una crema biancastra mischiata col verde del basilico “atomizzato”. Credo sia dovuto al latte in polvere e alcuni suoi derivati usati come aromi mischiati negli ingredienti!Il sapore è molto sapido ma non so se sia colpa del sale, del troppo formaggio o di entrambi.Purtroppo non ho scorto l’ombra di pinoli, sostituiti dagli più economici anacardi tritati a fine granella.L’amalgama con la pasta sarà assicurato ma niente di che…un sapore forte che ha poco a che fare col vero pesto. Altri brand concorrenti fanno meglio…primo fra tutti il famoso Rana.Per la mia esperienza, visto il prezzo per 190g di 1,75\200 (discreto) ed il sapore (sufficiente), do un voto di 6,7/10 => 3 stelle nel rapporto qualità/prezzo."
    ## [2] "Mangiamo spesso Il pesto a casa nostra e questa versione e quella che mette d'accordo tutti in casa ,grandi e piccini.In questa ricetta non c'è aglio cosa che abbiamo provato in passato e rendeva il sugo difficile da digerire per noi e sopratutto per i bimbi. Lo serviamo con rigatonibo all'occorrenza pennette con una spolverata di grana padano hanno un sapore eccezionale,  velocissimo da preparare , lo caliamo cosi com'è dopo aver scolato la pasta lo aggiungiamo in pentola su un fuoco lento e amalgamiamo aggiungendo 100 g di Grana ( x 4 persone ) 1 minuto di mantecazione ed il gioco e fatto.Perfetto x pasti veloci e senza sporcare più di una pentola . ECCEZIONALE VERAMENTE"

### Sometimes you want to scrape a link.

More in general, sometimes you want to scrape an `html`-attribute. What
is an attribute? Is a code that modifies or ‘augment’ the front-end of
the content called in the code. Most of the times, you just want to
scrape a link, that is exactly an augment of a text or an image.

For example, for linking something in Markdown, the language behind this
document, you have to back-end this format: `[text](link)`. Taking this
metaphor in mind, the equivalent in `rvest` would be to select
(`html_elements`) the `[text]`, but then to extract the `(link)`
attribute, not the text in itself. In `rvest`, this is done with the
command `html_attr("href")`.

``` r
read_html(URL) %>%
  html_elements("#cm_cr-review_list .a-profile") %>%
  html_attr("href") # notice html_attr and not html_text()
```

    ##  [1] "/gp/profile/amzn1.account.AEJ5W75HK3VXWYKK2XHU75GGRWZA/ref=cm_cr_arp_d_gw_btm?ie=UTF8"
    ##  [2] "/gp/profile/amzn1.account.AEJ5W75HK3VXWYKK2XHU75GGRWZA/ref=cm_cr_arp_d_gw_pop?ie=UTF8"
    ##  [3] "/gp/profile/amzn1.account.AES4FXB5YNBKSMPNSSBP2QVCPFIQ/ref=cm_cr_arp_d_gw_btm?ie=UTF8"
    ##  [4] "/gp/profile/amzn1.account.AEZKHX736VTPTYNF3KR72ZMBW6QQ/ref=cm_cr_arp_d_gw_btm?ie=UTF8"
    ##  [5] "/gp/profile/amzn1.account.AEZKHX736VTPTYNF3KR72ZMBW6QQ/ref=cm_cr_arp_d_gw_pop?ie=UTF8"
    ##  [6] "/gp/profile/amzn1.account.AFYAZWPWIUBL6JA7VL6C55RT6GSQ/ref=cm_cr_arp_d_gw_btm?ie=UTF8"
    ##  [7] "/gp/profile/amzn1.account.AFYAZWPWIUBL6JA7VL6C55RT6GSQ/ref=cm_cr_arp_d_gw_pop?ie=UTF8"
    ##  [8] "/gp/profile/amzn1.account.AGJOT5T6XT2OWTXMS2LGFAL3F4RA/ref=cm_cr_arp_d_gw_btm?ie=UTF8"
    ##  [9] "/gp/profile/amzn1.account.AGJOT5T6XT2OWTXMS2LGFAL3F4RA/ref=cm_cr_arp_d_gw_pop?ie=UTF8"
    ## [10] "/gp/profile/amzn1.account.AHBBITMVHK6Z32CW7PXH5SBW4J2Q/ref=cm_cr_arp_d_gw_btm?ie=UTF8"
    ## [11] "/gp/profile/amzn1.account.AHTYS2UDLNNVEXVKSV3CQB77GBXQ/ref=cm_cr_arp_d_gw_btm?ie=UTF8"
    ## [12] "/gp/profile/amzn1.account.AHTFZW3VSZ27SLI3MHAYX4I2UGGA/ref=cm_cr_arp_d_gw_btm?ie=UTF8"
    ## [13] "/gp/profile/amzn1.account.AHTFZW3VSZ27SLI3MHAYX4I2UGGA/ref=cm_cr_arp_d_gw_pop?ie=UTF8"
    ## [14] "/gp/profile/amzn1.account.AFCLJKUDUAK62UEODAEWBHMQERVQ/ref=cm_cr_arp_d_gw_btm?ie=UTF8"
    ## [15] "/gp/profile/amzn1.account.AHHRODKEHXRIZHA7NS4WREPMQD3A/ref=cm_cr_arp_d_gw_btm?ie=UTF8"

### Care for the difference between singluar and plural in “element\[s\]”

This is a list of all the times that `rvest` finds a list of link
associated to usernames, in the list of reviews for that URL. We would
expect only a list of 10 elements, but it may be more.

### WHY?

Because Amazon code is not exactly very tidy, so I wrapped the code with
a fix that will leave only unique links.

``` r
read_html(URL) %>%
  html_elements("#cm_cr-review_list .a-profile") %>%
  html_attr("href") %>%
  str_remove("\\/ref.*") %>% # THIS IS A REGULAR EXPRESSION
  unique()
```

    ##  [1] "/gp/profile/amzn1.account.AEJ5W75HK3VXWYKK2XHU75GGRWZA"
    ##  [2] "/gp/profile/amzn1.account.AES4FXB5YNBKSMPNSSBP2QVCPFIQ"
    ##  [3] "/gp/profile/amzn1.account.AEZKHX736VTPTYNF3KR72ZMBW6QQ"
    ##  [4] "/gp/profile/amzn1.account.AFYAZWPWIUBL6JA7VL6C55RT6GSQ"
    ##  [5] "/gp/profile/amzn1.account.AGJOT5T6XT2OWTXMS2LGFAL3F4RA"
    ##  [6] "/gp/profile/amzn1.account.AHBBITMVHK6Z32CW7PXH5SBW4J2Q"
    ##  [7] "/gp/profile/amzn1.account.AHTYS2UDLNNVEXVKSV3CQB77GBXQ"
    ##  [8] "/gp/profile/amzn1.account.AHTFZW3VSZ27SLI3MHAYX4I2UGGA"
    ##  [9] "/gp/profile/amzn1.account.AFCLJKUDUAK62UEODAEWBHMQERVQ"
    ## [10] "/gp/profile/amzn1.account.AHHRODKEHXRIZHA7NS4WREPMQD3A"

Much tidier, right?

------------------------------------------------------------------------

# Regular expressions

THEY ASKED ME TO TEACH YOU REGULAR EXPRESSION.

REGULAR EXPRESSIONS ARE THE BANE OF COMPUTATIONAL SOCIAL SCIENCE

Computer scientists seem to lack the capacity to explain what a regular
expression is to a social scientist. Check the superca\*\*o\*a of
Italian Wikipedia (translation in mine):

|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A regular expression is a sequence of symbols (then a string) that identifies a set of strings. They can define all and only the regular languages. Kleene’s Theorem says that the class of regular languages corresponds to the class of languages generated from type-3 grammars (in Chomsky’s hierarchy) and recognized by finite status automata. However, in practice, exist some constructs (for example, backreference constructs) that allow to amplify the set of defined languages. Different programs support different notations to express the same regular expression, hence an “universal” syntax do not exist. |

Now, my definition of Regular Expression:

|                                                                                                                                                                                                                   |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A regular expression is a method of symbolic logic to identify a class of sequences of symbols. It allows to refer at the same time to more than one sequence of symbols, but with one representation of symbols. |

A regular expression is a symbol that represent a family of symbols.

The most famous regular expression is `"."` within a string. It means:
“anything that is not a new line (`"\n"`). So if one looks for `"a."`,
is looking for `"ab"`,`"ac"`,`"ad"`,`"aò"`,`"aä"`,`"a?"`… anything that
is not `a\n`.

How can one look for only et exactly `a.`? You put `\` before a regular
expression, to negate the regular expression function. So, you need to
write: `"\a."`

Sometimes regular expressions are write extensively, like “\[:digit:\]”
looks for symbols of numbers (not numbers! symbols of number!).

The truth is that regular expressions are 10% understanding and 90%
memory, and are often stupid, so just [take this
sheet](https://www.r-bloggers.com/2017/11/new-rstudio-cheat-sheet-strings-in-r/)
and let’s end this atrocity.

------------------------------------------------------------------------

# Ready for scraping the whole thing?

There are a bunch of issues to solve, here:

-   It only scraped 10 reviews. Why?
-   This text has no author, no date, no score…

Let’s solve these issue!

## Infer the number of pages to scrape

The scraper downloaded only 10 reviews because in the URL that I
provided there are those 10. There are other reviews for the jar of
pesto in Amazon, but they are in other URLs. Luckily, it’s quite easy to
infer where these are…

For example…

``` r
read_html(URL %>% str_c("2")) %>%
  html_elements(".review-text-content span") %>%
  html_text() -> reviews
reviews %>% head(2)
```

    ## [1] "La quantità di pesto contenuta nel barattolo da 190gr è sufficiente per fare due volte la pasta al pesto in una famiglia di 3 persone. Lo si puo può utilizzare anche per condire una focaccia, per fare delle sfoglie, ma io lo preferisco utilizzare sulla pasta, meglio se fresca, il top sarebbe con le trofie, così da essere il vero primo genovese. Il vasetto con la chiusura è utile se non lo si consuma in una volta, quindi si può richiudere e riporre in frigo. Il pesto ha un bel colore e risulta molto profumato all'apertura del barattolo, infatti ricevuto e mangiato il giorno stesso, con l'aggiunta di un po' di olio extravergine di oliva e grana padano grattugiato. Il prodotto è gustoso rimanendo delicato, più delicato di altri prodotti sul mercato che tendono ad essere invece più forti, meno cremosi bensì più oleosi e persino di un verde più forte e scuro del pesto Barilla. In famiglia è stato gradito."                                                                                                                                                                                                                                                                                                                                                         
    ## [2] "Essendo una mamma lavoratrice, ho sempre una scorta di sughi pronti  e pesti  in caso di mancanza di tempo  nella mia cucina, infatti per me sono un \"mai più senza\".Un aspetto importante di questo pesto è il senza glutine e che possiamo mangiare tutti lo stesso condimento.Questo Pesto alla Genovese senza aglio risulta essere delicato e si percepiscono bene i profumi di  basilico e il sapore del Grana Padano,dalla giusta consistenza non troppo oleoso ma cremoso.Ideale per condire qualsiasi tipo di pasta e pronto da versare direttamente nella pentola una volta che la pasta è stata scolata, io per renderlo più Cremoso aggiungo un goccio di latte  e una manciata di grana, così da renderlo ancora più gustoso.Il fatto che sia senza aglio limita di molto eventuali problemi di alitosi.In questo periodo estivo l' ho utilizzato anche per la pasta fredda da portare al mare perché il pesto la rende più profumata e gli conferisce un ottimo sapore.<U+0001F31E><U+0001F3D6><U+FE0F><U+274C>Questo pesto come tutti i pesti pronti da consumare contengono  olio di semi di girasole e anacardi al posto di olio di oliva e pinoli come nella ricetta originale,<U+274C> sicuramente per un genovese questo non sarebbe neanche un pesto, ma per noi va benissimo.Spero di esserti stata di aiuto nella scelta <U+2764><U+FE0F>."

I only added a number after the URL, now the scraper knows that it must
go to page 2 of reviews. Almost all websites are organized exactly in
this way. I already pre-coded the URL of Amazon in a way that I only
need to add one number to jump into that webpage, and you should try to
do the same, understanding the structure of the URL code.

`https://www.amazon.it/product-reviews/B00XUGZRL8/ref=cm_cr_getr_d_paging_btm_next_2?ie=UTF8&filterByStar=all_stars&reviewerType=all_reviews&pageNumber=`

In this case `&pageNumber=` ends the address; this is the reason why the
trick with `%>% str_c("2")` works. You should adapt to the website that
you want to scrape and arrange accordingly the address. Usually website
have front-ends elements, like buttons or drop-down menus that will help
you with it.

So, which is the number of pages for the jar of pesto?

On Amazon it is not explicit, however it is explicit the number of
written reviews, that can be selected from the main `URL` with a
specific selector.

``` r
read_html (URL) %>%
  html_node("#filter-info-section span") %>% html_text
```

    ## [1] "\n                    1.285 valutazioni globali | 297 recensioni globali\n                "

The information is here, but we need to polish it. The number we are
looking for is “290 recensioni globali”.

``` r
read_html (URL) %>%
  html_elements("#filter-info-section span") %>% html_text %>% word(25) %>%
  as.integer() -> nrev
nrev
```

    ## [1] NA

Finally, if for each 10 reviews will be generated a new page on Amazon,
then the number of pages is…

``` r
ceiling(nrev / 10) -> lastpage
lastpage
```

    ## [1] NA

# How to organize a scraped dataset

Information on timestap, author, text and score goes in parallel, in the
sense that there is one author per review, etc.

The ideal data structure to organize parallel observation of different
features is the `tibble`, and it is certainly viable to just declare a
`tibble` with a fixed number of row and then fill it procedurally with
the scraped content.

However, I think that the best method is to fill separate `vectors` and
only after the scraping to convert them in a `tibble`. For many
`vectors`, the good practice is to organize them in a `list` and then
convert the `list` into a `tibble`. I do not like to work with `list`
syntax, and `list` are very annoying to browse, but once the coding is
done the whole operations are much more smooth.

``` r
reviews = list()
```

``` r
for (i in 1:lastpage) {
  
read_html(URL %>% str_c(i)) -> x
  
reviews$Product[(1+(i-1)*10):(i*10)] = "Pesto Jar"
  
    # Timestamp  
  x %>% html_elements("#cm_cr-review_list .review-date") %>%
    html_text() -> reviews$Time[(1+(i-1)*10):(i*10)]
  
    # Userpage link
  x %>% html_elements("#cm_cr-review_list .a-profile") %>%
  html_attr("href") %>%
  str_remove("\\/ref.*") %>%
  unique() -> reviews$User[(1+(i-1)*10):(i*10)]
  
    # Score
  x %>% html_elements("#cm_cr-review_list .review-rating") %>%
    html_text() -> reviews$Score[(1+(i-1)*10):(i*10)]
  
    # Comment
  x %>% html_elements(".review-text-content span") %>%
    html_text() %>% .[. != ""] -> reviews$Text[(1+(i-1)*10):(i*10)]
}
```

``` r
db = as_tibble(reviews)
db
```

Yes, the `db` needs a bit more of cleaning, but this is higly dependent
by the Amazon website you are going to scrape. These data are in
Italian. In my experience the different languages requires slightly
different workflows, and unfortunately I cannot say that the selectors
in `html_elements` are the same across languages…

### Why a for cycle?

Why a `for` cycle and not something fancier, like a `map_*`? In my
experience, everytime you have to connect to a website you always want a
slow, procedural, controlled `for` cycle.

# Tell me more about selectors

There are two ways for fishing for selectors:

-   [SelectorGadget](https://chrome.google.com/webstore/detail/selectorgadget/mhjhnkcfbdhnjickkkdbjoemdmbfginb)
-   Inspecting the back-end code of the webpage

My suggestion is to watch a video-guide on SelectorGadget and start
playing with it in simpler websites. Amazon is not very simple. The key
idea to memorize is that you always start highliting in green something
in the front-end that you want to scrape for sure, then you localize
elements that you want to be out of the selection. SelectorGadget will
highlight these in red. In the end it should provide a CSS selector code
to insert in the `html_elements()` command.

Personally, I prefer go searching within the `html` code of the page. My
strategy is something like this:

-   I decide an example of front-end text that I want to scrape
-   I look for the position of that text in the back-end code
-   I try to deduce the class (`class=something`) of that elements.
    Often that class is in common with all the other elements with the
    same functions (e.g. the score)
-   `html_elements(".something")`

Sometimes I need to scrape `html_elements(".something")` only within a
section of the page; for example, look in the code above that I select
with:

`html_elements("#cm_cr-review_list .review-rating")`

The function of `#cm_cr-review_list` is to limit the selection only
within a section of the webpage called `cm_cr-review_list` in the `html`
code of the webpage.

# Why to limit only to a jar of pesto?

In theory, every single product on Amazon can be scraped. Sometimes it
is useful to make a collection of similar items.

There are many ways to do so. I think that the most reliable would be to
collect in a `tibble`:

-   the product ID (e.g. “Pesto Jar”)
-   all the webpages associated to their `pageNum`
-   the lastpage for each ID

Let’s name this `tibble` as `URL_list`

Then , instead of reading the `html` code from

`read_html(URL %>% str_c(i)) -> x`

the `for` cycle would iterate `x` across

`read_html(URL_list$URL[i]) -> x`.

The final `db` would look like this:

``` r
tibble(Product = c("Pesto Jar","Pesto Jar","Tuna Can"),
       Time = c("Monday","Tuesday","Sunday"),
       User = c("Donald","Goofy","Donald"),
       Score = c(4,5,2)
)
```

    ## # A tibble: 3 x 4
    ##   Product   Time    User   Score
    ##   <chr>     <chr>   <chr>  <dbl>
    ## 1 Pesto Jar Monday  Donald     4
    ## 2 Pesto Jar Tuesday Goofy      5
    ## 3 Tuna Can  Sunday  Donald     2
