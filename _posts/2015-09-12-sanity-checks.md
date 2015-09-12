---
layout: post
title: "Sanity Checks"
date: 2015-09-12
comments: true
---

Last week I was troubleshooting a bug in the staging environment (which functions more like a testing environment) of one of our apps.  The application has a service-oriented architecture, and the bug concerned three component applications:

- an ETL (extract, transform, load) app that pulls data from third-party APIs, processes that data, and loads the results into the next app listed below
- a data storage app that stores the ETL results and provides a JSON API to view those results
- a dashboard app that controls how the ETL behaves through various settings

The bug was that the data being served by the data storage app was inconsistent with one of the dashboard app's settings.  The weird thing was that the setting in question was tested and approved a couple of weeks before, in the same environment.  In hindsight this was a very important clue.

First thing I did was to verify the bug existed.  Sometimes bugs get reported to that don't really exist.  This time though, it did.

Next I went over the code and tests in each of the three codebases again, and confirmed with my teammates that I understood it correctly. We all agreed that, given the code and tests, the bug should not have existed.

At this point I remembered another bug where setting changes in the dashboard app weren't propagated to the ETL app.  Whenever settings are changed in the dashboard app, the other apps are 'pinged' about it.  These other apps then pull the new settings in.  Sometimes however, the new settings never make it to the non-dashboard apps.  I checked the ETL app via console (settings aren't available through the API), hoping to finally find the cause of the bug.  That wasn't it either.

I was stumped.

And then it hit me.  I'd missed doing one of the quickest and most simple checks: checking that the code was actually there.  It wasn't.  Every month we create new version control staging branches off of the maste branch of each component app.  The code in question was created, tested, and approved in August, but was neglected to be merged in when the September staging branches were created.

Slightly embarassing, but this episode did highlight things to improve:

- I've always though of myself as methodical, but I probably need to make my debugging more scientific.
- our monthly change of staging branches is a manual process.  Automate where possible.

I hope this helps someone save time someday.
