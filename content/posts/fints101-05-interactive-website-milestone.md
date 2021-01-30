---
title: "FinTS101 Ep. 5: Interactive website milestone"
date: 2021-01-29T15:19:31+01:00
categories: ["fints101"]
tags: ["javascript", "project" ]
---

Today I'm reporting back to tell you about the latest code changes which result
in a simple but interactive website that displays the colored parse result as
shown before. (Spoiler: you can see the result at the end of this post.)

# The TL;DR

* The application was split into frontend and backend: The website has a text
  field and a submit button, sending asynchronously a request to a parser REST
  endpoint and displaying the result on the same page.
* HTML5 and node.js builtin functionality were used to avoid new dependencies.

# Goal: Interactivity

The previous implementation worked by parsing a fixed input pattern and
returning its parse result whenever the page was loaded. This was OK to verify
basic working of the parser and to develop the CSS based markup concept that
delivers colored output depending on the used parser rules.

With a goal of a [regex101](https://regex101.com) experience in mind and to get
a working solution for me for basic daily tasks, I wanted to make the "webapp"
(you cannot really call it that yet) interactive, as soon as possible. For me,
that means that I can choose the input and get the parse output without any
hassle. *Hassle* here means that I don't even want to switch between pages.

# Incarnation of dedicated frontend and backend

I'm more a backend than a frontend guy and want to explore current cloud
technologies so it was clear to me from the start that this project would
*(should)* work by having a decicated backend. I also started a node.js based
project as a JavaScript entry point so starting there felt a bit familiar.

That being said, I see that using the application without running or relying on
a dedicated service is highly desirable. Surely browsers will be powerful enough
to run the entire thing so my plans include to create a build and deployment
process for running fints101 browser-only.

# Framework decisions

For the frontend, I was confronted to look into AJAX (which I first heard about
decades ago before being a "real" developer but never quite got it) and jQuery
which I sometimes heard was not so popular any more, but it seems many still use
it also for new projects. Again I want to see if builtin functionality is
sufficient and after going a bit deeper I chose html5's *fetch()* method which
is a never and more flexible way to do some of the things that AJAX provides.
Basically it allows to make REST calls as JavaScript promises and so can be
integrated directly into the web page.

It goes without much saying that a web application backend will be REST based.
Pluralsight courses and most tutorials usually introduce Express.JS framework
for writing REST backends in Node.JS. This is understandable, it simplifies so
many things when handling different routes, middleware and more. I however want
to keep dependencies low (at least for now) so I am sticking with what Node
provides builtin. As an alternative to Express I also consider OpenAPI. I does
not introduce new runtime dependencies and has support for multiple languages.
This will make building a REST service with the same API in e.g. C# possibly
easier (me dreaming of experimenting with Blazor). I also read it can generate
test classes for the defined API.

# The code

## The frontend

The frontend shall do the following:
* allow entering the data
* send the data as parse request to the REST service
* display result, preferably without reloading the page

Building the form with button really reminded me of early html days when I
played around with simple web pages at the end of the 90s . That were different
browser times!

The html (returned by node) starts with a html form which contains a textarea
with the input. Its `name` attribute, *fintsInput*, will be the name for the
JSON object property in the REST GET request. Note that the REST request type,
*GET*, and the target URL are not specified in the form but handled elsewhere.

Two `<div>` fields, `resultDiv` and `resultStatus`, are placed below the form to
hold the result and request status.

The functionality is contained in a script that gets executed when pressing
*Submit*:

* Take the form data from the `textarea` field and build the string for the GET
  request.
* Execute fetch() for the GET request.
* Place the result in `resultDiv` when its ready. This is nonblocking thanks to
  promise.
* Prevent default form action which would send its own request and reload the
  page.

```html
<label for="fintsInputArea">FinTS message</label>
<form id="fintsForm">
<textarea id="fintsInputArea" name="fintsInput" rows="10" cols="80" padding="3" autofocus>HIBNK:1:1:1</textarea>
<p>
<button type="submit" name="submitButton" value="Submit">Submit</button>
<button type="reset" name="resetButton" value="Reset">Reset</button>
</form>

<p>
<div id="resultDiv"></div>
<p>
<div id="resultStatus"></div>

<script type="text/javascript">
  const form = document.getElementById("fintsForm");
  const sb = form.elements["submitButton"];
  sb.addEventListener("click", event => {
    event.preventDefault();

    const parseInput = form.elements["fintsInputArea"].value;
    const request = new Request("/parse?fintsInput=" + encodeURIComponent(parseInput) );
    fetch(request)
    .then( responseData => { return responseData.text(); } )
    .then( parseResult => {
      let resultDiv = document.getElementById("resultDiv");
      resultDiv.innerHTML = parseResult;
    });
  });
</script>
```

## Node.js backend

As before, the code is based on Node's buitin `http` module. It is complemented
by an explicit check for the HTTP `GET` method at the `/parse` URL.

With help of the `url` module, the form data parsed and the `fintsInput` part is
extracted. It is then used as FinTS parser input where a hard-coded value was
used before.

Remember, the ANTLR visitor creates html output so it can be put directly into
the request result for later display in the browser.

```javascript
http.createServer(function (req, res) {
  const reqUrl = url.parse(req.url, true);
  if (req.url === '/style.css') {
    // as before
  }
  else if (req.method === "GET" && reqUrl.pathname === "/parse") {
    // parse form data and send result html
    const parseData = reqUrl.query;
    const input = parseData.fintsInput; // e.g. "HIBNK:1:1:1"
    
    const parser = fints.getParser(input);
    const treeSegmentkopf = parser.segmentkopf();

    const ruleNames = parser.ruleNames;
    const symbolicNames = parser.symbolicNames;
    const visitResult = treeSegmentkopf.accept(new fints.HtmlVisitor(ruleNames, symbolicNames));

    res.writeHead(200, { 'Content-Type': 'text/html' });
    res.write(visitResult);
    res.end();
  }
  else {
    // html is delivered here
  }
  ```

The html is also completely delivered by the backend as "raw" html without e.g.
a templating engine. This is to avoid possibly heavy dependencies and like the
backend, this leaves room for experiments with other frameworks later. For the
frontend, I might give Angular or React a try.

## Result

After all the talk, the result kind of looks unspectacular but once more I'm
quite happy to see things working in the end after solving each intermediate
step 🤩

![screencast](/img/fints101/fints101-first-interactive-frontend.gif)

# Future work

Nothing is decided yet but these are some short- to midterm goals.

The following two go hand in hand:
* Are we ready for tests?<br> I consider myself an engineer who always praised
  the importance and usefulness of automated testing for various reasons. Having
  no tests yet kind of begins to have a bad taste.
* I have not dealt with error mechanisms of ANTLR but of course I want to detect
  if the parsing process encountered problems on its way.

Parts for improved user experience which probably have to wait a bit:
* avoid submit button, just reload after some time
  * on error, display latest succesful run or place where it failed, when
    possible
* enable drag-and-drop into HTML form
* browser-only version (no backend service needed)

For better readability of FinTS messages I want to add line breaks at certain
points. Actually that was one of the primary aims before creating the FinTS101
project.

# References

I decided to put in some references for looking up later and for you to follow
if interested.

* [Eloquent JavaScript, Chapter "HTTP and
  Forms"](https://eloquentjavascript.net/18_http.html) -- pretty good free
  online book, also has a chapter about callbacks, asynchronosity and JS
  promises
* [The form object at
  w3schools.com](https://www.w3schools.com/jsref/dom_obj_form.asp) -- a really
  nice reference throughout
* [fetch() at
  developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Basic_concepts)
  -- also very nice reference
* [How to use NodeJS without frameworks and external
  libraries](https://adityasridhar.com/posts/how-to-use-nodejs-without-frameworks-and-external-libraries)
  -- this helped in creating the REST endpoint
* [Submit Form in React without jQuery
  AJAX](https://www.pluralsight.com/guides/submit-form-react-without-jquery-ajax#module-thefetchapi)
  -- this helped with fetch*()
* [HTML Ajax](https://www.w3schools.com/xml/ajax_intro.asp) Just as comparison
  to the more current and preferred fetch()
* https://medium.com/javascript-in-plain-english/parsing-post-data-3-different-ways-in-node-js-e39d9d11ba8
* Pluralsight courses I watched or skimmed over (not necessarily for this part
  of my JS journey):
  * [Introduction to Web
    Development](https://app.pluralsight.com/library/courses/web-development-intro/table-of-contents)
  * [Front-End Web Development Quick Start With HTML5, CSS, and
    JavaScript](https://app.pluralsight.com/course-player?clipId=3fc2827e-c1e5-46ce-b9fb-2e5a27c10653)
  * [JavaScript
    Fundamentals](https://app.pluralsight.com/library/courses/javascript-fundamentals)
  * [Advanced
    JavaScript](https://app.pluralsight.com/library/courses/advanced-javascript)
    -- great part about callbacks vs. promises and synchronous vs. asynchronous
    computing and thinking