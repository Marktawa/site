---
title: How to run Medusa on Google Cloud Shell
date: '2023-05-02'
tags: ['medusa', 'google cloud shell', 'nextjs' 'ecommerce']
draft: false
summary: In this tutorial, you will learn how to install, run and test Medusa on Google Cloud Shell
---

## Introduction

In this tutorial, you will learn how to install, run and test Medusa on Google Cloud Shell.

If you ever tried to run a Medusa project in Google Cloud Shell you may have run into some issues loading content from the Medusa backend. This is due to CORS. In this tutorial you will use a workaround to successfully load content from the Medusa backend hosted on Google Cloud Shell.

We will use the default quickstart Next.js storefront provided by Medusa as means to test the project. You will walk through the steps required to configure Google Cloud Shell to work with Medusa. By the end of this tutorial, you should have a working Medusa app running in a Google Cloud Shelll instance.

### What is Medusa?

[Medusa](https://docs.medusajs.com/development/overview) is an open source composable commerce engine for building ecommerce websites. 

![Medusa](https://res.cloudinary.com/craigsims808/image/upload/v1682059136/articles/scarlet-mouse/medusa-intro_gc6mba.png)

It was built using Express running on Node.js.

It is fully customizable and extensible throught the use of commerce modules. You can build webshops, subscription services, digital products stores, ticketing systems, and any other ecommerce solutions.

Here is an architecture example of a full fledged ecommere app you can build using Medusa.

![Fully Fledged Ecommerce App](https://res.cloudinary.com/dza7lstvk/image/upload/v1678954316/Medusa%20Docs/Diagrams/fully-fledged-ecom_qqwraq.jpg)

You could think of Medusa as a set of commerce building blocks used to create custom commerce applications. If you want to know more about Medusa, check out their [documentation](https://docs.medusajs.com).

### What is Google Cloud Shell?

[Google Cloud Shell](https://cloud.google.com/shell/) is a free online development and operations environment accessible from any web browser. It provides command-line access to a Linux-based virtual machine instance. This machine comes preloaded with utilities and an online code editor with support for popular programming languages.

![Google Cloud Shell](https://res.cloudinary.com/craigsims808/image/upload/v1682060511/articles/scarlet-mouse/cloud-shell-intro_ghhxen.png)

Cloud Shell offers a Virtual Machine Instance with the following features:

- 2 CPUs/4 CPUs

- 8GB/16GB of RAM

- 5GB of persistent disk storage in your $HOME folder

- 50 hours of weekly access

All in all Google Cloud Shell is a suitable environment to quickly test out a Medusa app, especially when you are on the go.

## Prerequisites

To follow along this tutorial you must have the following:

- Google Account: [Sign up here](https://accounts.google.com/signup)
- Google Cloud CLI - [Download it here](https://cloud.google.com/sdk/docs/install)
- Web browser of your choice
- SSH client of your choice

## Step 1 - Launch Google Cloud Shell Workspace

In your browser, log in to your Google Account and open up the Google Cloud Shell by visiting [shell.cloud.google.com](https://shell.cloud.google.com). This launches your Google Cloud Shell and you will see a window split into two with the Cloud Shell Editor on top and the terminal on the bottom.

In the terminal, you will see the following message, “Welcome to Cloud Shell! Type “help” to get started.”

![New Google Cloud Shell Instance](https://res.cloudinary.com/craigsims808/image/upload/v1682061654/articles/scarlet-mouse/cloud-shell_pg6rpd.png)

## Step 2 - Setup Medusa backend

Next, you will install Medusa backend using the [Quickstart guide](https://docs.medusajs.com/development/backend/install).

Google Cloud Shell comes pre-installed with `node`, `npm` and `yarn`. These are the necessary tools required to install Medusa.

In your Google Cloud Shell terminal window, install the Medusa CLI.
```bash
npm install @medusajs/medusa-cli -g
```

Create a new Medusa project for your backend and seed it with some data.
```bash
medusa new my-medusa-store --seed
```

Start your Medusa backend server
```bash
cd my-medusa-store
medusa develop
```

Your Medusa server should start running on port 9000. Test it, in a new terminal tab. Click on the `+` icon in the Cloud Shell terminal window to open a new terminal

Enter the following command to test if the Medusa backend is working.
```bash
curl localhost:9000/store/products
```

The beginning of the JSON response should be similar to this:
```json
{
    "products": [
        {
            "id": "prod_01GWTHY98VZ3BHWFJX9DNXJ6PY",
            "title": "Medusa Coffee Mug",
            "subtitle": null,
            "status": "published",
            "external_id": null,
            "description": "Every programmer's best friend.",
            "handle": "coffee-mug",
            "is_giftcard": false,
            "discountable": true,
            "thumbnail": "https://medusa-public-images.s3.eu-west-1.amazonaws.com/coffee-mug.png",
            "profile_id": "sp_01GWTHY89ZKN791N6H948SVA38",
            "collection_id": null,
            "type_id": null,
            "weight": 400,
            "length": null,
            "height": null,
            "width": null,
            "hs_code": null,
            "origin_country": null,
            "mid_code": null,
            "material": null,
```

## Step 3 - Setup Next.js storefront

Next you will install the Next.js starter storefront to view the products from the server using the [Next.js Storefront Quickstart Guide](https://docs.medusajs.com/starters/nextjs-medusa-starter) in the Medusa Documentation.

In a new Cloud Shell terminal tab, create a new Next.js project using the Medusa starter template:

```bash
npx create-next-app -e https://github.com/medusajs/nextjs-starter-medusa my-medusa-storefront
```

Set up environment variables for your Next.js storefront:

```bash
cd my-medusa-storefront
mv .env.template .env.local
```

Make sure the Medusa backend server is running and then run the Next.js app:

```bash
npm run dev
```

Your Next.js storefront should start running on port 8000.

Google Cloud Shell has got a [Web Preview](https://cloud.google.com/shell/docs/using-web-preview) feature. This allows you to preview your storefront running on port 8000. The Web Preview button  can be found on the top right of the Cloud Shell taskbar.

![Web Preview](https://res.cloudinary.com/craigsims808/image/upload/v1682229181/articles/scarlet-mouse/cloud-shell-web-preview_ocktua.png)

A new tab with the URL of the preview link to the storefront will open. Here's the storefront.

![Google Cloud Shell Storefront Preview](https://res.cloudinary.com/craigsims808/image/upload/v1683042521/articles/scarlet-mouse/storefront-preview_y9u6my.png)

The storefront loads but the products from the backend server do not appear. This is due to a Network Error due to some CORS issue.

To get around this we will use port forwarding to preview the storefront.

## Step 4 - Setup SSH port forwarding

In this step we will use SSH port forwarding to preview the storefront in your browser. If you want to know more about SSH port forwarding please check out this [article](https://www.ssh.com/academy/ssh/tunneling-example).

Make sure you have the following:
- An SSH client (Use either [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) or [OpenSSH](https://www.openssh.com/portable.html))
- [Google Cloud CLI shell](https://cloud.google.com/sdk/docs/install) 

Initialize your Google Cloud CLI using these [instructions](https://cloud.google.com/sdk/docs/initializing).

Launch your Google Cloud SDK Shell and enter the following command to forward port 9000 for the Medusa server to your local machine's port 9000.

```bash
gcloud cloud-shell ssh --ssh-flag="-L 9000:localhost:9000"
```

If all works well port 9000 from the remote Google Cloud Shell workspace should be available on your local machine you can test using `curl localhost:9000/store/products` and you should see the list of products.

Next forward port 8000 for the storefront. Open a new Google Cloud CLI session. Copy the previous command and replace 9000 with 8000.

```bash
gcloud cloud-shell ssh --ssh-flag="-L 8000:localhost:8000"
```

Step 5 - Preview Next.js Starter Storefront

Open a new tab and go to `localhost:8000` in your browser. You should see the Next.js starter storefront once again. 

![Next.js Starter Storefront](https://res.cloudinary.com/craigsims808/image/upload/v1683048975/articles/scarlet-mouse/nextjs-starter-storefront_ppk4ba.png)

No error message! Everything is working as expected.

The difference now is that the products load and there is no network error. With that done, you have successfully ran and tested your storefront.

You can proceed to experiment more with your Medusa powered commerce store.

## Conclusion

Congratulations! You have managed to set up a Medusa backend with a Next.js storefront on an online development environment provided by Google Cloud Shell.

You also learned how to use SSH port forwarding to preview the storefront to bypass any network or CORS issues.

This tutorial has introduced you to Medusa and Google Cloud Shell. You can use these tools to rapidly build and test ecommerce applications efficiently. You can now continue to customize and build upon this foundation to create an e-commerce application that meets your specific needs. Keep exploring and have fun building!

### Useful Links

- [Medusa Documentation](https://docs.medusajs.com)
- [Google Cloud Shell Documentation](https://cloud.google.com/shell/docs)
- [Next.js Documentation](https://nextjs.org/docs/getting-started)
- [SSH Resource](https://www.ssh.com/academy/ssh)