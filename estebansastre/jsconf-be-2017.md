# My experience in JSCONF Belgium 2017.

This Thursday 29th June the Jsconf.be conference was celebrated in the beautiful city of **Brugge**, and I had the chance of going there! I had already taken a look at the speakers and I was interested in at least four talks so it was more than worth going.

The conferences started in the afternoon and where structured in two tracks not following any topic in particular, so I had to sacrifice some.

# Keynote

There was a big delay on the trains so I couldn't watch this piece in it's whole, the speaker [Peter Paul Koch](https://twitter.com/ppk?lang=es) was already halfway through his talk when I arrived. The remaining of the keynote was extremely interesting.

He talked about the all well known obesity of the web, pinpointing how the indiscriminated use of frameworks, build tools, libraries, was contaminating the web development environment, generating heavier and heavier websites that would take several seconds to download and render in high end devices, making almost impossible to access these sites to a big part of the world's population, having access to low end devices, unstable and sloppy network connections and not the highest throughput environments that we are used to in first world countries.

All this bloat is partially originated due to the advent of web developers trying to emulate the fully functionality of native desktop applications using web technologies.

Also, the uncertainty that frontend developers have to deal with when developing software in such an aggressive and hostile environment as the web was brought to the table. Maybe the reason why the so called **javascript fatigue** is partly originated because we frontend developers try to be taken seriously by overcreating tooling and patterns that exponentially increase the complexity of software.

Every idea was reflected in a deep and encouraging attitude. Far from being a rant, it was inspiring and full of hope towards the web. I very much enjoyed it I am already following the speaker to see what he will be up to.    


# Reactive Programming By Example.

A high level introduction to Reactive Programming by a couple of speakers: [Lander Verhack](https://twitter.com/LVerhack) and his boss. Using a funny and engaging way of question-answer format between them they gave a very simple overview on what is reactive programming by creating a fake page that would provide a simple search engine to query the Spotify API and show a list of songs, artists and albums.

At work now I work primarily with **Ember.js**, so the concepts explained in this talk reminded me very closely of the ember observers and computed properties, built on top of observers.

# The Era of Module Bundlers.

[Arun DSouza](https://twitter.com/amdsouza92) provided a walkthrough accross *task runners*, *build tools*, and *module bundlers*, enumerating the most commonly used ones, either today or historically: **grunt**, **gulp**, **browserify** and **webpack** were several examples.

At the end he focused on **webpack** for being the most complete tool, incorporating task running, bundling, minifying, and several other tasks in a single environment. Despite of being intersting, this talk did not provide me anything new other than learning some specifics of Webpack in order to do this or that task.

# How AI saved my life.

I really really enjoyed this talk. [Nick Trogh](https://twitter.com/nicktrog), evangelist in **Microsoft BeLux** gave an introduction to the super cool [Microsoft Cognitive Services API](https://azure.microsoft.com/en-us/services/cognitive-services/?v=17.25c) in Azure and using a website gave examples on how users can interact with this API.

The first example made use of the *Text Analytics API* to extract information about the sentiment, the subjective opinion of the emitter of the text. Being closer to zero negative and to one positive.

Then the *Computer Vision API* came along. Providing an initial small set of example images from a football team, the API detected if a new person belonged to that football team, also their age, emotion (*Emotion API*), gender, and a range of other parameters. All with a very close precision. It was amazing, there is a lot of potential on opening this kind of services to be used by third parties.

# Enterprise Javascript.

I think one of my tweets pretty much summarizes the general idea: *Oracle bundled jQuery+knockout.js+require.js+cordova = boom, OracleJET. We will see the usage in the future!*

Yet another frontend javascript framework (Peter Koch warned us!) to add to our collection. Oracle jumped on the frontend train with an open source hotchpotch of existing technologies wanting to generate community around it.

It has several interesting points: it wants to adapt Web Components, it is responsive by default, *it provides internationalization out of the box*... Only time will say where this new tool falls in.   


# How I hacked my coffee machine with JS.

 The time the speker spent talking was soaking wet in pure awesomeness. [Dominik Kundel](https://twitter.com/dkundel) explained the empowering process of being initially bored and deciding to crack open your flatmate's coffee machine to reverse engineer the microcontroller and learn how the buttons worked and communicated within.  

Once having done that he could hook a [Tessel](https://tessel.io/) microcontroller to the machine and develop a small service to allow him to start brewing coffee remotely.

Really cool home hacking project!


# Conclusions.

The overall level of the talks was introductory but I enjoyed almost all the talks very much. The one explaining Microsoft Cognitive Services API and the Coffee Machine Hacking were on the top, and also sparked my interest in Reactive Programming, I will have to look into that too.

Overall, it has been fun and enriching to attend, I bring several ideas to test at home. As a fun fact, looking around I didn't find a single linux user in the whole conference, I think I just saw a guy with emacs open! :P
