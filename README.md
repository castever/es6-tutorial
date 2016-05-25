ES6 Quick Tutorial
===================

Follow the [step-by-step tutorial](#TDB) to get familiarized with ES6 (also known as ECMAScript, ECMAScript 2015 or ES2015).

---
# Babel

## Setup Babel

1. `npm init` and follow the wizard to initialize NPM.

2. `npm install babel-cli babel-core --save-dev` to install [babel](http://babeljs.io/docs/setup/) required modules.

3. `npm install babel-preset-es2015 --save-dev` to install the ECMAScript 2015 preset plugin.

4. `npm install http-server --save-dev` to install a lightweight http web server to run the application locally.

5. Create custom node tasks under the `scripts` section in the `package.json` with:

    ```
    "scripts": {
      "babel": "babel --presets es2015 js/main.js -o build/main.bundle.js",
      "start": "http-server" // Note: If port 8080 is taken you can specify another one using e.g http-server -p 9000
    },
    ```

## Build and Run

1. Create a `~/build` directory in the root of your project and run `npm run babel` to compile your `~/js/main.js`.

2. `npm start` to start the http server.

3. Go to http://localhost:8080/.

4. Click **Calculate** button to calculate the monthly payment of the mortgage.

5. Open `~/build/main.bundle.js` in your code editor and compare it to `~/js/main.js`.  Both files are almost identical because we haven't included any ES6 feature.

# `Let` Variable Declaration

In ES6, variables declared using `let` are block-scoped and live in the block they were defined. Note that declaring variables using `var` is function-scoped.

1. Replace all occurrences of `var` with `let` in `~/js/main.js`.

2. `npm run babel` to compile `~/js/main.js` to ES5.

3. Open your browser and click **Calculate** button; **it doesn't work**. By opening the developer console you'll see a message: *...ReferenceError: monthlyRate is not defined*.

4. Modify `calculateMonthlyPayment` function in `~/js/main.js` as follow:

    ```
    let calculateMonthlyPayment = function(principal, years, rate) {
      let monthlyRate = 0;
      if (rate) {
        monthlyRate = rate / 100 / 12;
      }
      let monthlyPayment = principal
      * monthlyRate
      / (1 - (Math.pow(1/(1 + monthlyRate), years * 12)));
      return monthlyPayment;
    };
    ```

5. Re-compile your `~/js/main.js` by running `npm run babel`.

6. Open your browser and click **Calculate**; you should see the monthly payment calculation working again.

# Object Destructuring

ES6 introduced an easy way to create objects on variables.

1. Modify `calculateMonthlyPayment` function in `~/js/main.js` as below, to **create an objects from variables**.

    ```
    return {principal, years, rate, monthlyPayment, monthlyRate};
    ```

    This is a shorthand for the following ES5 code:

    ```
    return {
      principal: principal,
      years: years,
      rate: rate,
      monthlyPayment: monthlyPayment,
      monthlyRate: monthlyRate
    };
    ```

2. Modify `~/index.html` to add the code below to display the monthly rate under the monthly payment.

    ```
    <h2>Monthly Payment: <span id="monthlyPayment" class="currency"></span></h2>
    <h3>Monthly Rate: <span id="monthlyRate"></span></h3>
    ```

3. Modify the `calculateMonthlyPayment` call in the `calcBtn` click event handler at `~/js/main.js` as below, to **create variables from an object using Destructuring**.

    ```
    let {monthlyPayment, monthlyRate} = calculateMonthlyPayment(principal, years, rate);
    ```

    This is a shorthand for the following ES5 code:

    ```
    var mortgage = calculateMonthlyPayment(principal, years, rate);
    var monthlyPayment = mortgage.monthlyPayment;
    var monthlyRate = mortgage.monthlyRate;
    ```

4. Add the code below as the last line of the `calcBtn` click event handler to display the monthly rate right after the monthly payment.

    ```
    document.getElementById("monthlyRate").innerHTML = (monthlyRate * 100).toFixed(2);
    ```

5. Build using `npm run babel`.

6. Open the browser and click **Calculate** button.

# Arrow Functions

In ES6, the arrow function is a shorthand for the ES5 function syntax.  It support both block and expression bodies.  The value of `this` inside the function is not altered; it's the same as the outside function. Now we can get rid of the habit of using `var self = this`.

1. Modify `~/js/main.js` to add the following function, right below `calculateMonthlyPayment`.

    ```
    let calculateAmortization = (principal, years, rate) => {
      let {monthlyRate, monthlyPayment} = calculateMonthlyPayment(principal, years, rate);
      let balance = principal;
      let amortization = [];
      for (let y=0; y<years; y++) {
        let interestY = 0;  //Interest payment for year y
        let principalY = 0; //Principal payment for year y
        for (let m=0; m<12; m++) {
          let interestM = balance * monthlyRate;       //Interest payment for month m
          let principalM = monthlyPayment - interestM; //Principal payment for month m
          interestY = interestY + interestM;
          principalY = principalY + principalM;
          balance = balance - principalM;
        }
        amortization.push({principalY, interestY, balance});
      }
      return {monthlyPayment, monthlyRate, amortization};
    };
    ```

2. Modify `calculateMonthlyPayment` function structure as follows:

    ```
    let calculateMonthlyPayment = (principal, years, rate) => {
    ```

3. Modify the structure of `calcBtn` click event as follows:

    ```
    document.getElementById('calcBtn').addEventListener('click', () => {
    ```

4. In the `calcBtn` click event handler, call `calculateAmortization` function instead of `calculateMonthlyPayment`.

    ```
    let {monthlyPayment, monthlyRate, amortization} = calculateAmortization(principal, years, rate);
    ```

5. Log amortization data to the developer console by using the following code, **using expression body**.

    ```
    amortization.forEach(month => console.log(month));
    ```

6. Build using `npm run babel`, open the browser and click **Calculate**.  Make sure you open the developer console to see the amortization values in the console log.

# Template Strings

In ES6, template strings are single or multi-line strings defined using ` `  (back-tick) character, allowing expressions defined inside `${}`.

1. Add the following HTML code after the `<h3>` monthly rate in the `~/index.html`:

    ```
    <table>
      <thead>
        <tr>
          <th>Year</th>
          <th class="principal">Principal</th>
          <th class="stretch"></th>
          <th class="interest">Interest</th>
          <th>Balance</th>
        </tr>
      </thead>
      <tbody id="amortization"></tbody>
    </table>
    ```

2. Add the following code at the end of `calcBtn` click event handler in `~/js/main.js`:

    ```
    let html = "";
    amortization.forEach((year, index) => html += `
      <tr>
        <td>${index + 1}</td>
        <td class="currency">${Math.round(year.principalY)}</td>
        <td class="stretch">
          <div class="flex">
            <div class="bar principal"
              style="flex:${year.principalY};-webkit-flex:${year.principalY}">
            </div>
            <div class="bar interest"
              style="flex:${year.interestY};-webkit-flex:${year.interestY}">
            </div>
          </div>
        </td>
        <td class="currency left">${Math.round(year.interestY)}</td>
        <td class="currency">${Math.round(year.balance)}</td>
      </tr>
    `);

    document.getElementById("amortization").innerHTML = html;
    ```

3. Build using `npm run babel`, open the browser and click **Calculate** button.

# Webpack

ES6 added native support for modules in JavaScript.  We will use [Webpack](http://webpack.github.io/) and Babel to convert to ES5.  At this step we will configure Webpack to help us on this process.

## Setup Webpack

1. Run `npm install babel-loader webpack --save-dev` to install **babel-loader** and **webpack** modules.

2. Add `webpack` script into `~/package.json`:

    ```
    "scripts": {
      "babel": "babel --presets es2015 js/main.js -o build/main.bundle.js",
      "start": "http-server",
      "webpack": "webpack"
    },
    ```

3. In the root of your project add a new file `~/webpack.config.js` with the following code:

    ```
    var path = require('path');
    var webpack = require('webpack');

    module.exports = {
      entry: './js/main.js',
      output: {
        path: path.resolve(__dirname, 'build'),
        filename: 'main.bundle.js'
      },
      module: {
        loaders: [
          {
            test: /\.js$/,
            loader: 'babel-loader',
            query: {
              presets: ['es2015']
            }
          }
        ]
      },
      stats: {
        colors: true
      },
      devtool: 'source-map'
    };
    ```

4. Build using `npm run webpack`, open your browser and click **Calculate**.  Webpack uses Babel behind the scenes to compile your application.  At this point the `babel` script in `~/package.json` is not required anymore.

# Modules

At this point we will create a module and build the application using Webpack.

1. Create a new file `~/js/mortgage.js`.

2. Move the `calculateMonthlyPayment` and `calculateAmortization` functions from `~/js/main.js` into `~/js/mortgage.js`.

3. Export both functions by using the `export` keyword, for example:

    ```
    export let calculateMonthlyPayment = ...;

    export let calculateAmortization = ...;
    ```

4. Use the modules created above by importing them at the top of `~/js/main.js`.

    ```
    import * as mortgage from './mortgage';
    ```

5. Modify the `calculateAmortization` function as follow:

    ```
    let {monthlyPayment, monthlyRate, amortization} =
        mortgage.calculateAmortization(principal, years, rate);
    ```

6. Run `npm run webpack`, open the browser and click **Calculate**.

# Classes

ES6 introduced the concept of class as a syntactical sugar on top of the existing prototype-based inheritance model; but it is not a new object-oriented inheritance model.

1. Create a new file `~/js/mortgage2.js` and add the class definition below, also exporting it as a module by using `export default` keyword.

    ```
    export default class Mortgage {

      constructor(principal, years, rate) {
        this.principal = principal;
        this.years = years;
        this.rate = rate;
      }

      get monthlyPayment() {
        let monthlyRate = this.rate / 100 / 12;
        return this.principal * monthlyRate / (1 - (Math.pow(1/(1 + monthlyRate),
        this.years * 12)));
      }

      get amortization() {
        let monthlyPayment = this.monthlyPayment;
        let monthlyRate = this.rate / 100 / 12;
        let balance = this.principal;
        let amortization = [];
        for (let y=0; y<this.years; y++) {
          let interestY = 0;
          let principalY = 0;
          for (let m=0; m<12; m++) {
            let interestM = balance * monthlyRate;
            let principalM = monthlyPayment - interestM;
            interestY = interestY + interestM;
            principalY = principalY + principalM;
            balance = balance - principalM;
          }
          amortization.push({principalY, interestY, balance});
        }
        return amortization;
      }
    };
    ```

2. Update the `import` in `~/js/main.js` as follows:

    ```
    import Mortgage from './mortgage2';
    ```

3. Update `calcBtn` click event handler as follows:

    ```
    document.getElementById('calcBtn').addEventListener('click', () => {
      let principal = document.getElementById("principal").value;
      let years = document.getElementById("years").value;
      let rate = document.getElementById("rate").value;
      let mortgage = new Mortgage(principal, years, rate);

      document.getElementById("monthlyPayment").innerHTML = mortgage.monthlyPayment.toFixed(2);
      document.getElementById("monthlyRate").innerHTML = (rate / 12).toFixed(2);

      let html = "";
      mortgage.amortization.forEach((year, index) => html += `
        <tr>
          <td>${index + 1}</td>
          <td class="currency">${Math.round(year.principalY)}</td>
          <td class="stretch">
            <div class="flex">
              <div class="bar principal"
                style="flex:${year.principalY};-webkit-flex:${year.principalY}">
              </div>
              <div class="bar interest"
                style="flex:${year.interestY};-webkit-flex:${year.interestY}">
              </div>
            </div>
          </td>
          <td class="currency left">${Math.round(year.interestY)}</td>
          <td class="currency">${Math.round(year.balance)}</td>
        </tr>
      `);

      document.getElementById("amortization").innerHTML = html;
    });
    ```

5. Run `npm run webpack`, open your browser at http://localhost:8080/index2.html and click **Calculate**.

# Promises

ES6 added native support for **Promises** replacing callback functions as the preferred programming style for handling asynchronous calls.  At this point we will create a simple application to use callbacks and then switch to promises.

## Using Callbacks

1. Create a new file `~/rateFinder.html` as follows:

    ```
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8">
    </head>
      <body>
        <table id="rates"></table>
        <script src="build/ratefinder.bundle.js"></script>
      </body>
    </html>
    ```

2. Create a new file `~/js/rateFinder.js` as follows:

    ```
    let url = 'rates.json';

    fetch(url)
      .then(response => response.json())
      .then(rates => {
        let html = '';
        rates.forEach(rate => html += `<tr><td>${rate.name}</td><td>${rate.years}</td><td>${rate.rate}%</td></tr>`);
        document.getElementById("rates").innerHTML = html;
      })
      .catch(e => console.log(e));
    ```

3. Modify `module.exports` in `~/webpack.config.js` as below, to compile two applications `~/js/main.js` and `~/js/rateFinder.js`:

    ```
    entry: {
      app: './js/main.js',
      rateFinder: './js/rateFinder.js'
    },
    output: {
      path: path.resolve(__dirname, 'build'),
      filename: '[name].bundle.js'
    },
    ```

4. Run `npm run webpack`, open your browser at http://localhost:8080/ratefinder.html

## Using Promises

1. Create a new file `~/js/rate-service-mock.js`.

2. Define a `rates` variable with sample data as follows:

    ```
    let rates = [
      {
        "name": "30 years fixed",
        "rate": "13",
        "years": "30"
      },
      {
        "name": "20 years fixed",
        "rate": "2.8",
        "years": "20"
      },
      {
        "name": "15 years fixed",
        "rate": "2.5",
        "years": "15"
      }
    ];
    ```

3. Add a new function `findAll()` in `~/js/rate-service-mock.js` as follows:

    ```
    export let findAll = () => new Promise((resolve, reject) => {
      if (rates) {
        resolve(rates);
      } else {
        reject("No rates");
      }
    });
    ```

4. Modify `~/js/rateFinder.js` implementation as follows:

    ```
    import * as service from './rate-service-mock';

    service.findAll()
      .then(rates => {
        let html = '';
        rates.forEach(rate => html += `<tr><td>${rate.name}</td><td>${rate.years}</td><td>${rate.rate}%</td></tr>`);
        document.getElementById("rates").innerHTML = html;
      })
      .catch(e => console.log(e));
    ```

5. Build using `npm run webpack`, open your browser at http://localhost:8080/rateFinder.html.

---
**Note: Refer [here](http://es6-features.org/) for more ES6 features.**

*Thanks http://ccoenraets.github.io/es6-tutorial/ecmascript6-setup-babel.html for the original tutorial*
