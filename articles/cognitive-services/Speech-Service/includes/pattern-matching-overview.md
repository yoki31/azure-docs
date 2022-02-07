---
title: Pattern Matching overview
titleSuffix: Azure Cognitive Services
description: Pattern Matching with the ``IntentRecognizer`` helps you get started quickly with offline intent matching.
author: chschrae
manager: travisw
ms.topic: include
ms.date: 11/15/2021
ms.author: chschrae
keywords: intent recognition pattern matching
---

Pattern matching can be customized to group together pattern intents and entities inside a ``PatternMatchingModel``. Using this grouping, it's possible to access more advanced entity types that will help make your intent recognition more precise.

### Patterns vs. Exact Phrases

There are two types of strings used in the pattern matcher: "exact phrases" and "patterns". It's important to understand the differences.

Exact phrases are a string of the exact words that you'll want to match. For example:

> "Take me to floor seven".

A pattern is a phrase that contains a marked entity. Entities are marked with "{}" to define the place inside the pattern and the text inside the "{}" references the entity ID. Given the previous example perhaps you would want to extract the floor name in an entity named "floorName". You would do so with a pattern like this:

> "Take me to floor {floorName}"

### Outline of a PatternMatchingModel

The ``PatternMatchingModel`` contains an ID to reference that model by, a list of ``PatternMatchingIntent`` objects, and a list of ``PatternMatchingEntity`` objects.

#### Pattern Matching Intents

``PatternMatchingIntent`` objects represent a collection of phrases that will be used to evaluate speech or text in the ``IntentRecognizer``. If the phrases are matched, the ``IntentRecognitionResult`` returned will have the ID of the ``PatternMatchingIntent`` that was matched.

#### Pattern Matching Entities

``PatternMatchingEntity`` objects represent an individual entity reference and its corresponding properties that tell the ``IntentRecognizer`` how to treat it. All ``PatternMatchingEntity`` objects must have an ID that is present in a phrase or else it will never be matched.

##### Entity Naming restrictions

Entity names containing ':' characters will assign a role to an entity. (See below)

### Types of Entities

#### Any Entity

The "Any" entity will match any text that appears in that slot regardless of the text it contains. If we consider our previous example using the pattern "Take me to floor {floorName}", the user might say something like:

> "Take me to the floor parking 2

In this case, the "floorName" entity would match "parking 2".

These are lazy matches that will attempt to match as few words as possible unless it appears at the beginning or end of an utterance. Consider the pattern:

> "Take me to the floor {floorName1} {floorName2}"

In this case, the utterance "Take me to the floor parking 2" would match and return floorName1 = "parking" and floorName2 = "2".

It may be tricky to handle extra text if it's captured. Perhaps the user kept talking and the utterance captured more than their command. "Take me to floor parking 2 yes Janice I heard about that let's". In this case the floorName1 would be correct, but floorName2 would = "2 yes Janice I heard about that let's". It's important to be aware of the way the Entities will match and adjust to your scenario appropriately. The Any entity type is the most basic and least precise.

#### List Entity

The "List" entity is made up of a list of phrases that will guide the engine on how to match it. The "List" entity has two modes. "Strict" and "Fuzzy".

Let's assume we have a list of floors for our elevator. Since we're dealing with speech, we will add entries for the lexical format as well.

> "1", "2", "3", "lobby", "ground floor", "one", "two", "three"

When an entity with an ID is of type "List" and is in "Strict" mode, the engine will only match if the text in the slot appears in the list.

> "take me to floor one" will match.

> "take me to floor 5" will not.

It's important to note that the Intent will not match, not just the entity.

When an entity is of type "List" and is in "Fuzzy" mode, the engine will still match the Intent, and will return the text that appeared in the slot in the utterance even if it's not in the list. This is useful behind the scenes to help make the speech recognition better.

> [!WARNING]
> Fuzzy list entities are not currently implemented.

#### Prebuilt Integer Entity

The "PrebuiltInteger" entity is used when you expect to get an integer in that slot. It won't match the intent if an integer cannot be found. The return value is a string representation of the number.

#### Examples of a valid match and return values

> "Two thousand one hundred and fifty-five" -> "2155"

> "first" -> "1"

> "a" -> "1"

> "four oh seven one" -> "4071"

If there's text that is not recognizable as a number, the entity and intent will not match.

#### Examples of an invalid match

> "the third"

> "first floor I think"

> "second plus three"

> "thirty-three and anyways"

Consider our elevator example.

> "Take me to floor {floorName}"

If "floorName" is a prebuilt integer entity, the expectation is that whatever text is inside the slot will represent an integer. Here a floor number would match well, but a floor with a name such as "lobby" would not.

### Optional items and grouping

In the pattern it's allowed to include words or entities that may be present in the utterance or not. This is especially useful for determiners like  "the", "a", or "an". This doesn't have any functional difference from hard coding out the many combinations, but can help reduce the number of patterns needed. Indicate optional items with "[" and "]". You may include multiple items in the same group by separating them with a '|' character.

To see how this would reduce the number of patterns needed consider the following set.

> "Take me to the {floorName}"

> "Take me the {floorName}"

> "Take me to {floorName}"

> "take me {floorName}"

> "Take me to the {floorName}" please

> "Take me the {floorName}" please

> "Take me to {floorName}" please

> "take me {floorName}" please

These can all be reduced to a single pattern with optional items.

>"Take me [to | the] {floorName} [please]"

It's also possible to include optional entities. Imagine there are multiple parking levels and you want to match the word before the {floorName}. You could do so with a pattern like this:

>"Take me to [{floorType}] {floorName}"

> [!NOTE]
> While it's helpful to use optional items, it increases the chances of pattern collisions. This is where two patterns can match the same-spoken phrase. If this occurs, it can sometimes be solved by separating out the optional items into separate patterns.

### Entity roles

Inside the pattern, there may be a scenario where you want to use the same entity multiple times. Consider the scenario of booking a flight from one city to another. In this case the list of cities is the same, but it's necessary to know which city is the user coming from and which city is the destination. To accomplish this, you can use a role assigned to an entity using a ':'.

> "Book a flight from {city:from} to {city:destination}"

Given a pattern like this, there will be two entities in the result labeled "city:from" and "city:destination" but they'll both be referencing the "city" entity for matching purposes.

### Intent Matching Priority

Sometimes multiple patterns may match the same utterance. In this case, the engine will give priority to patterns as follows.

1. Exact Phrases.
2. Patterns with more Entities.
3. Patterns with Integer Entities.
4. Patterns with List Entities.
5. Patterns with Any Entities.
