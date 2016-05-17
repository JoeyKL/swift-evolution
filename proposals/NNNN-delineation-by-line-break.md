# Allow delineation of entries by line break for comma separated lists

* Proposal: [SE-NNNN](https://github.com/apple/swift-evolution/blob/master/proposals/NNNN-delineation-by-line-break.md)
* Author: [Joey Kellison-Linn](https://github.com/JoeyKL)
* Status: **[Awaiting review](#rationale)**
* Review manager: TBD

## Introduction

It is common when creating a large array/dictionary literal or tuple to spread the entries out across multiple lines:

```swift
let reasonsWhyTheFallOfTheBerlinWallDidNotBringAboutTheEndOfWalls = [
    "The Berlin Wall was only one wall",
    "Not all walls are connected to each other",
	"Some walls are not even in Germany",
	"People are always building walls",
	"Reagan demanded that Gorbachev 'tear down this wall,' not 'tear down all walls'",
	"There are many kinds of walls"
]
```

By far the most common syntax for this is a single entry per line. Because each entry is implicitly delineated by its own line, the comma at the end of each expression is included only as compiler necessity. Without the commas, the array is no less readable, and the programmer does not need to worry about placing a comma at the end of each entry:

```swift
let reasonsWhyTimothyMcVeighShouldNotBeOnAStamp = [
	"It’s only been 14 years since his death"
	"McVeigh was a harsh and outspoken critic of the United States government"
	"He planned and carried out the Oklahoma City bombing"
	"We could use some diversity among the people our government chooses to honor with commemorative stamps"
    "He doesn’t have a recognizable face"
]
```

Swift-evolution thread: [link to the discussion thread for that proposal](https://lists.swift.org/pipermail/swift-evolution)

## Motivation

Commas at the end of entry in a multi-line literal are unnecessary for the same reason as semicolons at the end of each statement in a multi-line code-block: line breaks already do the job of delineating expressions. Semicolons were removed because it is redundant and a waste of time to type a semicolon at the end of every statement when the statement was obviously ended by a new line. Removing the semicolons makes for cleaner looking code, as it gets rid of unnecessary visual clutter. In the same way, removing the need for commas at the end of each entry in a array, dictionary, tuple, or list of function parameters eliminates pointless redundancy and makes for cleaner code.

Additionally, one of the problems with delineating multi-line collections with commas is that the formatting of the last entry in the array, which does not need a comma, is inconsistent with the rest, adding slight friction to changing the code to add more entries to the array by needing to update the format of that entry. Swift solves this problem as most languages do, by allowing for a trailing comma for the last entry in an array, keeping the format consistent. This works fine, but it looks slightly awkward for single line literals (e.g. `[1, 2, 5,]`) and Swift does not allow trailing commas for tuples and lists of function parameters, which is the topic of another open proposal. This problem instead best solved by eliminating the need for commas entirely.

## Proposed solution

Allow an entry in an array, dictionary, or tuple, or a parameter in a function, to be terminated by a line break. Commas would still be allowed to terminate an entry that would already be otherwise terminated by a line break, just as semicolons are allowed to terminated. The would simply be unnecessary.

This would result in array literals that look like this:

```swift
let friendlyList = [
    "Hi"
    "how"
    "are"
    "you?"
]
```

Dictionaries that look like this:

```swift
let questionsAndAnswers = [
    "Got any fives?":             "Go fish"
    "How do you sleep at night?": "Haunted by faces"
    "Spare some change?":         "Sorry, none on me"
    "Hi how are you?":            "Never better!"
    "Are you alright?":           "I've lost much blood"
]
```

Tuples look like this:

```swift
let secretCode = (
    whatISay:  "Never better!"
    whatIMean: "Please help me."
)
```

And function calls (if you choose to use the syntax) look like this:

```swift
injuredPerson.drive(
    to:        hospital
    inVehicle: ambulance
    speed:     veryFast
)
```

If this is added for all comma separated lists, which would be good from a consistency standpoint, then generic parameterization would be included too, though multi-line expressions for these are very uncommon (to my knowledge):

```swift
let strangeEmptyDictionary = Dictionary<
    String
    String
>()
```

There you go.

Your first impression may be that these lists look empty, or broken, or incomplete. Try to resist this. It may take a small amount of getting used to, but just as with semicolons, once they are eliminated, the result will be cleaner code.

## Detailed design

This is an addition to the specification for array-literal-items, dictionary-literal-items, expression-element-list, parameter-list, generic-parameter-list, and generic-argument-list, to allow each to consist of an item, a new-line, and the remainder of the list.

Since unnecessary white space is ignored, putting a comma at the end of an entry should not cause problems, as it will simply cause the line break to be ignored.

## Impact on existing code

This will have no impact on existing code, as delineating literals and parenthetical expressions by commas and line breaks will still be allowed. Developers may freely and voluntarily eliminate commas from their multi-line arrays, or may choose to leave them as they currently are. Choosing to remove them should consist of a simple find and replace.

## Alternatives considered

Some may support eliminating the necessity of commas for arrays and dictionaries, but not for parenthetical expressions. I believe if it makes sense for one kind of comma separated list, it makes sense for all. The reasoning is the same: the comma serves no purpose. Still, it would be possible to adopt the elimination of the comma for any particular subset of the comma separated list cases.

There's currently an open proposal to add trailing commas to parenthetical expressions. This would solve the trailing comma problem for tuples and function calls. If this proposal is adopted, it may make sense to eliminate all trailing commas from the language, as the only actual use case (multi-line, frequently modified comma separated lists) will be taken care of by this new syntax.

For other ways of consistently formatting comma separated lists (without changing syntax), you could hypothetically do what some (especially Haskell-ish) languages do:

```swift
let friendlyList =
    ["Hi"
    ,"how"
    ,"are"
    ,"you"
    ,"?"
    ]
```

But this is very ugly.
