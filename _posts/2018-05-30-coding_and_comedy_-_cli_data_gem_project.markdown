---
layout: post
title:      "Coding and Comedy - CLI Data Gem Project"
date:       2018-05-30 10:36:33 +0000
permalink:  coding_and_comedy_-_cli_data_gem_project
---


I've always enjoyed comedy and thought it'd be awesome if I could somehow tie it into my coding journey. I'd initially set out seeking a database of comedians and their jokes. It would've been cool to take a look at any given comedian  and see all his/her jokes with the relevant source info of when and where they were told. After a day of searching, I had stumbled upon Comedy Central's website, which is essentially next best thing (jokes.cc.com), a database of jokes by category. I decided it was probably not going to get better than that. 

The program as I first imagined it would run by having the CLI output a list of the categories. Then, the user would enter the associated number beside the category. Then, the list of all the jokes in that category would be displayed. The user would once again input a number associated the joke (title) of their choice. Finally, the joke would be displayed with the appropriate sourcing info. Next, I started the project by looking at the key objects and their attributes that would be necessary. I noted that the website is organized by category and each category has a list of jokes. Beyond the category level, each joke has a title and the link to the actual text of the joke. Lastly, I noted that the structure is thus atleast two levels deep. I then moved on to thinking about classes themselves.

**Description of the Classes**

*The Scraper Class*

This is the first class that needed to be created and is the most important class, as it will be directly responsible in extracting information from the website (i.e scraping) and then using that information to create objects of the the following classes, namely,  Category and Joke classes.

*The Category Class *

The Scraper class will scrape and instantiate all objects of this class. A list of this Class's objects will populate the first options presented to the user. Each object in this class has attributes of the title, i.e, the name of the category, and the link where all the associated jokes are shown.

*The Joke Class*

The Joke class is very similar to the Category Class. The Scraper Class object will be passed the link attribute of the category object.  The Scraper object will then make all associated Joke objects using the link. Joke objects will each have a title and link, where the text of the joke is stored.

Those were all of the objects that are necessary to get the basics of the CLI functioning. Next, I focused on creating and then streamlining the CLI.

**The CLI**

The CLI flow was pretty straightforward. I created a separate file for the CLI that would be responsible for all the logic of the execution, only running a *call* function in the *bin/jokegenerator* file. The *call* function would create all the objects of the Category class, welcome the user and pass on the torch to the *start* function.  The *start* function is the meat of the CLI.  The first iteration of the CLI displayed the list of categories (via a *print_joke_categories* function Then, created the appropriate Category object, whose link attribute was passed on to the Scraper class, which called the *make_jokes* function that created all the Joke objects. Then the CLI displayed the titles of all the Joke objects via the *print_joke_titles* function. Upon receiving user input, both the joke and category instances were sent to another function whose sole function was to display the joke (aptly entitled *display_joke*. That would be the end of the flow. The final step was to ask if the user wanted to play again and if yes, the process would call function start again.

**New Feature**

Upon getting the first iteration of the project working, I decided that a cool new feature to add to the program would be  a 'Tell me a joke' option. This option would appear first and simply pull a joke from the database at random and display it to the user. This is where the object-oriented aspect of the program came into play. With ease, I was able to look at all the objects of the Category and Joke class via the @@all array for each, where they were saved. I simply applied Ruby's *rand()* function to each array length and was able to have the program select a random category and a joke that could then be displayed.

**Final Thoughts**

This project allowed me to the see the value of object orientation and think through relationships between the various classes and objects. One last thing, that's worth mentioning was the importance of binding.pry in debugging the scrape as well as the program logic. Without that, it would've been impossible to debug and the project would've taken way longer. I've become a much bigger fan of the tool and have much more dexterous at using it. I thoroughly enjoyed both building the program from the inside and seeing the results from the outside as a user. Plus, I 've something to cheer me up anytime I'm feeling down or taking a break!








