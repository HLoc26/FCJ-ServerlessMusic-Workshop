---
title : "Set up Storage with S3"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4.4. </b> "
---

In this section, you'll learn how to configure Amplify to support uploading music files to Amazon S3.

We'll first set up a storage resource using the Amplify. This creates a dedicated S3 bucket that allows authenticated users to upload and store audio files securely. You will also verify the setup using the AWS Console.

Later in this section, you'll test the existing `/tracks/upload` endpoint to generate a signed URL, upload a file via that URL, and finally store track metadata to DynamoDB using the `/tracks` endpoint.

{{<figure src="/images/4.amplify/4.4.s3-storage/what-you-will-do.png" alt="What you will create: S3 file bucket" width="80%">}}

---

### Step 1. Create a Music Storage Bucket
To store user-uploaded music files, we will use Amazon S3, provisioned through Amplify.


#### Step 1.1. Create a Storage Resource
Go to the `amplfy` directory and create a new storage resource `storage/resource.ts`

{{<figure src="" alt="Create Storage Resource" width="100%">}}

Now in `resource.ts`, you can use Amplify's Storage module to create a new S3 bucket. This bucket will be used to store the music files uploaded by users.

```typescript
import { defineStorage } from "@aws-amplify/backend";

export const fileBucket = defineStorage({
    name: "<your-bucket-name>",
})
```

{{% notice note %}}
Replace `<your-bucket-name>` with a unique name for your S3 bucket. This name must be globally unique across all AWS accounts.
{{% /notice %}}

#### Step 1.2. Deploy the Storage Resource
Now go to `backend.ts` and add the storage resource to the Amplify backend configuration below the existing `auth` resources (don't forget to import the storage resource first).

{{<details summary="Your backend.ts should look like this">}}
```typescript
import { defineBackend } from '@aws-amplify/backend';
import { auth } from './auth/resource';
import { fileBucket } from './storage/resource';

const backend = defineBackend({
	auth,
	fileBucket,
});
```
{{</details>}}

Now, deploy the backend resources by running the following command in your terminal at the root of the project:

```bash
npx ampx sandbox
```

{{% notice tip %}}
If you are using a profile other than the default profile, remember to add `--profile <your-profile>`.
{{% /notice %}}

#### Step 1.3. Verify the S3 Bucket
After the deployment is complete, you can verify that the S3 bucket has been created successfully.
1. Go to the [AWS Management Console](https://console.aws.amazon.com/).
    {{<figure src="" alt="AWS Management Console" width="100%">}}
2. Navigate to the S3 service.
    {{<figure src="" alt="S3 Bucket" width="100%">}}
3. You should see the bucket you created listed there (with the prefix `amplify-`). Click on it to view its details.
    {{<figure src="" alt="Bucket created" width="100%">}}
4. Save the bucket name, as you will need it later to configure the CloudFront distribution.

### Step 2. Configure CloudFront Distribution to serve music files
To serve the music files efficiently, we will set up the earlier CloudFront distribution that points to the S3 bucket. This will allow for faster content delivery and caching.

#### Step 2.1. Navigate to the existing CloudFront Distribution
1. Go to the [AWS Management Console](https://console.aws.amazon.com/).
    {{<figure src="" alt="AWS Magagement Console" width="100%">}}
2. Navigate to the CloudFront service.
    {{<figure src="" alt="CloudFront" width="100%">}}
3. Find the CloudFront distribution you created earlier for serving web UI.
    {{<figure src="" alt="CloudFront Distribution" width="100%">}}
4. Click on the distribution ID to view its details.
    {{<figure src="" alt="Distribution details" width="100%">}}
#### Step 2.2. Add a new origin for the S3 bucket
1. In the "Origins" tab, you should see a origin pointing to your S3 web UI bucket.
    {{<figure src="" alt="Origins" width="100%">}}
2. Click **Create origin** to add a new origin for the S3 bucket.
    {{<figure src="" alt="Create Origin" width="100%">}}
3. Add a new origin for the S3 bucket you created in Step 1.1.
   - Set the origin domain to the S3 bucket URL (e.g., `your-bucket-name.s3.amazonaws.com`).
   - Ensure that the origin path is set to `/` to serve files from the root of the bucket.
   - Remember the origin name, as you will need it when creating a new behavior for the music files.
        {{<figure src="" alt="Origin domain" width="100%">}}
   - In **Origin access**, select **Origin access control settings (recommended)**, and an **Origin access control** field will appear.
        {{<figure src="" alt="Origin access" width="100%">}}
   - Select the existing origin access control (OAC) that has been automatically created for the file bucket (the OAC name should be similar to the Origin domain above).
   - Choose **Copy Policy** and save it in a text editor for later use. The policy you copied should look similar to this (with proper values replaced):
        ```json
        {
            "Version": "2008-10-17",
            "Id": "PolicyForCloudFrontPrivateContent",
            "Statement": [
                {
                    "Sid": "AllowCloudFrontServicePrincipal",
                    "Effect": "Allow",
                    "Principal": {
                        "Service": "cloudfront.amazonaws.com"
                    },
                    "Action": "s3:GetObject",
                    "Resource": "arn:aws:s3:::<your-bucket-name>/*",
                    "Condition": {
                        "StringEquals": {
                        "AWS:SourceArn": "<your-distrobution-arn>"
                        }
                    }
                }
            ]
        }
        ```

   - Choose *Go to S3 bucket permissions* to open the S3 bucket permissions page.
        {{<figure src="" alt="S3 permissions" width="100%">}}
4. In the S3 bucket permissions page, in the **Bucket policy** section, you will need to set the bucket policy to allow CloudFront to access the S3 bucket contents.
   - Select **Edit**.
   - Use the above saved OAC policy to set the bucket policy. You only need the statement with the `AllowCloudFrontServicePrincipal` Sid. 
   - Or you can use the following policy as a template:
        ```json
        {
            "Sid": "AllowCloudFrontServicePrincipal",
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudfront.amazonaws.com"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::<your-bucket-name>/*",
            "Condition": {
                "StringEquals": {
                "AWS:SourceArn": "<your-distrobution-arn>"
                }
            }
        }
        ```
   - Paste the policy into the bucket policy editor, replacing `<your-bucket-name>` with your actual S3 bucket name and `<your-distrobution-arn>` with your CloudFront distribution ARN.
        {{<figure src="" alt="S3 Bucket Policy" width="100%">}}
   - Select **Save changes** to apply the policy.

5.  Back to the Distribution Origin edit tab, save the changes to the origin settings.
    {{<figure src="" alt="Create origin success" width="100%">}}
#### Step 2.3. Create a new behaviour for the S3 origin
1. In the "Behaviorus" tab, create a new behaviour for the new S3 origin.
    {{<figure src="" alt="Distribution create" width="100%">}}
2. Set the **Path pattern** to `/tracks/*` to match the music files you will upload.
3. Set the origin to the new S3 bucket origin you just created.
4. In **Compress objects automatically**, select **No** since audio files are already compressed.
    {{<figure src="" alt="Basic info" width="100%">}}
5. Leave the other settings as default.
6. Click **Create** to save the new behaviour.
    {{<figure src="" alt="Create new behavior" width="100%">}}
