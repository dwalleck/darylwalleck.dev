+++
author = "Daryl Walleck"
title = "Becoming a Flaky Test Whisperer"
draft = true
date = "2020-11-19"
description = ""
tags = [
    "Testing"
]
+++

In the past, you may have encountered a test that became your nemesis. After hours of hard work on a new feature, you submit your pull request...and it's blocked by a failing test. One that had just passed when you ran your tests before committing your changes. You feel more troubled when you realize that the failing test covers a feature that you didn't change. You glance through your changes to make sure your changes couldn't have impacted the failing test, but you don't see an obvious culprit. Out of good ideas, you re-run the checks for your changes...and everything passes! The first time this happens, you are likely relieved that your change merged so that you can start on your next task. However, by the 50th time it's happened, you have the code for the test in question highlighted with your finger hovering above the delete key. You've had enough. It's wasted enough of your time and is obviously defective. It just needs to go away.

However, before hitting delete, you take a deep breath and realize that _something_ must be wrong. Something is out of alignment between what you and this test are seeing. Shouldn't you at least hear it out before sending it off to the void? Sorting out the root cause of flaky tests can be a time-consuming and sometimes frustrating effort, 

## So what is a flaky test?

## Can't I just skip it and be on my way?

Ideally, no. A failing test is trying to communicate something, whether it's an issue with the application or the test code. At the best you're creating technical debt that will have to be addressed at some point. At the worst, you could be ignoring a difficult to replicate but very real defect in your application.

Almost just as bad, you're starting down a road of erroding trust in your test results. If one or two test failures becomes the new normal for the team, phrases like "acceptable test failures" and the like may become normal in your team's vocabulary. If a team takes the time to write good tests, then they should also take the time to understand what their failures indicate. Failures may seem random, but there's almost always a common theme behind their cause.

## How flaky tests happen

## Practices to avoid flaky tests

## Tests aren't bad, they're just coded that way

## Extra stuff

Tests should never lie. As a developer, automated tests should be your dependable partner that let you know when a change you made didn't work out. They're your scrappy and loyal sidekick in a police drama. But what if your tests *did* lie to you? You might start to

Most of the time, automated tests are useful. They buzz and spin, silently judging our code while we grab our next caffeniated beverage. More importantly, they provide feedback to developers to let them know something has gone wrong. Most of the time.
