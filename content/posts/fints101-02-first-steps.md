---
title: "FinTS101: Choosing the tool"
author: "Sebastian Krämer"
date: 2020-12-28T16:27:31+01:00
lastMod: 2020-12-28T16:27:31+01:00
categories: ["fints101"]
tags: ["javascript", "project" ]
---

## The TL;DR
- I decided to use a parser generator for the project instead of writing my own. So a lot of code writing is saved by defining grammar rules.
- A decision must be made between CFG and PEG based parsers.
- The decision today is to use ANTLR for generating the parser.

## Getting started: evaluation

OK so how do we bootstrap. that thing?

I came to the conclusion quickly that I wouldn’t want to write the parser
myself. Lexing and parsing is such a well-understood process that several
techniques and generators are out there to help the typical task of creating a
parser based on a provided grammar.

Bonus properties of such are tool are
- gives me a parse tree out of the box
- active development, community support _(you don’t want to get stuck somewhere without any help available anywhere)_
- multiple target languages (write the grammar once, get code for multiple languages) (could be beneficial to have someting to integrate into the existing C++ code base at work, but it’s quite uncertain if there is any point in that).

A quick search led me to the really useful overview _[Parsing in JavaScript](https://tomassetti.me/parsing-in-javascript)_.

### ANTLR
[ANTLR](https://www.antlr.org/), the first candidate of CFG based grammar generators, looked quite promising with apparent maturity, community and JS and C++ among the target languages. I'd have to install Java to generate the parser but mkay. Due to its versatility, it might be reasonable to learn it and use again in other projects.

### APG
The second in row, [APG](http://coasttocoastresearch.com/overview), was appealing for its interesting extensions to the BNF grammar, e.g. lookahead for conditional checks. Not sure how to handle it with the LR(*) parser in ANTLR although I guess people do solve with ANTLR similar problems to what I intend to do. The project’s comment about APG being used by telefommunication companies was a plus, I guessed that meant it’s well suited for protocols.

## My decision 
I felt unclear about the decision regarding CFG vs. PEG parsing, I only heard about the latter recently, incidentally in a conversation with a friend who mentioned the [PEGTL](https://github.com/taocpp/PEGTL) C++ library. PEG grammars avoid ambiguity when choosing a grammar rule, I count it as a plus although you read everywhere that the CFG conflicts can be and are coped with. At this point my mind was stuck at any of the candidates that could also target C++ code and I thought I could not really go wrong with something CFG based.

I won’t go into details of the other candidates, for me it was between APG and ANTLR. Searching the internet for some scenarios revealed that while ANTLR is quite known and used, there were hardly any results for APG, and also the projects at github seem more like a years-old data dump instead of an active project although some PR was merged some time ago. This doesn’t mean it’s not stable but it left kind of a bad taste. That may be typical for a library that is released by a company into the public after the company itself supports the code. The notation for ABNF (extended grammar feature) is after an RFC but I found it a bit less readable than some other candidates. 

Finally, I decided to go with.. you guessed it, ANTLR. I felt I spend too much time already evaluating it for what little project I-m about to start.

Good. So next up I’ll write about setting up my project and writing the first lines of code.
