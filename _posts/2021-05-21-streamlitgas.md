---
toc: false
layout: post
description: Using some free resources to pull and show data
categories: [data science]
title: Streamlit dashboarding for gas prices
---
# Streamlit dashboards

- [Github](https://github.com/ahy3nz/colonial)

- [Streamlit dashboard link](https://share.streamlit.io/ahy3nz/colonial/colonial/app.py)

The recent events with the colonial pipeline drew a lot of attention to gas stations and gas prices everywhere, and in the back of my head I had been wanting to try out [streamlit's free hosting capabilities](https://docs.streamlit.io/en/stable/deploy_streamlit_app.html) and [the plotting library altair](https://altair-viz.github.io/index.html).

Streamlit is a data dashboard tool. 
Altair is a data visualization tool.
I found a twitter post about gasbuddy hosting a query-able API, like [this endpoint](https://www.gasbuddy.com/assets-v2/api/trends?search=VA).
This was already enough to build out a dashboard to query gasbuddy for gas prices and share them in an (interactive) altair plot on streamlit.
In this implemention, I'm using python's threading library to launch separate threads to query gas buddy.

To beef things up more, I'm using a personal S3 bucket to store and recall any pulled data. 
Streamlit also provides a nice secrets utility to safely store sensitive information like [AWS credentials](https://github.com/ahy3nz/colonial/blob/master/colonial/data.py#L17-L23) or the [IDs of gas stations near me](https://github.com/ahy3nz/colonial/blob/master/colonial/scraper.py#L27-L29).

I think there's still room for improvement, like the gas price querying on streamlit only occurs when someone visits the URL and launches the dashboard (which then launches parallel threads to query gas buddy).
If no one is active on the streamlit app, then there will be no scraping threads.
It might be better to use some sort of local server/bot to periodically query gasbuddy and not rely on streamlit to do the scraping.

In summary, I recommend people give streamlit a shot at deploying a (free) application!
