---
title : "Create a CloudFront Distribution"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2. </b> "
---

Now that the S3 bucket for hosting the UI is ready, it's time to create a **CloudFront distribution** that will serve your static web app to users — fast, globally, and securely.

In this step, we'll set up **a single CloudFront distribution** with a default behavior pointing to the UI bucket you just created. This distribution will later also be used to serve audio files (from another S3 bucket that will be created by Amplify in upcoming steps).

{{% notice info %}}
At this point, the CloudFront distribution will **only serve the UI bucket**. Once Amplify is deployed, we'll come back and **add a new origin and behavior** for streaming audio from the second bucket.
{{% /notice %}}

---

#### Create the distribution

You can create the CloudFront distribution manually via the AWS Console, or use the AWS CLI (JSON config required). For most users, the Console is easier at this stage:

1. Go to the **CloudFront Console**
2. Click **Create Distribution**
3. Under **Origin domain**, select the S3 UI bucket (`your-ui-bucket-name.s3.amazonaws.com`)
4. Leave default behavior settings (GET, HEAD allowed, caching enabled)
5. In **Default root object**, enter: `index.html`
6. (Optional) Enable **Compress objects automatically**
7. Click **Create distribution**

Once the distribution is created, note the **CloudFront domain name** — you'll use this to access your UI later.

---

{{% notice tip %}}
You won't upload the real UI yet. In the next step, we'll test the setup by uploading a simple `index.html` file to the UI bucket and verifying that CloudFront serves it correctly.
{{% /notice %}}

---


