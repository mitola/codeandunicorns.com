---
title: 'Genomes and object granularity I'
date: 2015-05-10T14:37:46+00:00
author: Joao Pereira

categories:
  - ALL
  - Science
---
![Genomes](posts/romatizma_gen.jpg "")
I enjoy dedicating a good share of my free time reading and learning new skills. It is a necessary trait that keeps my sanity level high on day-to-day basis and helps me abstract from boring tasks at work. But when I do want to learn a new language I prefer having a decent and meaningful project that will keep me motivated.

This time it all started with a statement from one of my favourite theoretical physicists &#8211; Michio Kaku &#8211; when addressing the question of genetics as the key to immortality. The argument was that as the price of genomics goes down the need to devise new gene analysis methods will go up in which he concludes by saying that **Biology will be reduced to Computer Science**.
<p align="center">
<iframe src="https://giphy.com/embed/3o8dFn5CXJlCV9ZEsg"  width="480" height="360" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/converse-3o8dFn5CXJlCV9ZEsg"></a></p>
</p>

Now that got me thinking: What would be the challenges when modelling an entire genome sequence (of an organism) and interact with it real-time? What would be the architecture? Does it exist already at all?

As I had already imagined, these questions are not trivial at all. Without even going into scientific articles and just using Wikipedia alone I found that there is already a [computer model of an organism](http://en.wikipedia.org/wiki/Computational_genomics#First_Computer_Model_of_an_Organism) back from 2012. That organism is the smallest free-living organism. This is already remarkable work. I had to get some idea of the source and of the complexity involved.

### Back to basics

If you&#8217;re not aware of the basics in genomics don&#8217;t feel bad I didn&#8217;t remember either. I had to go Indiana Jones on my biology books in order to be sure I was understanding the problem correctly.

Living organisms consist of cells. Each cell is composed by DNA string(s) denominated as _chromosome(s)_. These can be pictured as the &#8220;blueprints&#8221; for the organism. The chromosomes can then be divided into sequences of _genes_. Roughly we can say genes are protein configurations (= sequences of amino-acids) and can behave differently depending on their position in the DNA string. The complete collection of chromosomes is then called the **genome**.

I used the word &#8220;sequence&#8221; on purpose to reiterate the fact that its the multiple sequencing configurations of these &#8220;objects&#8221; that makes us who we are and how our bodies react to other organisms. But lets look at a small example:

Say you want to search a specific sequence of amino acids (= protein), the average size is about 300 ([source](http://www.weizmann.ac.il/plants/Milo/images/proteinSize120116Clean.pdf)) but can go over 30000. There are 20 possible amino-acids in each position of the chain this means a simple search for a particular protein offers roughly infinite possibilities. There is of course many years of study and research in this area and many breakthroughs allowed scientists to find solutions to these kind of problems.

I then found [this](http://users.rcn.com/jkimball.ma.ultranet/BiologyPages/G/GenomeSizes.html) website as I was curious to know at least the order of magnitude comparing bacteria genome to human genome. The answer is:

> &#8211; Mycoplasma genitalium  
> * Base pairs: 580073  
> * Genes: 517
> 
> &#8211; Humans  
> * Base pairs: 3.3 x 10^9  
> * Genes: ~21000
> 
> &#8211; Humans have about 40 times more genes and about 5000 times more base pairs.

Of course I&#8217;m sure you cannot just compare this data by magnitude alone but it still kind of makes me feel smashed by the complexity just by imagining.

Finally I got to the official [Github repo.](https://github.com/CovertLab/wholecell) I could finally take a look at what kind of programming we are talking here.

### Problem

First of all it seems that it took a cluster of 128 machines alone to run all simulations of the organism on it&#8217;s natural habitat, with results coming very close to the real deal. That alone screams for effective and efficient distributed computing if we ever want to see this kind of work for larger scale organisms.

The guys at Stanford used and abused Matlab in order to achieve this great feat. Personally I had a bit of Matlab experience before and I totally understand how it can save on testing and programming time. The choice of organism is explained as an important first step before proceeding to larger and more complex organisms which is also fairly understandable.

With these points in mind i couldn&#8217;t help to imagine on how could we process this data in a larger scale. Eventually proof of concepts and scientific models are handed to software designers and engineers so they work their magic and make it scalable. But I still didn&#8217;t know what seems to be the &#8220;Achilles heel&#8221; of computational genomics, so I tried to find some experts opinions and that&#8217;s where I stumbled upon a one-sentence-fits-all (from Quora) on computational genomics problems:

> Analysing seemingly disparate, wide-ranging&#8211;and ever expanding&#8211;phenotypic data in context of a static genetic profile in a diverse cohort that is dynamically growing.

Cryptic as it seems at first I then proceeded to find an analogy that best fits this statement. I came up with 2 simpler problem statements like so:

> **Analysing seemingly disparate, wide-ranging&#8211;and ever expanding&#8211;phenotypes**  
> 1 &#8211; The modelling of always-evolving individuals and figuring out the &#8220;family&#8221; tree.
> 
> **Generating a profile from the diverse cohort that is dynamically growing**  
> 2 &#8211; How different families interact with each other in order to picture a larger entity (== profile)

My analogy seems to make sense to me but I plea for you my dear reader to correct me if I am wrong. If we go back and check the orders of magnitude presented above, one can imagine how these statements fit in.

#### So the problem is granularity?

If the above statements are true, this seems to be one of the main problems. How do we connect the dots for wide-ranging/ever-expanding types and how do they work together to form bigger entities?

One thing is certain as well, as first observed and as discussed in many forums: Distributed computing architecture is also a big problem, one I won&#8217;t be addressing in this article anymore as it would deserve a whole article on its own.

### Looking for answers

As a good soldier I had to turn to actual bibliography concerning code architecture and design. There might be other (more suited?) examples, I will be very grateful if you point me to other references.

#### The broad direction

I needed a book which could provide a broad but rich analysis on software development practices from the very basics to the most advanced techniques and that could get me started on where to read on the specifics. I normally refer to [Code Complete 2](http://www.amazon.com/gp/product/0735619670/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0735619670&linkCode=as2&tag=codeunico-20&linkId=ESQXUVIGF4RA4YC3) when looking for further reading on specific topics. One of the conclusions extracted was clear:

> &#8220;Patterns are the key to larger granularity discussions.&#8221;

If patterns are the key to larger granularity discussions that brings us to&#8230;\*drum roll\*&#8230; one of the most celebrated books of all computer science short life &#8211; [Design Patterns by the **Gang of Four**](http://www.amazon.com/gp/product/0201633612/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0201633612&linkCode=as2&tag=codeunico-20&linkId=5JZ5ZWALQA2TSXQU)

#### The Gang of Four

If Code Complete is the [&#8220;Joy of Cooking&#8221;](http://blog.codinghorror.com/recommended-reading-for-developers/) for programmers, this one is probably a crash course on Mediterranean cuisine, with specifics on how to assemble tasty pasta dishes.

This book is famous for statements that still spill much ink even over 20 years after it was first published, such as:

> &#8220;Program to an interface, not an implementation.&#8221;

&#8230;or even:

> &#8220;Favor object composition over class inheritance.&#8221;

It thoroughly catalogs some of the most common design patterns in the object-oriented software development history.

At the time I am writing this I have just started digesting this book, but I jumped to one design pattern to get me started on how to address our honourable quest for modelling complex and granular objects such as genomes. And the answer that seems to come close when handling many objects is **Flyweight** and his _compadres_ **Composite** and **Factory**. Now the million dollar question seems to be: **Does computational genomics fit into the use cases for Flyweight?**

### &#8220;A journey of a thousand miles begins with a single step&#8221;

Now, now, now&#8230; Clearly my expertise in this area is a limiting factor deciding which design patterns apply, the key elements, their interactions and the small detail that&#8230;uhm&#8230;it&#8217;s a NP-HARD problem! So I must be pragmatic. Please lay down the pitchforks and put down the torches, that&#8217;s not nice.

However I couldn&#8217;t stop this train without producing something meaningful for myself and perhaps even useful for the community. That&#8217;s why I decided to implement an example of genetic algorithm in a new language&#8230; say Rust! (More on the next part)

I had formal training about genetic algorithms, though more as introductory than comprehensive. And I always wanted to get better at it, hence my resolution.

Finally I will finish with a childhood quote &#8211; the old (pt_PT) Dragon Ball Z ending quote:  
**Don&#8217;t lose the next episode, because we&#8230; won&#8217;t either!**

### Notes for clarity:

#### 1 &#8211; My knowledge of Biology doesn&#8217;t go beyond of that of a high school student, I didn&#8217;t come out looking to start a Biology war on this article (if there is any reason to)

#### 2 &#8211; Also [this](http://stackoverflow.com/questions/3819977/what-are-the-differences-between-genetic-algorithms-and-genetic-programming) should be important for clarity

### Computational genomics references:

#### 1 &#8211; [Genetics and computer science by Michio Kaku](https://www.youtube.com/watch?v=QsHuGQieyjY)

#### 2 &#8211; [Whole genome sequencing by Wikipedia](http://en.wikipedia.org/wiki/Whole_genome_sequencing)

#### 3 &#8211; [The first computer model of an organism, Stanford 2012](http://news.stanford.edu/news/2012/july/computer-model-organism-071812.html)

#### 4 &#8211; [The actual paper on whole-cell computational model](http://www.cell.com/abstract/S0092-8674%2812%2900776-3)

#### 5 &#8211; [Official Wholecell project git repo at GitHub](https://github.com/CovertLab/wholecell)

#### 6 &#8211; [Quora on computational genomics](http://www.quora.com/What-computational-problems-are-the-most-interesting-in-genome-informatics-today)

### Computer science books and references:

#### 1 &#8211; [Code Complete: A Practical Handbook of Software Construction, Second Edition](http://www.amazon.com/gp/product/0735619670/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0735619670&linkCode=as2&tag=codeunico-20&linkId=ESQXUVIGF4RA4YC3)<img style="border: none !important; margin: 0px !important;" src="http://ir-na.amazon-adsystem.com/e/ir?t=codeunico-20&l=as2&o=1&a=0735619670" alt="" width="1" height="1" border="0" />

#### 2 &#8211; [Design Patterns: Elements of Reusable Object-Oriented Software](http://www.amazon.com/gp/product/0201633612/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=0201633612&linkCode=as2&tag=codeunico-20&linkId=5JZ5ZWALQA2TSXQU)<img style="border: none !important; margin: 0px !important;" src="http://ir-na.amazon-adsystem.com/e/ir?t=codeunico-20&l=as2&o=1&a=0201633612" alt="" width="1" height="1" border="0" />

#### 3 &#8211; [Coding Horror Blog](http://blog.codinghorror.com/recommended-reading-for-developers/)

#### 4 &#8211; [Flyweight by Wikipedia](http://en.wikipedia.org/wiki/Flyweight_pattern)

#### 5 &#8211; [Composite by Wikipedia](http://en.wikipedia.org/wiki/Composite_pattern)

#### 6 &#8211; [Factory by Wikipedia](http://en.wikipedia.org/wiki/Factory_%28object-oriented_programming%29)