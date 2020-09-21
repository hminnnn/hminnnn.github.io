---
layout: post
author: HM
---

_Does my question sound stupid?_

I’ve been working with React and Angular in Typescript for awhile. One day when trying out a HackerRank question, I realize I didn’t know how to run my code if I wanted to do it in Typescript. I only knew how to run the entire project… :')

Turns out it's actually pretty simple.

Create a helloworld.ts file with a simple line
> console.log("helloworld")  

Create a default package.json file
> npm init

Install typescript
> npm install typescript

Install ts-node
> npm install ts-node

Create a default tsconfig.json
> npx tsc --init

To run:
> npx ts-node helloworld.ts

To generate the .js file – the typescript compiler transpiles the Typescript code to Javascript
> npx tsc helloworld.ts