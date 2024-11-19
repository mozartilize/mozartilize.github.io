---
layout: post
title: "To escape the maze of optimizations"
---

In the past, I used to work with map data visualization and one of the pain task
was loading GeoJSON files. The amount of data is large enough to make browser
interactions lagged. And they were all loaded from the beginning regardless zoom
levels. One of many lessons as of web developers that we know we should use
pagination. But at that time, I wondered what criteria I should use.
You know, the map boundary was determined based on a center point and a zoom
level, and the center point has its coordinations in floating-point numbers.
They can't be used for chunked and cacheable endpoints. Until one day I learn
about Uber's hexagon grid[0] which helps me created the endpoints to split the data
into chunks. Nevertheless, the project schedule was short and went to an end but
I have carried the feeling that the implementation wasn't optimal.

The first thing to do is split the data in small chunks. But with the default
grid XYZ tile system instead of Uber's hexagon grid. Since the endpoints now only
use x/y/z which are integers. It's easy to be cached.

The points are put in cluster ids so that all of them don't loaded on high view
(small zoom value), thanks to the `ST_ClusterDBScan` function. I only have three
layers of clusters for now and don't think it's a good fit. Like, at zoom level
15, which is still pretty far but all the points are loaded.

The event listeners for tiles loading (starts to load) and tiles unload plugged
in. I want to use the unload events because when users zoom out, the numbers of
points will be reduced based on its cluster id -- which means the points get
removed. As simple as it seems, I started doing the coding and once the
web page opens, the dev tools flooded with API calls and map interactions
jigged.

There's two main reasons:

   - Firstly, the network latency is high, about one and a half seconds. The
   	points for a tile which is not loaded yet later get rendered even the
   	tile itself is unloaded. Or the unload event got blocked until it get a
   	chance to run, it wipes out the points that should be there.
   - And the other is that it fetches the same set of points on every tile.
   	That wastes a lot of resources both in the web page and the API server.

To reduce the API calls, I set a rule that if the users zoom in, the web page
won't process the events if there's a zoom level on the same range of the cluster
has been loaded. It's not a significant reduction but I feel good when I have
it.

The first issue is the reason I put the title - the maze of optimizations. I did
a lot of trials, put listeners here and there and it doesn't seem to work.
Dealing with user interactions is extremely hard because we don't know what they
going to do with the application, especially this is a map. Not only it listens to those
two events, there's also zoom and moving events and the zoom event triggers
moveend event as well.

I spent days trying. I thought to myself what a loser I am. The load-all
approach works pretty well and there might be no issue at all because my
website never gain that much data. There's bunch of temporary solutions live
on production at my previous company without having any optimizations or
refactoring. And the people are still proud, still adding features and say it's
normal. They laugh when you cause a bug because of optimizations. Maybe they're
right. Maybe I should get along and could have a happy life.

Maybe, maybe...

I went for a new method. I pipe all the tile load and unload events into a
queue. Once the moveend triggered, all the events will be processed or filtered
out if duplicate or discarded each other. I don't care any other interactions,
just the data. I also offload it into a web worker, reducing the effect on the UI.
This time I’m relieved. The truth lies in the data. I don't have to guess or try a
scenario and then forget another.

I feel it is the answer. There's few cases bugs happen, so I still need to write
tests to ensure my specs. Feeling is relative, yes. I feel this function is not
correct. I feel the class has multiple responsibilities. But I think with
enough experience, observing few criteria give me a feeling about right or
wrong, possible or impossible. From that I dig deeper to prove my objection.

And I feel happy today.

