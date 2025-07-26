---
title : "Upload a Test File to the UI Bucket"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3.3. </b> "
---

Before wiring up the full frontend, we’ll do a quick sanity check: upload a simple `index.html` file to the S3 UI bucket and make sure it's accessible via CloudFront.

This ensures that:

- Your S3 bucket is correctly configured for static website hosting
- CloudFront is correctly pointing to the UI bucket
- Public access and caching are working as expected

---

#### Create a test file

In your local project folder (or anywhere), create a basic `index.html`:

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Serverless Music App</title>
  </head>
  <body>
    <h1>Hello from CloudFront</h1>
  </body>
</html>
```

---

#### Option 1: Upload via AWS CLI

```bash
aws s3 cp index.html s3://your-ui-bucket-name/index.html --acl public-read
```

Replace `your-ui-bucket-name` with your actual bucket name.

The `--acl public-read` flag is only needed if your bucket policy does **not** already allow public read access.

---

#### Option 2: Upload via AWS Console

1. Go to the [S3 Console](https://s3.console.aws.amazon.com/s3)
2. Click on your **UI bucket name**
3. Click **Upload**
4. Drag and drop your `index.html` file (or use **Add files**)
5. Click **Next** until the **Permissions** step
6. If prompted, **grant public read access** (or skip if bucket policy already handles it)
7. Click **Upload**

After upload completes, the file should appear in your bucket as `index.html`.

---

#### Test in browser

Now open your **CloudFront distribution domain**, which looks like:

```
https://dxxxxx.cloudfront.net/
```

You should see your "Hello from CloudFront" message rendered.

{{% notice info %}}
Once this test passes, you're ready to move on to deploying the real UI and configuring the audio file origin after Amplify is initialized.
{{% /notice %}}

---