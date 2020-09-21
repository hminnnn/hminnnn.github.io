---
layout: post
author: HM
---

   

Was trying out MEAN stack and so here's my BlogshopsCombined web app! It's damn basic and far from complete but I just want it to be up and running..

### Angular: ###

Idk why it was so difficult to get my Angular proj up on Google App Engine!?! I tried following the interactive tutorial for nodejs but that didn't work as I was getting these errors. 

> Error: Not Found  
The requested URL /projects was not found on this server.

and 
> Syntax Error in Angular App: Unexpected token < 

Then I blindly changed the app.yaml file according to whatever answers I found online but still...
 I followed [this](https://anupampawar.com/2018/11/03/deploying-angular-5-app-on-google-app-engine/) in the end, uploading my /dist folder and app.yaml to the bucket instead of cloning my entire project from github.


My app.yaml:
```yaml
runtime: python27
api_version: 1
threadsafe: true

handlers:
- url: /
  static_files: dist/blogshop-app/index.html
  upload: dist/blogshop-app/index.html
- url: /
  static_dir: dist/blogshop-app
```

I still don't know why the runtime is python27 instead of nodejs? :/

Generated the /dist folder with  
> ng build –prod

On Google Cloud Platform, go to Storage and select my project. Upload /dist folder and app.yaml into the bucket. 

In the browser cloud shell, make a new directory
 
> mkdir blogshop-app-gpc

Sync the data from the bucket to the directory just created
  
> gsutil rsync -r (insert here the __Link for gsutil__ from the Overview tab) ./blogshop-app-gpc

When it's done, cd into blogshop-app-gpc and run the deploy command gcloud app deploy. YAY IT'S DONE :')   

---

I was getting 404 when I refresh my page. Everything works fine on first load until I refreshed the page. Googled and it seems like I have to make some server side configurations...? I decided to cheat and remove my routes since all I had was one page. Will probably have to get this fixed if I ever have multiple pages. 

---


### Express:  ###

Deploying Express on Google App Engine was way easier. I followed this https://github.com/motdotla/dotenv for my mongoose db credentials uri. 

My app.yaml:

``` yaml
runtime: nodejs
```

My package.json start script: 
``` json
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "babel-watch server.js",
    "start": "node server.js"
  },
```

Uploaded my files the same way as I did above and it'z done!

---

Oh and I didn't know the import line that I had always been using is a ES6 syntax...

``` js
import mongoose from 'mongoose';
```

I was shoook by this error when I ran "node server.js" for the first time

>SyntaxError: Unexpected token import. 

It was working in dev because of babel and
[Babel is modern JavaScript transpiler. A transpiler means your modern JavaScript code will be transformed to an older format that Node.js can understand](https://www.freecodecamp.org/news/how-to-enable-es6-and-beyond-syntax-with-node-and-express-68d3e11fe1ab/)

so I changed my imports to require instead of enabling the imports...
``` js
const mongoose = require('mongoose');
```
---



### MongoDB: ###

Got my database on MongoDB Atlas. 

My data comes from parsing the blogshops using scrapy. 

### Scrapy: ###


 [https://medium.com/better-programming/develop-your-first-web-crawler-in-python-scrapy-6b2ee4baf954](https://medium.com/better-programming/develop-your-first-web-crawler-in-python-scrapy-6b2ee4baf954)  
[https://www.datacamp.com/community/tutorials/making-web-crawlers-scrapy-python](https://www.datacamp.com/community/tutorials/making-web-crawlers-scrapy-python)  
[https://alysivji.github.io/mongodb-pipelines-in-scrapy.html](https://alysivji.github.io/mongodb-pipelines-in-scrapy.html)  
[https://docs.mongodb.com/manual/reference/method/db.collection.update/](https://docs.mongodb.com/manual/reference/method/db.collection.update/)  
[https://mongoosejs.com/docs/connections.html](https://mongoosejs.com/docs/connections.html)

