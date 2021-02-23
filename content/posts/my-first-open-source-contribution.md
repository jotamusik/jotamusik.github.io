---
title: "My First Open Source Contribution"
categories: [ "Open Source" ]
tags: [ "Personal Stories", "Node", "npm" ]
date: 2021-02-19
draft: false
---

This time I will share with you a personal experience that have supposed a great opportunity for me and that will help me
to improve and at the same time, make a little impact on other people at the same time.

So let's start from the beginning, weeks ago, me and [my project mates](https://leanmind.es/) were working on our rabbitmq messaging api definition,
and we were using [AsyncAPI](https://www.asyncapi.com/) to do it. For those who don't know, [AsyncAPI](https://www.asyncapi.com/) is a set of tools supporting a specification standard to
define asynchronous APIs. While we were working on our specification, we needed to validate the things that we were typing on our 2000 lines
definition file were correct, so our workflow was:

1. Modify the definition
2. Copy the whole definition
3. Paste it on [AsyncAPI Playground](https://playground.asyncapi.io/)
4. Check if the playground outputs an error
5. If an error occur, die looking for it...

![frustrated-gif](https://media.giphy.com/media/OOezqqxPB8aJ2/giphy.gif)

As you can imagine, by the third time, I was a little frustrated and start thinking how this workflow could be improved or
automated, then I started looking for validating CLIs on NPM but found nothing. Heartily, at this moment I was a little disappointed
because finding nothing meant that I won't be able to avoid that painful workflow I mention above...

![sad-gif](https://media.giphy.com/media/ChX3hzy5CkXsI/giphy.gif)

BUT, I just remembered that I'm a developer so why not doing a little research and try to find a way to automate the workflow by
myself. I jump into npm and found a validator for AsyncAPI definition schemas, nice tool, but sadly it wasn't what I
was looking for. Then, I just noticed that AsyncAPI has a package called @asyncapi/parser that could be used to validate a
specification file! For now, we've found a way to validate our definition, including the exact location of errors with a brief
description to highlight what's wrong, the only thing left is the automation part, so let's find a solution.

My first approach was to make a node script that read the file and with the parser, show the errors on the console. It's a
nice first try but not enough automated as I was looking for, so the next step was to create a CLI to be able to call my
validator wherever I want from my terminal and not having my nodejs project opened to only run this.

At this point one idea came to me, nodemon will be familiar for those who have developed software with nodejs, and this was kind
of auto-magic I was trying to reach. So I searched again on npm and found what I was looking for, a package that executes a function
when detects a change on a given file. I joined all the ideas and the result was a little handmade tool to auto-validate a definition file.

![happy-dance-gif](https://media.giphy.com/media/M8NYTBgXbWc7u/giphy.gif)

The story doesn't end here, after publishing the validator on npm, I figured out why such a great tool like AsyncAPI Playground, didn't
show the errors even when they have the parser package that implements this, so I jumped into [playground's code](https://github.com/asyncapi/playground) and found that they were
using [@asyncapi/parser](https://www.npmjs.com/package/@asyncapi/parser) but wasn't returning the error's description to the frontend, so I decided to make that little change and send
a Pull Request. Surprisingly, the changes I made were ok and accepted fast. Finally, I was contributing to an active open source
project for the first time!!

AsyncAPI as a community has a slack workspace, so I decided to share my validator CLI with the team, and it was received with open arms,
and the team commented that they would like to have a single CLI that manages all the different tools under one, and I was
invited to lead the initiative to develop that tool, and of course, I accepted, so here starts my journey as an Open Source
contributor!

This wouldn't have been possible without the support of my teammates and the kindness of the community, so thanks a lot for helping me to
give my best.

![hug-gif](https://media.giphy.com/media/QbkL9WuorOlgI/giphy.gif)
