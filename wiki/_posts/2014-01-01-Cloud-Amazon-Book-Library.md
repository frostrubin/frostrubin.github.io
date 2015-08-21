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

## Server APIs

## Client Usage Flow
