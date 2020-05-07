# CraftCMS with Laravel Mix

By it's nature Craft CMS places your external assets like prod css, js etc files to it's ```./web``` folder. So we should create a source container for our tailwind base somewhere else. 

But let's get first thing's first. Unless it's specified everthing should be installed to the root of your CMS folder. 

## Base Setup

### Tailwind

Create a package.json for npm. ```-y ``` creates the minimal config without asking any questions.

```
npm init -y 
```
Install Tailwind 

```
npm install tailwindcss -D
```

You'll be needing the configuration file for your tailwind customizations and purge. 
	
*PurgeCSS is included in barebones of Tailwind but you need to tell it what to purge*

```
npx tailwind init
```


### Laravel Mix and PostCSS

Install Laravel Mix, Cross Env and PostCSS

``` 
npm install laravel-mix cross-env postcss-nested -D
```

Create a ```webpack.mix.js``` file in the root. 

```
touch webpack.mix.js
```
We'll be getting back to this file in a bit.


## Configuration 


Just not to mess with Craft's way of working we'll be creating seperate folder that's not been used by Craft called ```./src``` folder to place to the root. 

```
mkdir src
cd src
touch tailwind.css
```

Paste the base into the css folder. 

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

Now let's go back to the ```webpack.mix.js``` file and add these. 

```js
// Loads Laravel Mix
let mix = require("laravel-mix");

// Compiles the Tailwind base file located at src/tailwind.css to web/tailwind.css
mix.postCss("src/tailwind.css", "web/", [
  require("postcss-nested"),
  require("tailwindcss")
]);
```

You'd probably want the **Purge** option not to serve your visitors megabyte sized CSS. So get into to your ```tailwind.config.js``` folder and it should look like this. You can add more file formats that are used on your front-end like ```**/*.vue``` etc.
 
```
module.exports = {
  purge: [
    './templates/**/*.html',
    './templates/**/*.twig'
  ],
  theme: {
    extend: {},
  },
  variants: {},
  plugins: [],
}
```

Finally let's get to our ```package.json``` and create our scripts. 

```
{
  "name": "Your amazing website",
  "version": "1.0.0",
  "description": "Your amazing website's description",
  "main": "index.js",
  "scripts": {
    "prod": "npm run production",
    "dev": "cross-env NODE_ENV=development node_modules/webpack/bin/webpack.js --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
    "watch": "cross-env NODE_ENV=development node_modules/webpack/bin/webpack.js --watch --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
    "hot": "cross-env NODE_ENV=development webpack-dev-server --inline --hot --config=node_modules/laravel-mix/setup/webpack.config.js",
    "production": "cross-env NODE_ENV=production node_modules/webpack/bin/webpack.js --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js"
  },
  "keywords": [],
  "author": "You<3",
  "license": "MIT",
  "devDependencies": {
    "cross-env": "^7.0.2",
    "laravel-mix": "^5.0.4",
    "postcss-nested": "^4.2.1",
    "tailwindcss": "^1.4.5"
  }
}
```


There's 3 scripts that we've added here. So 
 
**Development:** ```npm run dev``` 

Builds the base css. Creates the huge file in your ```./web``` folder. **Run this script before everything else.** 

**Production:** ```npm run prod``` or ```npm run production```

Checks the classes you've used and purges them into a tiny file. If you try to build new stuff with this build, you'd mostly end up a bit wonky stuff. Just run the dev stuff back when working.

**Watch** ```npm run watch```

Assuming that you have a live local enviroment, with this you can actively watch your saved changes.









