---
title: "Lockdown Update: Chess"
date: 2020-7-13
categories:
  - blog
tags:
  - lockdown
  - covid
  - chess
---

It's been a really long time now with the lockdown in place. I've been driven almost insane by this situation now. Everyday there are newer cases arising and the situation seems hopeless. Along with news like the semester getting cancelled and other stuff like doctors contracting the virus, the situation is mentally disturbing.

However, there is also a positive side to things. We are developing cyber infrastructure and many people are getting time to explore other interests except their academics or jobs. This has led to a significant rise in the number of people creating YouTube channels, Instagram vlogs and blogs like this. It is high time these things are encouraged. I personally think the current situation is an ideal conducive environment to let your creativity go wild and explore avenues other than the ones we are familiar with.

Coming to myself, this post is one half about my personal updates and one half apology. Yes, I won't be posting today. I have found myself to be in the extraordinary position of programming a chess engine. Ya, you heard it right, I've been hopelessly trying to build one. Thus, I didn't realize it was time to post for the blog. In fact, I just remembered about it shortly before I started writing this post.

So, I thought today I'll write a normal post and tomorrow, I'll write about the experiment I have in mind (won't tell you which one yet!). Stay tuned to read about it.

Okay. I need to explain a bit I guess. When I first went to college, I met two people (who have stayed on to be in my close friends circle) who insisted that I play chess. I thought it was just a random game (probably very easy as I used to beat my cousin at it when we were younger) and not much to play or learn. Boy was I wrong. I realized I was wrong when I installed an app called "Learn Chess with Dr. Wolf" by chess.com to play chess. It made me realize the complexity and mathematical marvel of the game. The first time I played online on Lichess (a few days after downloading the app), I made a to-do for my bucket list: *Develop a chess engine*.

For almost a week, I had no clue where to start or what to do. Reading up many resources including [Lichess Bots site](https://lichess.org/team/lichess-bots), [Quora thread](https://www.quora.com/How-difficult-is-it-to-build-an-AI-chess-engine), [Medium posts](https://medium.com/@andreasstckl/writing-a-chess-program-in-one-day-30daff4610ec), and most importantly, [Chess programming wiki](https://www.chessprogramming.org/Main_Page). These resources helped me gain an insight into what it is like to write a chess engine.

Finally the [sunfish engine](https://github.com/thomasahle/sunfish) made me realize that it's not really that difficult to write a chess engine. I learned about available libraries like [chessboard.js](https://chessboardjs.com/) and [chess.js](https://github.com/jhlywa/chess.js) and used them to create a simple site where I could play chess against myself (or anyone who would care to sit besides me and my laptop).

Using inspiration from a [blog post](https://www.freecodecamp.org/news/simple-chess-ai-step-by-step-1d55a9266977/) by Lauri Hartikka, I introduced four levels of chess playing AI on my now beautified [site](https://aceking007.github.io/chess/). You can say it was a wonderful and fruitful endeavour. Partly, yes. I had higher ambitions you see. I wanted a bot that would play against other human players on different chess sites.

I created a Lichess BOT account (to test my non-existent bot on Lichess). Currently, I'm working on writing a chess engine in python from scratch that I can integrate with my Lichess BOT account and play with. For the time being, I'm running stockfish 11 on my BOT account.

Experience the unique experience of playing chess on my site at https://aceking007.github.io/chess/  
I'll update when I'm done with my chess engine. Stay tuned for tomorrow's post!
