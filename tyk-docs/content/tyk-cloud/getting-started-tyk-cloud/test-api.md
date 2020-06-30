---
date: 2020-03-17T19:13:22Z
Title: Task 6 - Test your API
menu:
  main:
    parent: "Getting Started with Tyk-Cloud"
weight: 6
aliases:
    - /tyk-cloud/test-api/
---

You'll now access the API you setup in [Task 5](/docs/tyk-cloud/getting-started-tyk-cloud/first-api/) from the Edge Gateway within Tyk-Cloud.


## Step One - Access the Gateway Ingress

From an Edge Gateway, copy the Ingress link and open it in a browser tab. You will get a 404 error.

## Step Two - Append the URL with your API

You created a API named **my app** in [Task 5](/docs/tyk-cloud/getting-started-tyk-cloud/first-api/). Add `/my-app/` to the end of the URL. You should be taken to https://tyk.io, which you added as the **Target URL** for the API in [Task 5](/docs/tyk-cloud/getting-started-tyk-cloud/first-api/#step-three---core-settings).


Next you'll create another Tyk Gateway Edge Stack from your Linked Stacks.