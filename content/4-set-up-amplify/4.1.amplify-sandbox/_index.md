---
title : "Run Amplify Sandbox"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 4.1. </b> "
---

In this step you will run Amplify sandbox to test the functionalities.

Run the following in the root folder:

```bash
npx ampx sandbox
```

If you are using a profile other than the default profile, remember to add `--profile <your-profile>`.


{{% notice tip %}}
If this is the first time you run Amplify in the region (e.g. `us-east-1`), you will need to "bootstrap" the region. Amplify will notify you about this, just follow the instruction: login with the root account, and wait until it is done.
{{% /notice %}}

#### Boostraping a region

You will see a notification if this is the first time you run `npx ampx sandbox` in the region.
![Amplify notify]()



Follow the link, click on **Initialize setup now**
![Boostrap1](/images/4.amplify/bootstrap.png)

Wait until the boostraping process is completed
![Boostraping -> done]()

After the boostraping step is done, go back to the terminal and run `npx ampx sandbox` (if it is not running) to see the Amplify Sandbox is running
![Amplify Sandbox]()

{{% notice tip %}}
After amplify have successfully deployed, you will see a newly created file named `amplify_outputs.json` in the root of the project.
{{% /notice %}}