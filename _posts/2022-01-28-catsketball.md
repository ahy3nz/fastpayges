---
toc: false
layout: post
description: A fantasy basketball dashboard
categories: [data science, personal]
title: Dashboarding to evaluate fantasy basketball matchups
---

# The gist

A streamlit dashboard that compares your fantasy basketball category league matchups, helping you figure out which categories you're winning and which categories you might need help with

- [Github](https://github.com/ahy3nz/catsketball)

- [Streamlit link](https://share.streamlit.io/ahy3nz/catsketball/main/catsketball/app.py)

# Fantasy basketball category leagues

My fantasy basketball league switched from a points league to a [categories league](https://sports.yahoo.com/fantasy-basketball-draft-cheat-sheet-for-category-leagues-211118357.html#:~:text=The%20eight%20default%20categories%20are,cat%20and%209%2Dcat%20leagues) this season.
The entire strategy has changed because you can't solely rely on people who score a lot of points; you're concerned about all the other box stats (FG%, FT%, rebounds, assists, steals, blocks, points, 3-pointers made, turnovers).
So here are a couple of thoughts:

- For most people, no one has a strong sense of who performs well in which categories, besides points

- If you compete against a new player each week, you don't know how your _opponent_ will perform in each category, and you especially don't know which categories are easy victories, possibly winnable, or downright impossible to win

- If you have a couple of streaming spots to pick up a player from the waiver wire, it would be nice to be informed of who is the correct waiver wire pickup. Not only will player news/injuries shake up waiver wire, but you might also want to consider if you should prioritze rebounds, steals, blocks, FG%, or 3PM, as a waiver wire pickup might be crucial to swinging a category your way

# The problem

In the perfect algorithmic approach, we'd have some automated decisionmaking framework to:

1. Understand our opponent's performance for the upcoming week
    - Who is injured?
    - How many games is each player playing?
    - Do the NBA matchups affect performance?
    - What are our opponents' strengths and weaknesses?
2. Understand our performance for the upcoming week.
    - Same as above
3. What categories are we definitely winning?
4. What categories are we definitely losing?
5. What categories will be competitive?
6. Who is the pickup to swing the category in our favor?

Unfortunately, this is a hard problem. 
Okay, it might not be _impossible_, but for a hobbyist this can be a little cumbersome as it requires factoring in a lot of different variables that might not be easily accessible.

To reduce the problem, we can focus on the parts that would be easy to resolve with a program:

- Things that don't require lots of logic

- Things that are basically number farming

# The approximate solution

Basically, what we're looking for is _information_ that could help us make a decision.

Over the course of data work, this is somewhat common: we have a lot of complex business logic that relies on a lot of information. 
The decisionmaking is hard to automate, but the data pulling is a very achievable task.
This work boils down to reporting, dashboarding, analytics, etc.

So this approach is a dashboard that leverages Streamlit and [ESPN API](https://github.com/cwendt94/espn-api) to predict your fantasy team's statline for a given time period (how many rebounds will I score from 2022-01-01 to 2022-01-08? what will my FG% look like?).
Also, you can generally assess your team's composition (and other teams') to figure out which trades you might want to make.

If you need help with blocks, you might want to swap your waiver wire 3 pt specialist for a rim-defending center.
If there's a team weak in steals, and your team is overwhelmingly strong in steals, you could try to trade a steals person to rebalance your steals in favor of another category in which you are weak.

# Technical commentary

ESPN API was fairly straightforward to use. 

- Pulling your fantasy basketball league's details will definitely require credentials, so there's this added requirement of passing certain ESPN fantasy cookies. I hadn't figured out a way to automate this, but that's fine.

- It's reassuring that this doesn't require passing literal usernames and passwords, but instead, some relatively harmless cookies.

- The API includes a team's schedule and different estimates for a player's stats, making it possible to do some primitive projections of box stats. Predicting stats is a rabbit hole that knows no bounds, so I did some basic averaging of last season's stats, this season's stat projections, and this season's current performance.

- There's also injury status, which means you can be very honest about your team's performance in the face of unfortunate player news.

- There's some noncohesion between team identifying information (is it a 3-letter abbreviation or an integer code?)

- Unlike [redfin requests](https://github.com/ahy3nz/yellowtail), I didn't find my IP address blacklisted

The choice of dashboarding was Streamlit

- Easy to deploy to some perpetually-available server. Just provide a github repo

- Don't have to worry about the specifics of the server, just the python code and associated dependencies

- Publicly accessible URLs (so you can do fantasy research from home or at work...)

- Free

- Generally very easy to quickly prototype and see the resultant dashboard

- Cooperates with pandas dataframe styling well

Unfortunately, maintaining state was kind of weird.
What does state refer to? 
Generally, state refers to a sort of context and context-dependent variables that dictate what actions can be performed or how actions should be conducted.
In this case, state refers to:

- What is the fantasy basketball league we're looking at (the cookies you passed)


Additionally, streamlit generally re-runs the dashboard python script everytime an input is changed. 
However, sometimes you don't want to re-run the script upon every input change, only when a set of inputs change simultaneously:

- What time period are you examining?

- Which teams are you examining?

- Do you want to include the questionable/day-to-day players in the projections?

Streamlit has addressed some considerations of [state](https://docs.streamlit.io/library/api-reference/session-state) so you can preserve your fantasy cookies between dashboard refreshes.
Streamlit has also included [forms](https://blog.streamlit.io/introducing-submit-button-and-forms/) that delay re-computation until a button is pressed.
In practice, this manifested in the code as a lot of if statements and nested code.

As far as evaluating any technology goes, the gist is: yeah it works. Sure, it has some flaws/friction points, but we can make it work. And we're not looking for perfection, we just want something we can quickly build upon and iterate.

Has it helped? 
I'm in 2nd place in my fantasy basketball league, and I think some of these insights helped me make pickups to win a couple of extra categories.
At the very least, I'm no longer flying blind into each week of fantasy matchups.