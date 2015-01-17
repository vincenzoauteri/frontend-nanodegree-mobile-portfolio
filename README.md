## Udacity Frontend Developer Nanodegree Project 4 - Website optimization

#Steps take to optimize

- Forked project to my repo, and cloned it on a cloud webserver running Apache2

http://vindrop.noip.me:8080

- Analyzed index.html with Page Insight with this result:


- The first suggestion is to optimize images. In particular recording the timeline on Google Developer Tools shows that pizzeria.jpg is a whopping ~2.5 MB and takes ~1.5s to download.

- The image is used in pizza.html as well, but never gets bigger thatn 720x540. Saving the image with medium compression, and stripping metadata (as suggested by the "Optimizing Images" on PI) yields a substantial score increase:

- The next issue seems to be the analytics.js script that is blocking. Since it is not needed to render the page, we can set it with async.


