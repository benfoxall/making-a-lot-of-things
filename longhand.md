# Making a lot of things


Hi, I'm Ben.

I'm a developer/adventurer based in Oxford in the UK.

?? Slide - diff delta -> -2016+2017 -pusher+freelance 

It's a huge huge honour to be back here speaking again.  I say back here, because this isn't the first time I've spoken at FullStack. I've been coming here since 2014 - when it was called FutureJS and I got this really cool t-shirt.

After that, I came back to Full Stack Fest 2015, then in 2016 I snuck on stage in micro-talk.

I'm good at coming up with talks, but less good at choosing new clothes.

[DEMOS]
In the past, I've tended to give quite demo heavy talks, where I've tried to show different ways of interacting with the web.  Though today is not that talk, I decided that I wanted to give you something more useful that you could make more use of.  In several ways, this is going to be a bit of a meta talk about how you might go about exploring ideas and creating your own demos and hacks for things.

…if I was going to have a demo heavy talk, it'd be about THIS! this is a project that I worked on which uses esp8266'es to blah blah.

>> move to end
I've promised not to submit a talk next year.

FSF have promised that they'll accept at least one talk by someone in this room who hasn't ever spoken at a conference before.

And I promise you that I'll try and help with any ideas that you want to discuss.  This is my personal email - seriously, email me.

One of the things that has been interesting to speak here over the years is all the other things that have been going on whilst I have.  I'm Scottish, and in 2015 we were on the verge of IndyRef - it was really interesting, because I think the choice mirrored similar decisions with Cataluna and Spain.  In 2016 - the UK had just voted to leave the EU, for the first time I can remember; I've been deeply ashamed of being a British citizen.  This year I feel like I'm coming here in the shadow of something much bigger.  Last months terrorist attacks were a tragic beyond belief; I want you to know that the rest of the world crys with you, and admires your determination to fight terrorism with love.

The world's a bit shitty at the moment, which makes it more important than ever to maintain an open diverse, friendly and open tech community; and that starts here.

Right.

[making a lot of things]

Today I'm going to talk about making a lot of things.

Over the last few years, I've found myself building a lot of small hacks & projects.  I've started to call these #microhacks and I'm going to go through a few just now, so you get the idea of the kind of things that I'm talking about.

blah blah blah.

Why do I do this?  I've got a passion for front-end technology, and this gives me an opportunity to address that passion, and also it's let me explore ideas and learn how things feel.
^^ nope

(note to self, I think this might be quire similar to my need to write stuff own on a pad.  I can't really let things soak in unless I'm writing stuff down.
^^ nope

Why do I do this?  I'm quite driven by practical things.  And this is how I learn and explore ideas.

And I've been thinking that there's a couple of key features about this ind of micro project that make it uniquely powerful. I think firstly, it's got an explorative/discovery quality, and also a strong set of constraints, time, topic, etc.

And I think this drives the creativity of a project.

To me, this is most eloquently described by Igor Stravinsky – a German Composer from XXXXs, this is a long quote, but it's worth it.

…

I guess you might be able to summise this as "Constraint breeds creativity".

And this is what I'm going to talk about today, how we can alter the constraints on the way that we create things to change the creativity that we can achieve. 

I'm going to cover two different angles on this.  Firstly we're going to talk about the act of coding, on what constraints we can apply on our code to make it more explority and creative.  Then I'm going to talk about non-code things, and how we can focus on building small things to feel more satisfaction in the things that we create.

So, Code.

The code we write has very few constraints.  To create a simple page, we have a huge amount of bumf that we have to make and maintain before we actually get to the point of creating something.  And this is all the more complex when we factor in libraries, tools, build systems, datastores, external apis, best-practices and fitting everything together.

To be creative, you need to first understand the bounds and constraints of what you're creating, and then you can actually get down to making stuff.

One common way of handling this complexity is the idea of sandbox, or live repl.

The nice thing about this is that you're constrained [box around svelte data], and it's explorative [highlight text].

What we're going to explore today, is how we might go about making something like this, and what kind of creativity it can produce.

So, we're going to create this thing that I call a cell.  To one side of it, there's going to be a place that you can type in code, and on the other side - we're going to see the output of that code having been evaluated.

So, let's think about what that might look like.

   <textarea>     <iframe sandbox="allow-scripts">
   </textarea>    </iframe>


As our first pass at this, we might write something that takes the input from the text area, then wraps this up in some html, with a little helper function that can print the result in the frame.

This works.

We can put in any expression it'll evaluate it and we can see the result in the output. [demo].

However, this only works when the text area contains a single statement.  Where it would feel more natural to allow someone to put in multiple statements and build something a bit more complex.

To do this, we need to modify our code in some way so that we can pull out the value of the last statement in it.  We can do this by taking our text, and parsing it - so that we can access the underlying structure of the JavaScript.

We do this by generating an Abstract Syntax Tree - a structure that reprints the script that we've fed in.  We can then modify that tree and write out the modified script. Here, we've wrapped every single expression in a function expression.

We first use a library called esprima to generate our tree, then asa-tools to traverse and modify that tree, we can then use recast to write our updated script back into text.

There's actually a bunch of javascript tools that can do this for a bunch of languages, so you can similar things with css, shaders - if you're interested, check out astblah.

Now we just need to update our iframe wrapper to stash the last value it sees, which we can then print to the output.

And it works for multiple expressions now, and **feels** natural.

ok.  So now we've got this tool which lets us explore inside these tiny constraints and create stuff with.

And we can do a lot with this, even though it's completely constrained. (todo, demo).

…

## Adding More Cells

So, we've got this basic single computational unit,  how about we introduce more of them and see how they play together.

This is quite a common pattern for describing a series of steps to create an output.  For example: matlab, maple, jupyter - these are tools where you create a worksheet and are able to run particular elements as part of describing how they fit together.

To do this with our cells is a bit of a challenge because each one is running in it's own iframe.  Though again, we can use our AST to find out if there's any variable declarations, then we can use that to pass on to the next cell.

;;; sketchy ;;;

okay.

now we can actually take this to a new level, which is to analyse the input of each cell too, so we can see which cells are dependent on each other.  This allows us to do something **really** interesting, which is to generate a dependency graph between these cells - this means that we can selectively reevaluate nodes that are dependent on others.

This works pretty naturally. You can see how using this model, we're able to selectively evaluate each bit of code and only the dependent values update.

The critical thing here is that we've managed to keep this feeling natural, and we're still able to explore in little chunks which is something that we're not usually able to do.

This also introduces an interesting problem.

When a function isn't pure, like this one - this model kind of breaks down.

This is kind of hard to intuitively solve, other than having other languages like closure script or whatever.

So, what have we got now.  We've got an explorable cell-based coding tool, and lets have a look at some of the features of this.

… goes through some of the things that makes this nice.

This is based a lot from a project that Mike Bostok is working on which is d3.express - he's challenging the way that we can write code.

..Alternative entire section

When we're supporting more cells, we can do something interesting.  We can look at our syntax trees of each cell and compute a dependency graph.  This allows us to defer our cell evaluation


## Adding more people

There's basically two models for adding more people into something like this.

First, is allowing them to collaborate live on the code. This is super interesting, but kind of chaotic.

The other, is being able to fork/remix/copy is one of the great things about creating stuff on the web. You can take something else and tweak it.

By changing the way that we're thinking about how we edit code, we can do another kind of interesting thing.

Though the really interesting thing here is that because we've got a natural separation of the cells, we're able to update them intelligently based on whether a user has edited them.

Live demo: editing and following.

Live demo: pulling information from forks.

Wrap up cojs

So, I went a bit technical there,


POTENTIAL CHANGE TOMORROW - describe high-level the changes that are in each section rather than going into too much detail, then go straight to demos. It doesn't have to be instructional the whole way through and we want to focus on the exploration and constraint.


## /Part 1.

Okay, so what I've been covering here is how code doesn't have to be structured as we currently do it.  And although our ways of exploring and sharing ideas are awesome and powerful, there may be other ways of doing it.  What I'm going to move onto now is how to apply this kind of thinking to the things that we create.

## Part 2.

So, now we're going to move on from talking about the act of coding, to the act of making stuff, because they're two different things.

[ start, explore, finish ]

This is a basic timeline of a project/hack/side - what we've talked about is that explore part, how we can change the way that we go about coding to be more interactive and exploratory, but now we're going to.

{
	 * focus on the problem, not the solution
	 * identify the barriers - deploy, build, share
	 * ? isolated repetition, a pattern will emerge
	 * do things for the right reason
	 * be proud of what you do, because you're awesome
}

## Starting

I want you to do something.  I want you to think of a thing that you've heard over the last week that you've been inspired by.  Next, think of the smallest way that you could put that into practice.








final: making a lot of things.

I'm here to tell you today, that whatever you make, whatever you put into existence, you should be proud of.

---

Having a firm, but open-to-change, goal.  Fairy lights?

80/20 rule.  If you've deployed something at the start, you can just stop at the 80%


Bret victor?


Audio Analyser
https://cojs.co/?6y9b9

TODO - mention cojs earlier on, "and this is how I do this in cojs"

