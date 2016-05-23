ES6 Quick Tutorial
===================

Follow the [step-by-step tutorial](#TDB) to get familiarized with ES6 (also known as ECMAScript, ECMAScript 2015 or ES2015).

---

## Setup Babel

1. `npm init` and follow the wizard to initialize NPM.

2. `npm install babel-cli babel-core --save-dev` to install [babel](http://babeljs.io/docs/setup/) required modules.

3. `npm install babel-preset-es2015 --save-dev` to install the ECMAScript 2015 preset plugin.

4. `npm install http-server --save-dev` to install a lightweight http web server to run the application locally.

5. Create custom node tasks under the `scripts` section in the `package.json` with:

```
"scripts": {
  "babel": "babel --presets es2015 js/main.js -o build/main.bundle.js",
  "start": "http-server"
},
```

**Note: If port 8080 is taken you can specify another one using** `http-server -p 9000`.

6. Create a `~/build` directory in the root of your project.

## Build and Run

1. `npm run babel` to compile your `~/main.js`.

2. `npm start` to start the http server.

3. Go to http://localhost:8080/.

4. Click **Calculate** button to calculate the monthly payment of the mortgage.

5. Open `~/build/main.bundle.js` in your code editor and compare it to `~/js/main.js`.  Both files are almost identical because we haven't included any ES6 feature.


---
*Followed initially from http://ccoenraets.github.io/es6-tutorial/ecmascript6-setup-babel.html*
