---
layout: post
title:  "Quality Control - LinkedIn's Testing Methodology"
date:   2013-06-26 14:09:36
categories: Post
contributer: Sony Mohaptra
position: Senior Test Engineer
description: At LinkedIn, we believe in designing products that offer great value to our members, shipping those products quickly, and doing it all without compromising on quality.
image: sony%20mohapatra_0.png
link: linkedin.com/in/sonymohapatra
tags: 
 - test 
 - code 
 - something
---

###The Team

A typical test team at LinkedIn includes product managers, development engineers, software engineers in test, and quality engineers. We have a dedicated team of engineers that rotate weekly and whose only job that week is to fix and close bugs.

###Test Strategy and Process

A testing lifecycle can run multiple weeks long and it begins by spending the first week preparing for the official kick-off. Quality engineers do a product spec review and create a test plan along with an estimate of the release date. The next few weeks are spent in writing detailed test cases in an internal tool called Test Manager. Training sessions are held in parallel to educate product development engineers on the testing lifecycle. Once test cases and training is complete, the testing begins.

Tasks are assigned daily. A task dashboard tracks progress and monitors newly filed tickets, while daily stand-ups are used to discuss issues. This aggressive status-tracking approach helps us stay current and continuously plan towards the release. 

###Integration Testing

Integration testing and functional testing run in parallel. SETs (Software Engineers in Test) perform tests on APIs for positive, negative, and boundary conditions. Integration tests are written in TestNG - a Java testing framework. These tests run every night and on every code check-in. (Details on Integration testing will follow in subsequent posts.)

###Performance Testing

Performance testing is an important part of our release cycle. SETs start with backend load testing - we run load tests and get performance metrics, such as peak QPS values and response times. We then use Apache JMeter and other internal tools to ensure that our system can handle production load per business criteria. At this point, engineers start frontend performance testing and produce metrics such as page load, JavaScript execution time, page size, and page component load time. Bugs are filed and fixed, and we re-run tests until we reach our performance goals.

###Functional Testing

The test team, mostly quality engineers and a few development engineers, are engaged in executing test cases manually as part of functional testing. Features are distributed among testers, taking particular care not to have the same engineer develop and test a feature. Bugs are filed and aggressively addressed to ensure a shorter bug lifecycle.

At LinkedIn we developed LiX, an online experimentation platform and manages the lifecycle of all tests and experiments. Every product feature is behind a LiX test and functional testing is performed with LiX turned on and off. The test team is involved in testing the tracking of pagekeys, tracking codes on Kafka consumer, device testing on iPad and iPhone, and finding gaps in metrics testing.

###Cross-Browser Testing

After functional testing is complete, the team transitions to browser testing, which takes place across different versions of IE, Firefox, Safari, and Chrome. It's important to find and fix these browser issues early and collaboration with the webdev team is particularly important here.

###Automation

Automation begins next with each tester writing scripts for their assigned feature, using Ruby and Selenium as the automation tools. Scripts are i18n (internationalization) compatible and are passed on to the i18n teams to begin their test cycle. The test cases are marked as automated and tracked on Test Manager.

The product manager and offshore resources are involved in bug verification. We monitor bug activity using the dashboard, tracking the number of defects captured, and fixed daily. This helps us review any reopened bugs and ensure there are no blockers close to the release date.

After all the automation scripts are ready, regression tests are run everyday on all supported browsers through Test Manager against new builds, to capture bugs as they appear. A bug bash is organized to get feedback from different teams, and bug triggered automation is done as part of bug verification.

###L10N Testing

After automation is complete, test scripts are passed on to the L10N (localization) team to execute in different languages. The team adds code to take screenshots in each language, which are then used by translators to make sure the UI elements make sense contextually. Any bugs filed are immediately addressed by the bug fixers. Only after the L10N team signs off on the UI, we roll out the product in various languages.

###Ship

When code is pushed to production, the test team runs production sanity checks to ensure that functionality is not broken. LiX is slowly ramped to 100%, and we constantly monitor and logs in production for any anomalies.

At LinkedIn, we set and strive towards the highest quality in our products. The test team, working with our development engineers, act as gatekeepers to guarantee quality releases.
