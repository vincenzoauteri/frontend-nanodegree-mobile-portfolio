## Udacity Frontend Developer Nanodegree Project 4 - Website optimization

Live at : http://vincenzoauteri.github.io/frontend-nanodegree-mobile-portfolio

#Sources used in the project:

http://markgoodyear.com/2014/01/getting-started-with-gulp/

https://developer.chrome.com/devtools/docs/timeline

https://developer.chrome.com/devtools/docs/demos/too-much-layout

http://csstriggers.com/

http://updates.html5rocks.com/2013/02/Profiling-Long-Paint-Times-with-DevTools-Continuous-Painting-Mode

http://calendar.perfplanet.com/2013/the-runtime-performance-checklist/

http://www.html5rocks.com/en/tutorials/speed/parallax/

http://aerotwist.com/blog/on-translate3d-and-layer-creation-hacks/


#Steps taken to optimize Page Insight Score

- Analyzed index.html with Page Insight with this low score as a result.

- The first suggestion is to optimize images. In particular recording the timeline on Google Developer Tools shows that pizzeria.jpg is a whopping ~2.5 MB and takes ~1.5s to download.

- Resized and compressed images with Photoshop using the "save for web" option.

- The next issue seems to be the analytics.js script that is blocking. Since it is not needed to render the page, we can set it to async.

- Then we have render blocking css. The most immediate improvement seems to be adding a media="print" attribute when loading print.css, since it seems related only to printing, and won't loaded by the browser normally.

- PSI complains about the Google Open Fonts. Although the visually effect is potentially not optimal, moved the file at the bottom (below the fold) of the body.

- Finally, the score improves by inlining css styles directly in the html, although it makes it not so readable.

- To further improve the situation, the embedded css is minified, using the minify command (npm node module)

- Javascript is also minified.

- Finally, automated minification and image compression with gulp, using this tutorial http://markgoodyear.com/2014/01/getting-started-with-gulp/


#Steps taken to run pizza.html at 60 fps

- No problem on normal pages, but pizza.html seems slow: looking at the timeline, we see fps below 30.

- Looking at the code, we see that at the bottom there is a callaback for the 'scroll' event, calling function 'updateposition'.

- In this function, leaving uncommented only the metrics functions, we get to an update interval of less than 0.03ms, showing that the commented code is the bottleneck. At the same time, of course, the pizzas in the background stay still, since we have removed the relevant code. At least we have isolated the part of the code responsibles for the bottleneck.

- By looking at the timeline and doing some research, it seems that the one of the problems is a forced asynchronous layout: the scrollTop value is being accessed inside the for loop where the x of the images is updates, and this forces a layout, since the browser has to calcluate the requested value again, due to the latest changes. We can solve the problem by saving the value in a local variable before entering the loop, so that it is accessed only once per scroll event.

- After the change the timeline improves drastically, but still we have expensive paint events: it seems that a very large area (bigger that the viewport) is repainted at every scroll.

- After researching a bit, it seems that the problem is due to the brower being optimized for scrolling content from the top-down, and not having objects scrolling indipendently on the page. The ideal would be to have the compositor (GPU supported) carrying out most of the work.

- In the end, after more reseach on the sources above, I carried out the following optimizations to reach a refresh rate below the 60 fps line:

  - Synced the repaint with requestAnimationFrame, and not the scroll event (the scroll event invokes requestAnimationFrame).
  - Used translate3d to do the shift along the x-axis: multiple sources report that this way the browser aggregates the images     in their own layer, not triggering a full repaint. This can be appreciated by selecting "Show paint rectangles"  and     "Show composited layer borders" on Chrome developer tools.
  - Finally, on page load (and viewport resize) we draw only the images that will be visible in the viewport, and not a fixed number indiscriminately.
  - Created another image of the same size as the background ones, so that no scale is needed.
