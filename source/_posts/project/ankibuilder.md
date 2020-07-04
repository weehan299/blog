---
title: Automated Anki Deck Builder
date: 2020-06-29 15:17:19
intro: "Because flashcards isn't fast enough"
tags:
- webdev
categories: 
- Project
---
Website of Anki Deck Builder can be found [here](https://pythonankibuilder.web.app/#/)

I have been using [Anki](https://apps.ankiweb.net/) flashcard software for most of my learning that requires me to memorise huge chunks of facts. I am generally quite satisfied with the product with the exception of the tedious process required for me to create any deck. 

So, I have decided to automate this process away by creating a script that automatically creates the deck for me, without having me to enter each card input individually

The script that i used can be found [here](https://github.com/weehan299/PythonDeckBuilder/blob/master/FlaskBackEnd/deck/anki.py).

This simple script alone helped me to save at least half an hour of my time everyday which can better spend on doing other things (like writing this post). I wanted to share this script with my peers who also uses anki often to aid their revision. As such, I decided to turn this script into a full-fledged website that allows everyday users of anki to benefit it.

The web application comes with its own login system which is powered by Google Firebase's Authentication Service. I also utilised Firestore's cloud database and storage system to allow users to safely store their decks in their accounts and download it whenever they decide to use it. 

All the users need to do is to copy and paste the questions they created into our website (as shown in the image), and the web app will generate the deck for them. The decks will then be placed in the profile page where users can either download or delete the deck. 

{% asset_img sampleanki.JPG %}

In my opinion, it has quite an inituitive user interface that allows novice users of anki to enjoy the automated benefits without needing to understand code.





