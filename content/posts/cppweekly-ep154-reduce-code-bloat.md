---
title: "C++ Weekly - Ep 154 - One Simple Trick For Reducing Code Bloat"
date: 2020-12-12T21:56:51+01:00
refLink: "https://youtu.be/D8eCPl2zit4"
categories: ["c++weekly"]
tags: ["c++", "tl;dr"]
---

A friend sent me the link to [this c++ weekly
episode](https://youtu.be/D8eCPl2zit4) and I followed that clickbait. Just
recently I met the topic of compiler-generated functions at work so the episode
caught some special interest.

So what is it about already? In a nutshell, the episode is about the different
amount of compiler-generated assembly output depending on the (non-)definition
of the destructor. (I just wonder. Probably this will apply to other
non-generated functions as well.)

See this
```cpp
struct S {
   std::string s;
   // ~S();
};

int main()
{
  std::vector<S> vec;
  vec.emplace_back();
  vec.emplace_back();
}
```
vs. changed `S` destructor here
```cpp
struct S {
   std::string s;
   ~S();
};
S::~S() {}
```

Essentially, the episode noted the following:
- The second case generated more assembly code, especially it contained an
  explicit calls to the destructor function.
- This did not always happen, e.g. if `S` contained a native type the destructor
  call was optimized away (with `-O3`).
- The rule of zero was advertised.

I refrain from getting into more details of what happened when, since that would
not be TL;DR-ish and also because _the results have changed since the episode
was published almost two years ago_.

For me, the episode was a starter to dive into the _trivially-desctructible_
topic which crossed my way before, I believe also in one of Jason's episodes.
Was that about multiple destructor's feature in C++20?

My personal takeaways:
- It's a difference if a class is cleaned up by excecuting a function (i.e.
  destructor) and then freeing the memory of just freeing the memory, saving a
  possibly empty function call. Reading it that way, that appears obvious now.
  The insight for me is apparently that it is possible to free an object without
  calling _any_ destrcutor.
- ~~Just freeing the memory, as I understand it now, this is what _trivially
  destructible_ means.~~ I'm not sure about this any more, I read up on the
  topic a bit and by definition, e.g. the `struct S` with a `std::string` is
  never trivially destructible since `std::string` is not trivially
  destructible.
- Never underestimate the power of compiler optimization. Even what's not
  optimized today, might get optimized tomorrow.

Maybe it's worth to comment on the last point. In code reviews I've seen people
argue relentlessly for optimizing code in favor of _assumed_ perfomance (and
possible C++ developers are (in)famous for it). While I would agree that in this
example (or not ever if not needed, for that matter), you should define an empty
destrutor, don't design your code around performance. Clarity for example is
often favorable and it's not clear that even a performance hit that exists with
proof won't get an optimization in the future.

Hope that was TL;DR enough and not too much of a full blog post ;)
I also hope it was useful to you.

References:
- C++ Weekly episode #154: https://youtu.be/D8eCPl2zit4
- https://en.cppreference.com/w/cpp/language/destructor#Trivial_destructor
- C++17 standard, [§6.8.5 Object
  Lifetime](https://timsong-cpp.github.io/cppwp/n4659/basic.life#5)

Note to self: I'd really like to follow up on §6.8.5 and what it means exactly.
