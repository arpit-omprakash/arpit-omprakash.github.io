---
title: "Game Development"
date: 2020-2-02
categories:
  - blog
tags:
  - Snake
  - pygame
  - turtle
  - game development
---

I had always dreamed about developing my own game to play but I never really looked into it in depth till a few days ago. At some point in my life earlier, I had started out with coding games but I remember giving up as I was ill prepared to do stuff and felt demotivated every time I encountered an error. This time, however, things were different. I first learnt how to write the snake game using turtle in python. Turtle is kind of an interesting library that lets you animate objects. It reminded of the good old 'logo' that I had aced when I was in 5th standard. Even our computer teacher was astonished by the programs that I had written. This made me a bit nostalgic and even more determined. Then I created the pong game from scratch in python using turtle. That made me really proud of myself in the end, it was a really satisfying experience and I absolutely loved every bit of it. Starting from figuring out the rules of the game, translating them into code and seeing the final product ready made me so happy. I had thought at that moment to share this joy by helping others realise what a beautiful journey this was and encourage them by maybe writing an article on how to or making a video to teach people.

I ended up thinking about a lot of other things eventually and one thing that made me really even more fascinated was to make a bot that can play the game. Then I stumbled upon it, the code that I had written in python using turtle was kind of crude and I thought it would be much easier to write the AI if I had a game written in pygame. Thus began the journey of converting the code from turtle to pygame. It was kind of easy apart from the confusing things like pygame's coordinate system and some other such stuff. Once it was done I started making a neural net that would play and I had thought of using the scores as fitness values and evolve the code using a genetic algorithm. I have had fairly good practice of genetic algorithms as both a programmer and a biology student and yes biology has indeed helped me in many cases while programming (apart from providing for my brain, duh). I was very confident that it would be a positive enterprise. Alas, it took me nearly 3 days and then I realised that my approach was fundamentally flawed as I was not able to generate models that are variable enough to introduce the variety required for evolving the neural net. Now, I have two ideas, the first one is to improve the neural net, work upon the issue and then use the genetic algorithm for learning. The second idea is to change my approach and use reinforcement learning to teach the game to the AI. I'll explore both the ideas and post updates on future posts.

After not being able to come up with a satisfactory AI (the AI I developed lasted for 2-3 seconds, collected food and soon bumped on a wall), I decided to put things on hold and come back to it later when I am feeling a bit better. Meanwhile I had plans for an article or a video as written above. As I had mentioned some time back, I am currently writing articles on medium and I thought that this would be an interesting article. Thus, I wrote this [article](https://medium.com/@omarpit26/making-a-simple-snake-game-in-python-part-1-42eb2890f0eb) explaining the whole pygame snake program. I hope people will surely give it a read and make their own games and enjoy. I thought making a video would be a bit too much of a task, but I recently got to know about the windows Xbox game recorder, so I think I can use that for the same. However, I'll do that once I have enough programs and enough time to explain each of them in small sized code fragments.  
Meet you here next time, maybe give my medium [page](https://medium.com/@omarpit26) a visit?