---
title: Gmail Forwarding

layout: page
category: wiki
---

## Issue

When forwarding Emails from Gmail to another account, not all emails are forwarded! Since forwarding happens _after_ the Gmail SPAM Filter, certain mails might be "stuck" in SPAM (and will be deleted after a certain amount of time).

## Solution

Create two special filters in Gmail:

- "is:spam" -> Never send it to SPAM
- "in:spam" -> Never send it to SPAM
