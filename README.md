## Udacity Frontend Developer Nanodegree Project 4 - Website optimization

#Steps taken to optimize

- Forked project to my repo, and cloned it on a cloud webserver running Apache2: http://vindrop.noip.me:8080

- Analyzed index.html with Page Insight with this result:

- The first suggestion is to optimize images. In particular recording the timeline on Google Developer Tools shows that pizzeria.jpg is a whopping ~2.5 MB and takes ~1.5s to download.

- The image is used in pizza.html as well, but never gets bigger thatn 720x540. Saving the image with medium compression, and stripping metadata (as suggested by the "Optimizing Images" on PI) yields a substantial score increase.

- The next issue seems to be the analytics.js script that is blocking. Since it is not needed to render the page, we can set it with async.

- Then we have render blocking css. The most immediate improvement seems to be adding a media="print" attribute when loading print.css, since it seems related only to printing, and won't loaded by the browser normally.

- PSI complains about the Google Open Fonts but after researching into it, and finding a Stack Overflow response by Ilya (http://stackoverflow.com/questions/12147595/how-do-i-use-google-fonts-on-my-site-without-compromising-the-page-speed-of-the), it is probably better to leave it be.

- Since there doesn't seem any mean to appease PSI on this issue, I removed the Google Web Fonts link, I and I fall back on normal sans-serif font family supported by the user browers.

-Finally, the score improves by inlining css styles directly in the html, although it makes it not so readable.

-To further improve, the embedded css is minified.

-Finally, converting pizza.png to compressed jpg, further compressing pizzeria.png allows to get a score > 90 for all pages.
