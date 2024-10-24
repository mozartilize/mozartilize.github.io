---
layout: post
title: "To refresh API response cache"
---

Recently I learn to use axios-cache-interceptor, besides invalidating cache once data
get updated, during development I still need to manually invalidate cache by
deleting cache entries in the devtools.

The inconvinence urges me to work on a solution. As a web user, you all might
know that browsers support hard refreshing (ctrl+shift+R or ctrl+F5) and I
wanted to use it to force no-cache header on API call.

The problem is browsers aren't responsible for API calls, it interferes
only in static content like the html page and js, css assets. I got stuck
for weeks and even thought about adding a button for the feature.

Most of the stuff I found were for detecting page reload only, but some of them
took advance of the web performance API. Eventually I found the diamond beneath
it. Think of the network tab on the devtools where we can observe requests
information including if they are cached or not. And the API has a method to get
that information.

I choose to inspect the `favicon.ico` resource. It usually the first thing
browsers request, I think, so base on that we could determine if a user do a
hard refresh or not. If its response has 200 status code, API calls will follow
with no-cache header.

Here's my code snippet:

```js
async function checkForCache() {
    return new Promise((resolve, reject) => {
        const observer = new PerformanceObserver(async (list) => {
            const cachedResources = list.getEntries().filter(entry => {
                return entry.name.includes("favicon.ico");
            });
            if (cachedResources.length > 0) {
                const faviconLoadEntry = cachedResources[0];
                if (!(
                    // firefox & others
                    (
                        faviconLoadEntry.transferSize === 0 &&
                        faviconLoadEntry.decodedBodySize > 0
                    ) ||
                    // chromium based
                    faviconLoadEntry.deliveryType === "cache"
                )) {
                    console.log("clear axios cache");
                    setHardRefresh(true);
                }
                observer.disconnect();
                resolve(true);
            }
        });

        setTimeout(function() {
          observer.disconnect();
          resolve(false);
        }, 1000)
        observer.observe({ type: "resource", buffered: true });
    })

}
```

A PerformanceObserver instance observes the page's resources which buffered into
`list` variable. I only need `favicon.ico` resource so I chain with a filter.
The `deliveryType` is an experimental property and under my tests, only
chromium-based browsers have it. With firefox (and maybe safari and others),
`transferSize` and `decodedBodySize` are used as in [MDN document](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceResourceTiming/transferSize#examples).

`setHardRefresh` update the module's variable represented for hard refresh. And
API calls will use the value.

It also comes with a back-up plan that I set the timeout to 1 second to
disconnect the observer because I saw that it doesn't work perfectly that maybe
the js file contains the `checkForCache` execution load before the
`favicon.ico`.
