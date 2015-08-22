---
title: Cloud Amazon Book Library

layout: page
category: wiki
---

This page describes the setup of an S3 based file storage with corresponding management functions on a website, connected via Lambda functions and API calls.

## Server file setup
    {your-bucket-name}       
     |--authority_check
     |  |--manager.txt
     |  |--user.txt
     |--books        
     |--uploads       

## Client file setup
    {web-host-folder}       
     |--index.html
     |--book_list.html
     |--global.css
     |--global.js
     |--alphanum.js

## Server APIs

## Client Usage Flow

## Setup

1. Create the server-side file hierarchy (specified above), by creating a new bucket and setting up the folders within. Additionally, [enable CORS](https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html#how-do-i-enable-cors), so the files inside the bucket can be accessed from your test-domaint (most likely "localhost") and from the "real" domain.

## Sources
- [Alphanumeric Sorting](http://www.davekoelle.com/alphanum.html)
- [AWS S3 Enable CORS for a bucket](https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html#how-do-i-enable-cors)
