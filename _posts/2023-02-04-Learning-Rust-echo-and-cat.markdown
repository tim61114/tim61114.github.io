---
layout: post
title:  "Rust: echo and cat"
date:   2023-02-04 15:40:55 -0800
categories: github pages
---

## Introduction

I'm currently learning [Rust](https://www.rust-lang.org/). As a system language, it has modern language features and the performance close to C. But Rust is definitely a complex language and has a steep learning curve, so I decided to create this as a note and also a way for me to reflect on what I have learned.

## echo

refer to the [man page](https://linux.die.net/man/1/echo).
To limit the scope, I only implemented the "-n" flag, which is to not return a new line when this is present right after "echo"

### Intuitive approach

#### Code

{% highlight rust linenos %}
use std::env;

fn main() {
    let args: Vec<String> = env::args().skip(1).collect();
    let mut newline = true;
    for (i, arg) in args.iter().enumerate() {
        if i == 0 && arg == "-n" {
            newline = false;
            continue;
        }
        print!("{} ", arg);
    }
    
    if newline {
        println!();
    }
}
{% endhighlight %}

#### Explanation:

We'll need the std::env module to read command line arguments. 

env::args() returns an iterator of the arguments, including the name of the program, hence the skip(1). 
".collect()" collects the arguments into a vector of Strings for easy iteration.
We then iterate through the vector, notice that we used enumerate(), since we'll need the index to check if "-n" is present in the first location. We set boolean variable "newline" as false if "-n" is present.
Otherwise we will print the argument String with a space after the argument String.
If the newline variable is true ("-n" is not present at the beginning), then we'll print a new line, otherwise do nothing.

### More Concise approach

#### Code

{% highlight rust linenos %}
use std::env;

fn main() {
    let newline = env::args().nth(1) != Some("-n".to_string());
    let args: Vec<String> = env::args().skip(if newline { 1 } else { 2 }).collect();
    print!("{}{}", args.join(" "), if newline { "\n" } else { "" });
}
{% endhighlight %}

#### Explanation

The main reason the intuitive approach was not so consice is due to checking "-n", and the Rust iterator and type has many powerful feature to handle the case.
To check if the arguments after the program name starts with "-n", we use the "nth()" method of the iterator, which returns an Option of that data type. (In this case it's a Option<String>) If there are no arguments, then the return value of "nth()" will be None, and if the arguments does not start with "-n", "nth()" will return a "Some()" with something in it, which for us both means add a new line at the end.
We'll then collect the arguments, notice that we skip 1 if newline is true ("-n" is not found at the beginning), and 2 otherwise (skip "-n"), then collect the items into a vector.
Finally, we'll join the items in the vector with spaces, then add a new line at the end if "newline" is true, otherwise add an empty string.

## Cat
TBA