---
title: Easy async API requests on localhost
date: 2015-02-28T11:26:26+00:00
author: Joao Pereira
layout: post
categories:
  - How to
---
![Good news everyone](posts/GoodNewsEveryone.jpg "")

There comes a time for API developers when it’s handy to debug on localhost, the problem is that normally there are numerous asynchronous requests from third party API’s which can prove difficult to debug.

As an example, recently I was required to work on a custom payment system based on Woocommerce and PayPal’s Adaptive Payments, and I needed to check the PayPal’s IPN request hooked to Woocommerce on localhost. There are a number of ways to expose your localhost to the world but this method I’ll describe below is by far the easiest I have ever found which I believe can be handy for anyone in a similar situation.

### The tools

In order to expose your localhost we will be using a handy tool called localtunnel, it’s pretty straightforward to install if you’re Node.js developer, just follow the step provided in localtunnel’s website:

> npm install -g localtunnel

If you don’t have Node,js’ npm, then go ahead and install it. If you use Homebrew it’s as simple as:

> brew install node

### Making sure everything looks good

Just a quick check to see if everything is ok:

> node -v  
> npm -v

These should print the version on the console. Now we are set to start directing api calls with localtunnel:

> lt &#8211;port 80

Defining the port is important, be sure your web app is listening on the same port. After running the last command, localtunnel will provide you an exposed url like:

> https://xwttelmihf.localtunnel.me

You should now be able to access your web app through this url, so go ahead and open a browser to check it out. On my case I just double checked my httpd-vhosts.conf, confirmed my app was receiving traffic on port 80 and used localtunnel’s url when setting up the IPN Response URL like so:

> $args = array( &#8216;wc-api&#8217; => &#8216;WC_MyPayPalExampleAPI&#8217;,  
> &#8216;paypal\_chain\_ipn&#8217; => &#8216;1&#8217;,  
> &#8216;order\_id&#8217; => $order\_id,);  
> $payRequest->ipnNotificationUrl = add\_query\_arg( $args, &#8216;https://xwttelmihf.localtunnel.me/&#8217; ) ;

And there it is, you are now set in probably less than 10 minutes to start debugging your web hooks locally using localtunnel.

### Some $references if you need an extra hand:

#### 1 &#8211; [localtunnel step-by-step](https://localtunnel.me/)

#### 2 &#8211; [Official Github repo](https://github.com/defunctzombie/localtunnel)

#### 3 &#8211; [Node.js’ install guide](http://nodejs.org/download/)