by William Darling

[Last time](https://github.com/Microsoft/CNTK/wiki/Sequence-to-Sequence-%E2%80%93-Deep-Recurrent-Neural-Networks-in-CNTK-%E2%80%93-Part-1), I gave an introduction to sequence-to-sequence networks and their implementation using CNTK. In particular, I talked about how this seemingly simple deep recurrent neural network architecture is revolutionizing the approaches that we take to NLP (and other) tasks like text summarization, syntax parsing, and machine translation. To reiterate, a basic sequence-to-sequence network with no special knowledge of the problem domain is in many cases able to reach – and surpass – the results that a specialized approach, built up over several years by very smart people, have been able to accomplish with precise pre-processing, model tuning, and everything else. Today, I will report some of my experiences and results that I achieved using the basic sequence-to-sequence model in CNTK that I discussed last time, applied to machine translation. 

First, I know essentially nothing about machine translation except that, living in Germany, I depend on it probably a little bit too much. Every time I get a piece of mail that looks official (anything non-official-looking typically gets pre-processed out) I feel a great amount of gratitude to every MT researcher out there. So, like the intended reader of this blog, I am an MT newb. But I wanted to see how difficult it would be to come to a new domain and get some interesting results which is more-or-less what I promised s2s networks allow in my last post. I wanted to try something a little bit beyond a toy example, but nothing that would require too much domain knowledge or too much training time. In the end, I decided on English to French translation with the transcripts from the Canadian Parliament as training data. 

In the rest of this post, I will explain the steps I had to take to find the data and get it ready for training, the parameters I chose for my network, my results (I had some memories of the [BLEU](https://en.wikipedia.org/wiki/BLEU) metric left over from grad school) and what the next steps might be if I were so inclined to continue to improve this model. Let's start with the data… 

# Data #

Getting data for learning a MT model is harder than I had anticipated. I first considered the data from the EMNLP Workshop on Machine Translation ([WMT15](http://www.statmt.org/wmt15/index.html)) but it consists of about 20 GB of data so I thought that might be a bit big for a first try. I then considered taking a subset of that data – say the EuroParl V7. The EuroParl data seemed to require all kinds of sentence-alignment pre-processing with tools that never worked and I quickly gave up on that too. Finally, I went back to my (Canadian) roots and turned to the [Aligned Hansards of the 36<sup>th</sup> Parliament of Canada](http://www.isi.edu/natural-language/download/hansard/). The Hansards are the name for the transcripts of Parliamentary debates in the Canadian government. Because Canada is a bilingual country, the transcripts are released in a professionally-translated dual English-French set of documents. And best of all, they are completely free to download and use. Perfect for translation training data.

The training data can be downloaded [here](http://www.isi.edu/natural-language/download/hansard/hansard.36.r2001-1a.house.debates.training.tar) and the testing data is [here](http://www.isi.edu/natural-language/download/hansard/hansard.36.r2001-1a.house.debates.testing.tar). Note that this consists of the House debates (the bigger sets) but there are also Senate debates that could be combined into this data (see the website). The data is already sentence-aligned so we know the input sequence and the output sequence (obviously important) and it's enough data to build a pretty good model, but not so much that it would take weeks to train or use up your whole hard disk: 948,000 sequence pairs for the training data (14,614,000 English words and 15,657,000 French words) and 62,000 sequence pairs for test set 1 and another 60,000 sequence pairs for test set 2 (I only looked at test set 1 and didn't do any parameter optimization). Note that this is a TINY amount of data; to get a great translation model you'll need way more data and way more varied data. For example, the types of conversations that are had in the House of Commons are likely very different from the style, vocabulary, etc. of the types of things that you might want to translate from a TV show or a government letter or a magazine. But for this domain, at least, we should be able to get something that is not bad. 

An example sequence pair from this data looks like this (after some very basic NLTK-based tokenizing): 

| Language      | Sentence      | 
| ------------- |:-------------:| 
| English| that motion also called on the federal government to take action to tackle energy inefficiency | 
| French| cette motion exigeait également que le gouvernement fédéral prenne des mesures pour résoudre les problèmes dans ce domaine | 

# Model #

I trained a sequence-to-sequence network with attention, 2 LSTM layers of encoder (single direction), 2 LSTM layers of decoder (all LSTM layers with 256 hidden dimensions), an embedding (learned on the data) of 200 dimensions, vocabulary size of 99,771 (no words were cut), and any sequence longer than 50 words (< 1% of the data) was cut to its first 50 words. I implemented this network in CNTK using the [G2P example](https://github.com/Microsoft/CNTK/blob/master/Examples/SequenceToSequence/CMUDict/BrainScript/G2P.cntk) and a miniscule amount of pre-processing and basically random parameter choosing (i.e. why didn't I do one layer or three layers? Great questions with no answers). 

After about a weekend's worth of training (around 10 full passes over the data – which is probably too much but remember this is just for fun), I had what looked like a good enough model. I tried some basic things: 

| Language      | Sentence      | 
| ------------- |:-------------:| 
| English| house of commons | 
| French | chambre des communes | 
| English| i am here to help you| 
| French| je suis ici pour vous aider | 
| English| translating is not so difficult | 
| French| ce n'est pas une question | 

So all in all not so bad except that the translation of the third sentence means "it is not a question" (almost like talking to a bot!). As discussed above, however, having something close enough to "translating is not so difficult" that the model could learn to generalize to that was said in the House of Commons transcripts is pretty unlikely. That's the same reason that when I tried "hello world" the model guessed "le monde entier" which means "the entire world".[1] 


# Evaluation #

The first evaluation that I did (examples just above) was subjective. If you know a language, you can tell if what your model is spitting out is useful or garbage right away. And the stuff above looks pretty good indeed (when in-domain). For things that were "parliamentary" (if you can imagine that), the model seems really good. For everyday speech or common sayings that don't really match the style of the training data, however, it does much worse. But, we knew this would be a pretty domain-dependent MT system. Luckily, we have the test set from the Hansard data release which contains 62,000 pairs of aligned English and French sentences from the parliamentary debates, but that didn't form part of the training data. Let's evaluate our model on that. 

To evaluate our models, I used the machine translation metric [BLEU](https://en.wikipedia.org/wiki/BLEU). BLEU stands for Bilingual Evaluation Understudy and is probably the most popular automated MT metric in use (as far as I -- as a newb, remember -- know). It aims to correlate (and has been shown to correlate well) with human judgments of translation quality. BLEU is computed over an entire corpus (if you try to use it per-sentence it doesn't tell you much of anything [2]) and consists of a number between 0 and 1 with values closer to 1 representing more similar texts to the reference translation. The differences between BLEU and simple precision come in for a few reasons. First, MT systems tend to generate more words than are in a reference text (with many words you have a better chance at getting a high precision of correct words showing up in your translation). BLEU corrects for this by clipping the appearance of words to the maximum number of times that they show up in the reference translations. 

Second, individual word statistics are not usually very telling in translation quality, for obvious reasons. It would be fairly easy to look up each word in a dictionary table and simply replace the English word with its most likely French equivalent. Of course this wouldn't give perfect unigram precision, but it wouldn't be bad. However, it would be a bad translation that lacks proper grammar, and in many cases be entirely nonsensical. To deal with this issue, the same modified precision statistics discussed above are calculated using *n*-grams. The highest correlation with human judgments was found to be when *n*=4, so 4-grams are used in the calculation of BLEU.

Finally, BLEU on its own could in some cases favor very short sentences. If a translation cuts down a sentence and only retains say two words but those words are in all references, then again it will get a good precision score. To combat this problem, the score for the whole corpus is calculated by combining the modified precision scores using the geometric mean weighted by a brevity penalty. This penalty is applied only when the total length of the reference corpus *r* (the correct translation) is greater than or equal to the total length of the translated corpus *c*. If so, a penalty of *e<sup>(1 - r/c)</sup>* is applied. So, let's get to it! 

I used BLEU 1.04 (available [here](ftp://jaguar.ncsl.nist.gov/mt/resources) [3]) which was actually really hard to find, and after spending even more time trying to figure out the XML file format/schema/whatever required for the reference and test sets I finally found a pretty good explanation of them [here](http://www.itl.nist.gov/iad/mig/tests/mt/2009/MT09_EvalPlan.pdf). Here is the output of the BLEU metric: 

    > bleu-1.04.pl -t mytst.txt -r myref.txt 
    [...] 
    Add -0.0082 and +0.0082 to BLEU below for 95% confidence level 
    System,wmd 
    SegsScored,61268 
    SysWords,1093690 
    Ref2SysLen,1.0215 
    1-gPrec,0.5404 
    2-gPrec,0.3277 
    3-gPrec,0.2182 
    4-gPrec,0.1487 
    PrecScore,0.2753 
    BrevityPenalty,0.9787 
    BLEUr1n4c,0.2695 

So, I have 61,268 segments scored (each sentence I made a segment -- not sure if that's right), and those consist of a total of 1,093,690 words. That sounds about right given the stats above. My brevity penalty was 0.9787 and that's probably at least somewhat related to the fact that the way the decoder is currently setup it uses the length of the correct translation (but none of its content of course) and generates words until it hits that length. Once the end-sequence tag is generated ("</s>"), I cut the rest of the sequence, but it can never produce something longer. In the end, we get a BLEU score of 0.2695. Is that any good? Well, subjectively, as we saw, it does do a pretty good job on in-domain stuff. And quantitatively? 

A quick Bing search led me to a paper from 2002, "[A Phrase-Based, Joint Probability Model for Statistical Machine Translation](http://anthology.aclweb.org/W/W02/W02-1018.pdf)" by Daniel Marcu and William Wong, that uses the Hansard dataset to train their "traditional" MT model. They translate French->English instead of the other way around, but the numbers should be roughly on-par. They present numbers for their MT system (with special word-alignments, etc.) and for another baseline: Giza (the IBM Model 4). Remember I'm not claiming this to be rigorous, but I do think it suggests that as a total MT newb, with seq2seq, you can get a pretty good model: 

| Model| BLEU Score| 
| ------------- |:-------------:| 
| IBM Model 4| 0.2158| 
| Phrase-based joint probability model| 0.2325| 
| CNTK Sequence-to-Sequence MT Newb model| **0.2695**| 

Not bad!

# Discussion #

In this post I've showed that by doing some simple data pre-processing, you can get a very good domain-specific (in terms of content) machine translation model trained and ready to use with a very domain-independent (in terms of model-type) model (sequence-to-sequence) and little knowledge of the problem area. More involved papers (e.g. [here](https://arxiv.org/abs/1409.3215) and [here](https://arxiv.org/abs/1409.0473)) show that with a little bit of domain knowledge or at least some more time to spend on the problem, you can get state-of-the-art results using sequence-to-sequence, and even extend to domain-independent machine translation. 

The model I trained could be improved in many easy (and some more complex) ways. For instance, one could try: 

- Training the model for a longer time 
- Optimizing the hyperparameters on one of the development sets 
- Using WAY more data 
- Adding more layers, more hidden dimensions, using a bigger embedding, etc. 
- Doing better pre-processing (I did about the crudest thing imaginable) 
- Etc. 

# Notes #

1. For a great explanation of this problem, see the presentation "[Translating across domains is hard](http://www.clsp.jhu.edu/user_uploads/ws12/daume/Domain%20Adaptation%20in%20Statistical%20Machine%20Translation.pdf)". 
1. By the way, for all the information about BLEU I'm just paraphrasing the [Wikipedia page](https://en.wikipedia.org/wiki/BLEU) so if you're interested in this stuff at all it's probably best that you go read directly from the source. 
1. Yes, they really do still use Perl.
