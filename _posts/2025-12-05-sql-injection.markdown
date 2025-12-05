---
layout: post
title:  "Portswigger 1: SQL injection"
date:   2025-12-05 20:28:24 +0800
categories: 
---

## In the beningging

After spending a day learning about air conditioning parts, I had some time to spent. Oh wait, I have yet to update it here. My partner, Jo and I will be starting a air cond parts supply job next year in 2026, hence some of my time during the semester break will be spent on learning more about the business. At the same time, I have gained interest in the Web Security domain. 

In the past few months, I had did my own research and most sources (mainly reddits) suggested to start from either one of the platforms TryHackMe, HackTheBox and PortSwigger. Before writing these posts, I had completed most of the foundational courses on TryHackMe and attempted some of the labs on HackTheBox. However, I find the intuition from TryHackMe was not sufficient in completing the HTB labs. So this would be the first posts I had on learning the multiple types of
vulnerabilities of Web Security.

## What is SQL and what is SQL Injection?
SQL is Structure Query Language, its a specific programming language that allow us to build, manage and retrieve data from a relational database management system (DBMS). And why do we need it? Most of the frontend web pages or applications that we see, unless it is staticly hosted where its just displaying a fixed set of words/images. They usually requires a data transfer stream to the backend. Be it for use authentication, or data retrieval. Thats where a database comes in,
we need it for persistent data storage, where we can have different states saved for the data we require.

SQL Injection takes advantage of poorly designed SQL. In writing codes, sometimes we are not aware of the vulnerabilites we are exposing the applications to. There are different usage of SQL Injection and different attack methods, some are listed as below:

1. Retrieving hidden data, where you can modify a SQL query to return additional results.
2. Subverting application logic, where you can change a query to interfere with the application's logic.
3. UNION attacks, where you can retrieve data from different database tables.
4. Blind SQL injection, where the results of a query you control are not returned in the application's responses.

### Retrieving hidden data

Using this [lab][https://insecure-website.com/products?category=Gifts] 
