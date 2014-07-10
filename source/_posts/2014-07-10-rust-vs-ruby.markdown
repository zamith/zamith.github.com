---
layout: post
title: "Rust vs Ruby"
date: 2014-07-10 23:05
comments: true
categories: [rust, ruby, programming]
---

If you were expecting a showdown in which I go on to proclaim Ruby (or Rust) to
be the best language ever, you can stop reading now. I have been programming
Ruby for a few years and have only recently picked up Rust. This article serves
to show how much I (do not) know about Rust and how it compares to Ruby in my
point of view.

If you've never heard of it, [Rust](http://www.rust-lang.org/) is a language
championed by Mozilla that aims at replacing C++ as the language in which
Firefox in written.

While learning Rust I came across the [Rust for
Rubyists](http://www.rustforrubyists.com/book/index.html) book by Steve Klabnik,
which I recommend, and on that book there is small program that shows how to
create different kinds of monsters with the same interface. I tweaked it a bit
and implemented it in Ruby as well, so it can be compared.

## The Ruby version

``` ruby
class IndustrialRaverMonkey
  def initialize
    @life = 100
    @strength = 20
    @charisma = 10
    @weapon = 50
  end

  def attack
    puts "The monkey deals #{@strength} of damage"
  end
end

class DwarvenAngel
  def initialize
    @life = 100
    @strength = 50
    @charisma = 70
    @weapon = 50
  end

  def attack
    puts "The angel deals #{@strength} of damage"
  end
end

def call_attack(monsters)
  monsters.each(&:attack)
end

monkey = IndustrialRaverMonkey.new
angel = DwarvenAngel.new

monsters = [monkey, angel]
call_attack(monsters)
```

We have two monsters each with a set of characteristics and an `attack` method,
an instance of each of them is created, put into an array and finally they are
given order to attack. The `call_attack` method is there in order to correctly
replicate the Rust version, it is a bit redundant here.

## The Rust version

``` rust
trait Monster {
  fn attack(&self);
  fn new() -> Self;
}

#[allow(dead_code)]
struct IndustrialRaverMonkey {
  life: int,
  strength: int,
  charisma: int,
  weapon: int,
}

impl Monster for IndustrialRaverMonkey {
  fn attack(&self) {
    println!("The monkey deals {:d} of damage", self.strength)
  }

  fn new() -> IndustrialRaverMonkey {
    IndustrialRaverMonkey { life: 100, strength: 20, charisma: 10, weapon: 50 }
  }
}

#[allow(dead_code)]
struct DwarvenAngel {
  life: int,
  strength: int,
  charisma: int,
  weapon: int,
}

impl Monster for DwarvenAngel {
  fn attack(&self) {
    println!("The angel deals {:d} of damage", self.strength)
  }

  fn new() -> DwarvenAngel {
    DwarvenAngel { life: 100, strength: 50, charisma: 70, weapon: 50 }
  }
}

fn call_attack(monsters: &[&Monster]) {
  for monster in monsters.iter() {
    monster.attack();
  }
}

fn main() {
  let monkey:&IndustrialRaverMonkey = &Monster::new();
  let angel:&DwarvenAngel = &Monster::new();

  let monsters = [monkey as &Monster, angel as &Monster];

  call_attack(monsters);
}
```

The code is similar, but we have something called a `trait`, another thing
called `struct` and yes, a `main` function. Let's look a bit closer at each of
the differences.

## Comparing the implementations

The first and probably more obvious difference is that Ruby does not need an
interface definition, called a `trait` in Rust, it relies on duck typing. What
that means is that Ruby trusts you not to use a monsters that does not respond
to `attack`. On the other hand, Rust's compiler ensures that all the monsters
have that function, throwing an error otherwise. Nothing to fancy here, it's the
dynamic versus strong typing duality you are probably already aware of.

Even though Rust relies strongly on types, it does have a very good ability to
infer them, as you can see when the `monsters` variable is assigned on line X.

Another thing you don't have with Rust are classes, the way to get something
close to a class is to have a `struct`, which defines a set of variables, a
`trait` which defines a set of functions and mixing then with `impl TraitX for
StructY`.

Still on the topic of traits, you might have noticed that one of the functions
receives `&self` as a parameter and the other returns `Self`. Those are the
equivalent to an instance method and `initialize` in Ruby.

The last difference I find worthy of note is that in Ruby most variables are
pointers to the actual object and you have no control over that. In Rust you
have [three different](http://doc.rust-lang.org/0.11.0/guide-lifetimes.html)
[types of pointers](http://words.steveklabnik.com/pointers-in-rust-a-guide),
*owned*, *managed* and *borrowed*. Having programmed in C a few years ago, I
came to fear and respect pointers as the bringers of pain. Pointers in Rust are
much nicer, mostly because of the language philosophy to put safety first,
making it impossible for memory leaks and overflows to pass through the compile
phase. It is also great with concurrency through a couple of constructs called
[tasks and channels](http://doc.rust-lang.org/0.11.0/guide-tasks.html).

It obvious that the Rust implementation has more code, but it also adds an extra
layer of safety, which you might or might not want.

## Private by default

To be fair, Rust adds two "hidden" features. First, all variables are immutable
by default and you have to make them explicitly mutable if you want.

```
let mut x = 1;
```

Secondly, all functions, structs and traits are private by default, so if I was
to try and use them in another file:

``` rust
#![feature(globs)]
use monsters::*;
mod monsters;

fn main() {
  let monkey:&IndustrialRaverMonkey = &Monster::new();
  let angel:&DwarvenAngel = &Monster::new();

  let monsters = [monkey as &Monster, angel as &Monster];

  call_attack(monsters);
}
```

I would get a bunch of compile time errors such as:

```
error: use of undeclared type name `IndustrialRaverMonkey`
```

Which just to prove the point can be fixed with:

```
pub struct IndustrialRaverMonkey {
  life: int,
  strength: int,
  charisma: int,
  weapon: int,
}
```

## Conclusion

I have been fiddling with Rust for a very small amount of time, but I'm really
liking how conceptually different it is from Ruby. It is a great language to
learn some new ideas, and also to go down a level to where you have to use
pointers.
