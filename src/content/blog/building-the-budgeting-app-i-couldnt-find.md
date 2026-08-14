---
title: "Building the budgeting app I couldn't find"
description: "Why I started building MicroBudget, a savings-first budgeting app for people whose brains don't work like a spreadsheet."
pubDate: 'Aug 11 2026'
tags: [python, app-development, personal-finance]
---

I started MicroBudget for two reasons that eventually became one.

The first: I wanted to learn how to actually build an app. Not follow a tutorial and copy the answers — build something real, from scratch, that I'd actually use. The second: I was sick of the budgeting apps on offer. There are a few Australian ones, but they were either overcomplicated or missing the things I wanted. Nothing quite fit.

Then I heard about Caleb Hammer's Dollarwise app, and something clicked — I could build my own, inspired by his approach but with my own flair. The unlock came when I realised Up Banking supports Personal Access Tokens. That meant I could pull my own transaction data directly and build something around it, instead of manually entering everything like a caveman.

There was a third thing too, and it's really the heart of it. Even with a budget, I didn't actually know how to get ahead on my savings. So I did some digging and landed on the snowball savings strategy — and I wanted to build that in, not just track where my money went, but actually help me claw forward.

## Who it's for

MicroBudget is built to help me save — but really, it's built for anyone who struggles with it, especially people with ADHD. I want this to work the way my brain works, not the way a spreadsheet wishes my brain worked. Long-term, I'd like it to be something most people in Australia could use, because I genuinely can't find anything on the market that does what I'm trying to do here.

## Where it's at

Right now there's a basic but functioning tracker. It reads my transactions and savings from Up Banking and automatically sorts them into three categories — Needs, Wants, and Debts. That sorting is automated, which was the whole point: the less manual effort, the more likely I'll actually stick with it.

Under the hood, Python does the heavy lifting — all the calculations, the data storage, and talking to the Up Banking API. The front end doesn't exist yet, but the plan is a proper interactive site built with HTML, JS and CSS, and eventually a mobile app.

## The honest part

This is the first time I've really gone deep with Python, and the whole thing has been a massive learning curve. But I learn best by throwing myself in the deep end — give me a real problem I care about and I'll figure out the parts I need as I go. That's how MicroBudget has been built so far, and it's how the rest of it will get built too.

More to come as I extend it. If you've got ADHD and a savings goal you can't seem to reach, this one's for you.
