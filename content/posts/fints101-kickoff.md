---
title: "FinTS101 kickoff"
date: 2020-12-25T16:54:48+01:00
categories: ["fints101"]
tags: ["javascript", ]
---

## The TL;DR
- So this idea grew in me to create project for a tool that
  will support my daily work at StarFinanz and also teach me new technology.
- It will be implemented in JavaScript, I want to try web development.
- I have not chosen specific backend and frontend technologies.

## What it is about

FinTS, _Financial Transaction Services_, is a banking standard. One thing I have
to deal with is FinTS messages that get sent or received by our software. To
give you an idea how such a message looks typically, see the following as an
example:
```fints
HNHBK:1:3+000000000616+300+220120830267=168766141471CQCG=+2+220120830267=168766
141471CQCG=:2'HNVSK:998:3+PIN:2+998+1+2::7J/B9ULFfmQBAAA?+Vr9xqusWrAQA+1:201807
09:134108+2:2:13:@8@00000000:5:1+280:10050000:123456789:V:0:0+0'HNVSD:999:1+@36
4@HNSHK:2:4+PIN:2+920+1174665917332909+1+1+2::7J/B9ULFfmQBAAA?+Vr9xqusWrAQA+1+1
:20180709:134108+1:999:1+6:10:16+280:10050000:123456789:S:0:0'HIRMG:3:2+0010::N
achricht entgegengenommen.'HIRMS:4:2:3+0020::Der Auftrag wurde ausgeführt.'HITA
B:5:4:3+0+M:2:::::::::::Unregistriert 1::01514/654321::::::+M:1:::::::::::Handy
:*********4321:::::::'HNSHA:6:2+1174665917332909''HNHBS:7:1+2’
```
(taken from https://homebanking-hilfe.de/forum/topic.php?t=22058)

You see the problem? :) 

The message is a reply from a bank in return to a customer request message and
contains information about the available TAN authentication type. When working
on a support case it would be incredibly helpful to check at least syntactic
correctness of a message easily. For manual inspection of the values, I’m
thinking it would be great to mark a field and have the tool tell me what that
content type is supposed to be (dialogue number, actual account data, used
authentication type or whatnot). I can then decide if the value makes sense in
the context with help of the FinTS specification.

Currently, without such tool, the code often is reformatted manually, making the
structure more visible. Decrypting its contents becomes easier for the human. A
colleague has some editor-based macros in place to do part of that. I believe
FinTS101 would be a real time saver here!

An example of such a useful tool is regex101 which I have used many times when
writing or validating regex expressions (regexes sometimes being called
write-only code for a reason). If you don’t know it, bookmark it right away!

## FinTS101 ideas

So regex101 serves as inspiration and I'll adpot adopt the name. I love that
regex101 runs in my browser so fints101 will be a web application, too. (Did I
mention I want to learn JavaScript and web development?) I leave it as an open
decision as to how far and mature I’ll develop that thing. I’m happy if it turns
out as something useful for me. If there is more potential or work-related or
even public interest in it, I can well imagine to make that a really awesome web
app!

Another aspect is that I might want to continue learning in another area than
JS/web after finishing a certain FintS101 milestone. For now, I'll concentrate
on getting something to work at all!

So these are some ideas:
- There will be a parser for the messages. It will be useful to check if parsing
  is successful or not.
- Display the parsing tree.
- Would be great to being able to mark most of the fields by type, at least
  numerical, test, binary and so on, e.g. CSS-based (customizable) with
  different background colours
- on-demand information via mouse hover
- Semantic information (message/segment specific) sounds awesome but not sure
  it’s feasible in the scope I'm aiming for right now.
- Display related information from the FinTS specs.
- Deserialization of data which has its own format inside specific segments
  (there are several such formats).

As for technology, I want to learn JavsScript and will take this project as such
opportunity. (As someone coming from C++ I’d appreciate to start right away with
TypeScript for its stronger type system but I opt for doing one (or at least
fewer steps at a time.) I like the idea that the application will work in any
browser. Being a backend developer in the past (and future) I’ll probalby
involve Node.JS (which I want to get into, too, and [which I have played with
before](https://github.com/sebkraemer/wishlist-js)). The frontend could involve
React or Angular. (Angular, I believe, would force me to get into TypeScript,
hm.)

OK, so these are some ideas. Next step will be to have a closer look at tools
and libraries to work with.
