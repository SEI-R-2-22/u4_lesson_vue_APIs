# Vue With Apis

![](https://miro.medium.com/max/700/1*P3ODlZMsFN3uCmnTY3smaA.png)

## Getting Started

- Fork and clone
- `npm install`
- Create a `.env` in the root of this project

## Overview

In this lesson, we'll learn how to interact with RESTful API's utilizing VueJS. We'll be utilizing the Open Weather Map api to build a small weather application. Like most other frameworks and libraries, Vue has a component lifecycle that allows us to control what happens when a component is rendered, updated, and removed from the DOM tree. We'll utilize these component lifecycle methods to trigger a request to retrieve weather data. We'll also be utilizing the browsers native Geolocation API to retrieve a users location in order to send our API requests.

## Getting An API Key

Start by heading over to [Open Weather](https://home.openweathermap.org/users/sign_up) to sign up for an account, once you've signed up, you can head [here](https://home.openweathermap.org/api_keys) to view your api key.

Take your api key and add it to the `.env` file you created earlier with a variable of `VUE_APP_WEATHER_KEY`.

## Vue Component Lifecycle Methods

VueJS provides us with a few component lifecycle methods also known as `hooks`:

- mounted
- created
- updated

and more...

We'll include a full list with each hooks description in the [Resources](#Resources) section down below.

Each method has it's own responsibility in a components lifecycle. Today we'll be using `mounted` to trigger our API calls.

### Mounted

`mounted` is a hook that is triggered when a component get's loaded onto the DOM tree. This is one of the hooks that runs right when our component loads, which makes it a great candidate to trigger an API call.

## Scaffolding Our Project

Let's start by creating a few pieces of state in our `App.vue`. We'll create:

- `dailyWeather: []`,
- `currentWeather: null`

Next we'll set up a method called `getCurrentWeather` that accepts a parameter of `coords`. This method should be `async`

```js
methods :{
    async getCurrentWeather(coords){}
}
```

Now we'll import `axios` at the top of our `script` section and create a variable to access our api key:

```js
import axios from 'axios'
const API_KEY = process.env.VUE_APP_WEATHER_KEY
```

Inside of our `getCurrentWeather` method, we'll set up a `GET` request to the following endpoint:

`https://api.openweathermap.org/data/2.5/onecall?lat=${coords.latitude}&lon=${coords.longitude}&units=imperial&appid=${API_KEY}`

**Note: Make sure to utilize ` when inputting this url**

We'll plug in a few query parameters into this url using the template literal syntax:

```js
const res = await axios.get(
  `https://api.openweathermap.org/data/2.5/onecall?lat=${coords.latitude}&lon=${coords.longitude}&units=imperial&appid=${API_KEY}`
)
```

Finally we'll set the `current` property from the response to the `currentWeather` state:

```js
this.currentWeather = res.data.current
```

## Native GeoLocation Features

Most browsers come with a `GeoLocation` api built in. Today we're going to leverage that to help us get the weather in our specific location.

Start by adding `mounted(){}` below your data:

```js
mounted: function(){},
```

The `mounted` method will fire once the component get's mounted onto the `DOM` tree. This is a perfect candidate to trigger a location request from our user!

Inside of the `mounted` function, we'll add the following:

```js
navigator.geolocation.getCurrentPosition()
```

The `getCurrentPosition` function accepts `1` argument which will be a callback function. Once slight change however, we're going to be invoking our `getCurrentWeather` function from earlier in this callback which means it needs to be `async`. Let's add this callback:

```js
navigator.geolocation.getCurrentPosition(async () => {})
```

The callback function accepts `1` argument which is an object containing some really neat location data. We're interested in the `coords` property which contains the users longitude and latitude.

Let's accept an argument of `positon` in our callback and pass in `position.coords` to our `getCurrentWeather` function:

```js
navigator.geolocation.getCurrentPosition(async position => {
  await this.getCurrentWeather(position.coords)
})
```

Let's check our devtools to ensure the data was set correctly:

![api_current_weather](https://sei-r.s3.amazonaws.com/u4_lesson_vue_apis/api_current_weather.png)

### Passing Weather Data

Now that our weather data is set to state, we can provide it to a component that has been built for you already.

Let's import the `WeatherDash` component from the `components` folder:

```js
import WeatherDash from './components/WeatherDash.vue'
```

Once you've imported the component, list it in the `App` components object:

```js
components: {
    WeatherDash,
  },
```

And finally, we'll utilize the `WeatherDash` component and provide the `currentWeather` state as props along with a `v-if` directive to check for this data:

```jsx
<WeatherDash v-if="currentWeather" :currentWeather="currentWeather" />
```

After adding the `WeatherDash` component, add the following jsx to display something if our api call fails:

```jsx
<div v-else>
  <h3>No Weather</h3>
</div>
```

You should now see the following in your Vue app:

![current_weather](https://sei-r.s3.amazonaws.com/u4_lesson_vue_apis/current_weather.png)

## You Do:

Your task is to display the weekly forecast using the remaining api data in the response. Your final application should look as close as possible to the following:

![full_app](https://sei-r.s3.amazonaws.com/u4_lesson_vue_apis/full_app.png)

Requirements:

- Must display the day
- Must display the date in the following format `Sep 21` (Make sure this reflects the correct dates provided by the `dt` property in each object)
- Must display the correct icon (you can use `WeatherDash` as a reference)

Hints:

- The datetime is displayed as `UNIX` timestamps, simply passing these to the date object will not work. However, if you multiply these numbers by `1000`, it will give you a realistic time.
- The `Date` constructor may be very useful. Here are a couple of links that may help complete this task:
  - [DateString](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toDateString)
  - [Get Day](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/getDay)
- Using two individual methods for the day and date will help keep logic much easier to manage.
- The api docs give you a description of each property in the response which you can find [here](https://openweathermap.org/api/one-call-api). **You're looking for the `daily` data**.

## Recap

In this lesson, we learned how to use `Vue`'s lifecycle hooks to trigger api requests. We also utilized the browsers native geolocation features to read a users current location. Vue has tons of helpful and useful lifecycle hooks for any situation you may come across. Many frameworks and libraries follow very similar flows when it comes to working with external data and makes picking up new technology easy for developers.

## Resources

- [Native Browser Geolocation Api](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API/Using_the_Geolocation_API)
- [Openweather Api](https://openweathermap.org/api)
- [Symbol HexCodes](https://www.toptal.com/designers/htmlarrows/math/degree-sign/)
- [Vue Lifecycle Hooks](https://v3.vuejs.org/api/options-lifecycle-hooks.html)
