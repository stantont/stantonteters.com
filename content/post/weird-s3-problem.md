+++
categories = ["Devlopment", "Technology"]
date = "2015-07-07T22:03:40-04:00"
description = ""
tags = ["aws", "s3", "hosting", "css"]
title = "Quickie: Weird W3 Problem"
+++
Last night I pushed the changes with a new stylesheet.
Tonight while looking at the site I noticed one of the styles that was in the stylesheet was not getting applied.
Chrome showed the stylesheet had been downloaded, but none of the styles were showing up on the elements.
I cleared the cache and tried force reloading several times.

I wasn't sure if it was a Chrome problem or a problem with the site, so I tried loading it in Firefox with the same result.
But while looking at the list of resources in Firefox, I noticed the content type for the stylesheet was `text/plain`.

That was odd.
I thought S3 would set that correctly automatically.
I went to the S3 console, and looked at the file.
It's content type was set to `text/plain`.
I'm not sure if S3 set this or somehow the `s3cmd` app set it somehow.

I changed it manually in S3 and reloaded the site, but unfortunately the changes were applied yet.
My site is hosted on CloudFlare, so in order to get the change applied immediately I had to turn on "Development Mode" in order to turn off their caching.
Then I reloaded the site, and *voilà*, there were my stylesheet changes.
<!--more-->
