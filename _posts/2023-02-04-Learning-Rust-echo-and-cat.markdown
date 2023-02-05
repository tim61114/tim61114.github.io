---
layout: post
title:  "Rust: echo and cat"
date:   2023-02-04 15:40:55 -0800
categories: github pages
---

## Introduction

I'm currently learning [Rust](https://www.rust-lang.org/). As a system language, it has modern language features and a performance close to C. But Rust is definitely a complex language and has a steep learning curve, and I decided to create this as a note and also as a way for me to reflect on what I have learned.

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
We then iterate through the vector, notice that we used enumerate(), since we'll need the index to check if "-n" is present in the first location. We set the boolean variable "newline" to false if "-n" is present, otherwise we will print the argument String with a space right after.
Finally, if the newline variable is true ("-n" is not present at the beginning), then we'll add a new line, otherwise do nothing.

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

The main reason the intuitive approach was not so concise is due to checking "-n", and the Rust iterator and type has many powerful features to handle the case.
To check if the arguments after the program name start with "-n", we use the "nth()" method of the iterator, which returns an Option of that data type. (In this case, it's an Option of String) If there are no arguments, then the return value of "nth()" will be None, and if the arguments do not start with "-n", "nth()" will return a "Some()" with something in it, which for us both means to add a new line at the end.
We'll then collect the arguments, notice that we skip 1 if newline is true ("-n" is not found at the beginning), and 2 otherwise (skip "-n"), then collect the items into a vector.
Finally, we'll join the items in the vector with spaces, then add a new line at the end if "newline" is true, otherwise add an empty string.

## cat

refer to the [man page](https://linux.die.net/man/1/cat)
For this one, I'm only implementing printing the file to stdout.

### Short approach

#### Code
{% highlight rust linenos %}
use std::env;
use std::fs;

fn main() {
    for filename in env::args().skip(1) {
        let contents = fs::read_to_string(filename).expect("File not found");
        print!("{}", contents);
    }
}
{% endhighlight %}

#### Explanation
The intuition of this approach is really simple, read the whole file into one single string and print it out. If the file cannot be found then print "File not found".
For reading command line arguments, we'll need the "std::env" module we previously used in echo. We'll also need the "std::fs" for reading a file.
We'll traverse the command line arguments by using the "env::args()" iterator and skip the first item (the program name), then use "fs::read_to_string(filename)" to read the file. "expect("File not found")" is one of the methods to handle the return type "Result" from "read_to_string()". 

In the echo example, we used a different type named "Option", which is a little bit different from "Result". Option is for handling types where the value might not exist, Result is for handling the case where the "result" might be an error.
Using "read_to_string()" as an example, we expect the return value to be a single string. However, if the file is absent and we weren't able to read from the file, we would say this function failed from a higher level due to the file being absent.

This approach also has one major flaw. Since the read_to_string() reads the entire file and stores it in memory at once, this could cause memory problems. We'll tackle this in the next approach.

### Line-by-line approach

#### Code
{% highlight rust linenos %}
use std::env;
use std::fs::File;
use std::io::BufRead;
use std::io::BufReader;

fn main() {
    for filename in env::args().skip(1) {
        let file = File::open(filename).expect("File not found");
        let reader = BufReader::new(file);
        for line in reader.lines(){
            let line = line.expect("Error reading line");
            println!("{}", line);
        }
    }
}
{% endhighlight %}

#### Explanation
This looks more like the traditional way of reading a file in other languages, which is to read the file, then read it by creating a buffered reader, then read it line by line and print it. 
Notice that in the inner for loop, we redeclared and therefore shadowed line to be the actual string instead of the item from reader.lines(), which is a Result type object. Shadowing is fine in Rust. 


And there we have it, echo and cat explained.
T