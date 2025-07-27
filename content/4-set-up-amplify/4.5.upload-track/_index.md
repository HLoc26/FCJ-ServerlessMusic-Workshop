---
title : "Upload Music Tracks to S3"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

In this part, we will manually upload a music file to the S3 bucket and verify that it can be streamed via the CloudFront CDN. This ensures that music files are publicly accessible (if permitted) and served efficiently from edge locations.

### Step 1. Upload a test `.mp3` file to S3

- Go to the AWS Console
{{<figure src="/images/4.amplify/4.5.upload-track/upload-test-file.png" alt="Upload test file to S3" width="80%">}}
- Open **S3**, and navigate to your music file bucket (created via Amplify earlier)
{{<figure src="/images/4.amplify/4.5.upload-track/upload-test-file.png" alt="Upload test file to S3" width="80%">}}
- Choose the `tracks/` folder (or create it if missing)
{{<figure src="/images/4.amplify/4.5.upload-track/upload-test-file.png" alt="Upload test file to S3" width="80%">}}
- Click **Upload**, and select a `.mp3` file from your local machine

#### 2. Get the CloudFront URL

- Locate the CloudFront distribution created for this bucket
- Copy the **Domain name**, e.g. `d123456abcdef8.cloudfront.net`
- Construct the file URL as:

  ```
  https://<CloudFrontDomain>/public/<filename>.mp3
  ```

  Example:

  ```
  https://d123456abcdef8.cloudfront.net/public/test-track.mp3
  ```

#### 3. Test the audio playback

You can quickly test the streaming by pasting the URL in your browser. Alternatively, create a simple HTML page like:

```
<audio controls>
  <source src="https://d123456abcdef8.cloudfront.net/public/test-track.mp3" type="audio/mpeg">
  Your browser does not support the audio element.
</audio>
```

Save this as `test.html` and open in your browser.

### Summary

At this point, your CDN is working correctly and serving public music files. In later steps, you will automate the file upload using signed URLs and secure this bucket with stricter permissions.

