# How to choose your next tech side project

# Introduction

Besides the day-by-day work, I like to always have an ongoing side project to keep my skills sharp. These don't have to be necessarily useful or directly linked to my current job, but serve as a distraction to throw some spare hours into.

Normally those projects have a bunch of common traits:

* They are purposely small.
* They focus on a single defined task.
* I try to add *at least* one element of novelty on them (language, framework, programming paradigm).
* Although this not always happens, *I need to see the point of working on it*, otherwise boredom easily arises.

I remember during college I started writing an ELF file parser (much like the **readelf** binary) and realized later that it was way better to use memory mapping instead of opening the file with file descriptors. 

Another example was a simply e-shop webapp using Ember.js & Laravel 5. The reason for choosing Laravel was because I was borrowing a friend's hosting so I was limited to a *LAMP* environment, and despite having a shell available an thus being able to install node.js or whatever I needed (without root, extra difficulty), the backend was simple enough to stick with PHP.

The point of giving these examples is to state that over the course of the years I've had better or worse ideas (many completely useless), but I've greatly enjoyed my invested time.

Then, the moment of complete despair comes when I run out of them, when I dismiss all those waiting on my *Possible Projects* backlog and can't find anything worth starting. If it is not useful enough, it is way too complex to invest so many hours; if it is interesting, it might have no direct application, and thus creating anxiety (e.g: ReasonML is very cool, but I should really learn Typescript properly..); if it is real-life "useful", it may be utterly boring (e.g: writing better unit tests in python/selenium).

It is not uncommon to spend a considerable amount of time thinking on which project to embark next. Since this is a recurring problem I have eventually decided to sketch out a mind map describing the process I go through when looking for something new to do. 

<br>

<img style="display: block; margin-left: auto" id="project-choice" src="project-choice.png" alt="project-choice">

<br>

I have divided the potential **Idea Triggers** in six groups, colored in different shades of green. Once a choice is made, the **Possible Actions** are in different shades of orange, all but the "Choose Stack" choice, which I'll explain later. 

Each **Idea Trigger** represents a specific area where you want to focus when it comes to find an idea for a project, and each **Possible Action** is the action you take accordingly. Notably, not all combinations make a lot of sense.

# Idea Triggers

## Useful for your current job

You want to invest your free time into skills that have a direct impact in the job you are currently pursuing, so the contribution pays off directly. The potential areas I tend to pay attention to are:

* Identify stack & languages used at work: as a developer we use a cluster of technologies we are may not be very acquainted with (you can't know everything). 

	Your backend might be an array of **microservices** running in **Docker** containers written in **Ruby**, **Python** and **Go**, each of them running in an **Nginx** server, using **RabbitMQ** for message passing, one of them using **Redis** for some caching and the frontend written in **React.js** & **Typescript**. It is unlikely even as a "Fullstack Developer" that you have well versed expertise in all these technologies, hence making each one of the bolded words a possible challenge to tackle. 

* Identify repetitive or annoying tasks that can be automated: in a developer's (or any IT job) daily tasks there's often plenty of room for automating tasks, providing you a good ground for identifying weak spots in the processes (e.g. inefficient build process) that you'd otherwise neglect, given this examples from my own experience:
  * `npm link`'ing multiple repositories at once in a single project or having nested `npm link` inside a single repo. You can write a tool for that!
  * Cloning the same docker barebone repository and having to change over and over the code here and there to customize it. You can have a docker template and extend it with custom code!
  * Doing some Excel columns manipulation manually, over and over again. You can write a script for that!

	And I am sure there are plenty of these examples you are already starting to realize..

* Identify a process related to your job you don't quite yet understand: you might be a frontend developer who writes commits, pushes them and forgets what are the further steps until the feature is shipped to the final product. You know someone writes the Q&A automated testing, and that a thing called TravisCI perform automatic tests & building, but never scratched the time to understand it. This can be a good entry point to learn steps in the software shipping process you are never directly involved.

## Useful for a prospective job

You might be looking for a new job that can be either related to your current one, or diverts a little, or tackles an entirely new domain you have never worked with. Despite computer science related jobs normally share a common trunk of knowledge, there are so many specializations that it would become impossible to become proficient in all of them.

However, it is not uncommon for a developer to slide from one to another. Let's take a few examples:

* You are a frontend developer working in a web application, you realize there are many security holes in the code & architecture and you decide to fix them. You become increasingly interested in multiple ways attackers can take advantage of a web application and therefore you start to know it's correspondent security measures (*XSS*, *SQL injection*, *RFI*, *LFI*, etc.. vs. *CSP*, *CORS*, *Character filtering*, etc..). After some joyful playing you decide you find more enjoyment and purpose in web security and want to jump to a **Security Consultant/Architect** position.
* You are a backend developer and your next task is to find out why the DB queries are so slow. You start to enjoy data modeling and database administration and start to research alternative databases: *NoSQL*, *GraphQL*, *Triplestores*, etc.. and decide you want to move into a **DBA** role.
* You are a developer who is bored of waiting so much for the CD/CI cycle and decide to create your own set of scripts and try to tweak Travis, Jenkins or whatever your organization has running. You realize you love scripting and DevOps and want to jump into a role to do that. 

The list can go on, those are just some examples that popped into mind to illustrate the leap that might come after curiosity brings us outside our regular role.


## Hot Topic

There is a constant stream of new technologies, languages, frameworks, etc.. Most of them fade away, but good ideas tend to stick around and often they originate from old computer science papers that are rescued from their dusty bookshelves (functional programming?).

In your daily job you may just perform mundane development tasks, but some topics in [hacker news](https://news.ycombinator.com/) or [lobste.rs](https://lobste.rs/) appear quite often and start to resonate with you. If they are around for that long, they might be worth giving a look. Topics like (my personal list):

* Blockchain/Ethereum
* IPFS
* WebAssembly
* Natural Language Processing
* Machine Learning (applied to whatever)
* Serverless

I personally find this *Idea Trigger* one of the hardest to follow, since I need to see an immediate practical application to them and also enjoy playing with. As an example I really like the idea of WebAssembly but I don't see real projects made with it yet and I am concerned with the potential security implications it may bring along (like Flash did).


## Specific tech

Stricty related to the previous **Hot Topic** section, but instead of paying attention to technologies that appear often in the media and thus having a direct job finding correlation, the **Specific Tech** section encompasses the technologies you feel curious about and have been staying in the back of your head for a long time, despite not having necessarily a direct application, more like a general "usefulness" vibe. 

Some items of my personal list are:

* Lisp
* ReasonML
* Elm
* Exploit development
* Reactive programming
* Malware analysis
* Advanced Shell Scripting (sed, awk, etc)
* Understand the OS booting process and how programs are loaded into memory


Again, although enjoyment is paramount, sticking to these projects is hard since I often don't see a direct return of investment in time & effort.


## Solve a real problem


By far the easiest to stick to. If you are lucky enough to come accross a real problem you realize you could automate or build a software solution for it, you hit a gold mine. You have the motivation to craft something new with the added advantage of being useful for yourself or a group of people. 

An example of this could be that you participate in a cultural center in your neighborhood, and you notice they are having some trouble managing the activities, letting people know about the offer and providing a way of proposing new ones. Hence, you roll up your sleeves and decide to create a web application to cover those use cases.

To get inspiration for people who solve real problems you can check [Indie Hackers](https://www.indiehackers.com/), they have plenty of nice projects to showcase.


## Interesting Topic

Instead of looking for a given technology to learn, this method has the inverse approach. I make a list of my different areas of interest besides computers, for example:

* Privacy
* Freedom of Speech
* Motorbikes
* Learning new spoken languages
* Different forms of education

And from that list I would look for a project that tackles a given topic or a combination of several.


# Possible Actions

Per each of the *Idea Triggers* there are one or many possible ways to start building knowledge as you can see in [the overview picture](#project-choice). Those are the *Possible Actions* to be taken.


## Online Course

A simple and straightforward way of getting up and running with a topic is to follow an online course. This is good if you want filtered, curated and structured content, since other people have taken care of that for you. The downsides of this approach are that you still have to filter whether it is useful and worth following, and not all worthy courses are for free. 

Normally you can either opt for the regular MOOCs offered by multiple universities and platforms online or access a more specific courses platform. Some platforms I've used in the past are:


### MOOC
* [Coursera](https://www.coursera.org/)
* [FutureLearn](https://www.futurelearn.com/)
* [MIT OpenCourseWare](https://ocw.mit.edu/index.htm)
* [Udacity](https://eu.udacity.com/)
* [edX](https://www.edx.org)
* [FreeCodeCamp](https://www.freecodecamp.org/)

### Other Platforms
* [Udemy](https://www.udemy.com/)
* [Pluralsight](https://www.pluralsight.com/)
* [Egghead](https://egghead.io/)
* [Frontend Masters](https://frontendmasters.com/)


## Github Project

[Github](https://github.com/) provides and excellent repository to hunt for ideas, especially if you don't want to start a project from scratch and would be better off by contributing to an already started one. By clicking in ["explore"](https://github.com/explore) you can find the latest trending repositories in the Open Source community, as well as being able to filter them by the language, framework and topic you're interested in.

Let's say I am initially interested in *security* and I want to learn *Python* by applying it to that topic. By the time I am writing the article, I see there are some interesting projects that fall into this category I would be quite thrilled to contribute to:

* [wifiphisher](https://github.com/wifiphisher/wifiphisher): a security tool that allows to create a rogue access point and force clients to authenticate & phish credentials.
* [AutoSploit](https://github.com/NullArray/AutoSploit): enables automation of massively exploiting remote hosts through metasploit.
* [Maltrail](https://github.com/stamparm/maltrail): malicious traffic analysis system.

As seen, Github provides a great "open source supermarket" where you can search through a wide range of different options and hopefully find one you resonate with and are willing to contribute.


## Related Jobs

If you are clear about the topic or technology you want to jump to, a great way of finding a pragmatical use for that knowledge would be to look for actual startups & companies that make use of them, or simply focus on the topic you are interested in (e.g: Farming, Privacy, E-Health, you name it). 

There is a big offer of job portals out there, so I will just give a small example: imagine you are sure you want to learn the Haskell language, and you are very interested in making a career in health care. A great way of starting is to look in [StackOverflow](https://stackoverflow.com/) and in [AngelList](https://angel.co) for jobs with *haskell* or *functional programming* keywords and if then start to fine tune the search. 

Luckily there will be some interesting projects to look at, and might give you ideas for yours.


## Code Challenges


Participating in code challenges, code jams and whatnot is a good exercise to get comfortable with the quirks of the language, but they normally don't represent real problems or situations you might encounter in a job. It can be cool to know how to implement a double linked list, or a breadth-first tree lookup, but most of those problems have been solved time ago and are now part of standar libraries. 

This is not to undervalue their utility, they have proven to be incredibly good to keep my skills sharp in many times, but I'd rather do projects that try to solve a real problem, even though it is a well-known solution (say a host port-scanner).

There are plenty of them, but some of the code challenge websites I've used to practice are:

* [HackerRank](https://www.hackerrank.com/)
* [CodeChef](https://www.codechef.com/)
* [TopCoder](https://www.topcoder.com/)

However there is a small one I've seen to give this pragmatical approach for code learning and I really like it: [Hackattic](https://hackattic.com/challenges).


## Write a Blog Post


I use to do this often for small ideas I have a general sense of what they are about but never took the time to organize and understand them clearly, at least the basic principles. Good examples or this are [undestanding requestAnimationFrame](https://estebansastre.com/understanding-requestanimationframe/), [understanding PGP](https://estebansastre.com/a-primer-on-pretty-good-privacy/) or [understanding routing in javascript](https://estebansastre.com/routing-in-javascript/).

This principle can be applied to anything you want to understand: if you're able to explain it means you get it.


## Misc

There are many other ways to start a side project, such as reading books, but those have simply proven not to be useful for me at the beginning. When I take a book about a topic it has to be one I am fairly familiar with and towards which I already profess genuine interest. Alas, I lose interest quite fast. 


# Conclusion

I am sure I am leaving a lot of good information there, any remarks and constructive criticism is more than welcome in the comment section.


Have fun!
