## Udacity Frontend Developer Nanodegree Project 4 - Website optimization

#Sources used in the project:

https://developer.chrome.com/devtools/docs/timeline
https://developer.chrome.com/devtools/docs/demos/too-much-layout
http://markgoodyear.com/2014/01/getting-started-with-gulp/
http://csstriggers.com/


#Steps taken to optimize Page Insight Score

- Forked project to my repo, and cloned it on a cloud webserver running Apache2: http://vindrop.noip.me:8080

- Analyzed index.html with Page Insight with this result:

- The first suggestion is to optimize images. In particular recording the timeline on Google Developer Tools shows that pizzeria.jpg is a whopping ~2.5 MB and takes ~1.5s to download.

- The image is used in pizza.html as well, but never gets bigger thatn 720x540. Saving the image with medium compression, and stripping metadata (as suggested by the "Optimizing Images" on PI) yields a substantial score increase.

- The next issue seems to be the analytics.js script that is blocking. Since it is not needed to render the page, we can set it with async.

- Then we have render blocking css. The most immediate improvement seems to be adding a media="print" attribute when loading print.css, since it seems related only to printing, and won't loaded by the browser normally.

- PSI complains about the Google Open Fonts but after researching into it, and finding a Stack Overflow response by Ilya (http://stackoverflow.com/questions/12147595/how-do-i-use-google-fonts-on-my-site-without-compromising-the-page-speed-of-the), it is probably better to leave it be.

- But since there doesn't seem any mean to appease PSI on this issue, I removed the Google Web Fonts link, I and I fall back on normal sans-serif font family supported by the user browers.

- Finally, the score improves by inlining css styles directly in the html, although it makes it not so readable.

- To further improve, the embedded css is minified, using the minify command (npm node module)

- Finally, converting pizza.png to compressed jpg, further compressing pizzeria.png allows to get a score > 90 for all pages.

- Automated minification and image compressio with gulp, using this tutorial http://markgoodyear.com/2014/01/getting-started-with-gulp/


#Steps taken to run pizza.html at 60 fps

- Loading js console and scrolling the page we see a trace showing the ms for page update during scrolling. The average seems to be between 30 and 40 ms, so <= 30fps.

- Looking at the code, we see that at the bottom there is a callaback for the 'scroll' event, calling function 'updateposition'

- In this function, leaving uncommented only the metrics functions, we get to an update interval of less than 0.03ms, showing that the commented code is the bottleneck. At the same time, of course, the pizzas in the background stay still, since we have removed the relevant code. At least we have isolated the part of the code responsibles for the bottleneck.
