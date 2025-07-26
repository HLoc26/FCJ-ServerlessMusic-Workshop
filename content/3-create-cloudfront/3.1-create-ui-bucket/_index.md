---
title : "Create a S3 bucket to serve UI"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1. </b> "
---

To serve the frontend (React build) over CloudFront, we first need an S3 bucket configured for static website hosting.

1. Open AWS Console, search for Amazon S3
2. Click ***Create Bucket***
3. In **General configuration**:
   1. Choose **General purpose** for Bucket type
   2. Specify a bucket name that is globally unique, e.g. `serverless-ui-bucket`
4. Scroll down, in **Block Public Access settings for this bucket**, make sure the **Block *all* public access** checkbox is checked
5. Scroll down, leave everything as is, and click **Create Bucket**





