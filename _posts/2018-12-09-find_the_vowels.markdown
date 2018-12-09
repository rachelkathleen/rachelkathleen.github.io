---
layout: post
title:      "Find the Vowels"
date:       2018-12-09 05:42:44 +0000
permalink:  find_the_vowels
---


It's a Saturday night - and what else would a newbie coding student be doing other than working on problems in Codewars?

Codewars is my preferred practice resource for a few reasons – chiefly because once a kata is solved, I get to see other, often more elegant ways to solve the problem.  Working through various kata has introduced me to a number a cool methods and bits of code – `reduce` and `inject` are recent favorites.

Here are the instructions for a  [kata](https://www.codewars.com/kata/find-the-vowels/train/ruby) I just solved:

> We want to know the index of the vowels in a given word, for example, there are two vowels in the word super (the second and fourth letters).
> 
> So given a string "super", we should return a list of [2, 4].
> 
> Some examples:
> Mmmm  => []
> Super => [2,4]
> Apple => [1,5]
> YoMama -> [1,2,4,6]
> NOTE: Vowels in this context refers to English Language Vowels - a e i o u y
> 
> NOTE: this is indexed from [1..n] (not zero indexed!)

My first attempt was a long, messy piece of code that yielded an even longer error message.

```
def vowel_indices(word)
  answer = [ ]
  word_as_array = word.split(//)
  word_as_array.map do |letter|
  answer << word_as_array[letter]
  end
  answer
end
```

I had a bit of a think (and a few fruitless searches for a simple method to identify vowels in a string) and went back to a blog post on Regexp. Regular expression still feels like Greek - but it was worth a go.  After refining, and refactoring this was my final solution:

```
def vowel_indices(word)
  word.chars.map.with_index(1) {|letter, index| index if /[aeiouy]/i =~ letter }.compact
end
```








