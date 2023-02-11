## Razzle quick start

### Sign up for a free account

Head over to [Razzle](https://app.razzle.network) and sign up for a free account. Right now we are in beta and you can only signup with a gmail account. Follow the instructions to create your account until you arrive at chat screen.

### Create a new app

You can create a new razzle app by simple telling razzle to :). Just type something like this in the text box and press enter.

`Create a new app called Razzle that is meant for my personal use`

You can vary this ☝️ prompt to create an app but the format should be something like this.

`Create a new app called <app name> that is meant for <app purpose>`

... something like this

`Create an app for managing an e-commerce store called commerce bot`

You expect to see something like this

[Create app gif]

Copy the App ID and the API key. You will need them later.

### Flesh out your app

Now that you have created your app, you can start fleshing it out. You can do this by adding actions, descriptions and parameters in your code.

Start by install the razzle sdk

```bash
npm install @razzledotai/sdk
```

Please note that we only support nodejs for now. We will be adding support for other languages soon.

In you project you can initialize the sdk like this

```js
Razzle.app({
  appId: process.env.RAZZLE_APP_ID,
  apiKey: process.env.RAZZLE_API_KEY,
  modules: [{}],
});
```

Now let's talk modules. Modules are the building blocks of your app. A odule would typically be a class that contains a group of actions that can be performed by your Razzle App

For the sake of this documentation let's build a simple module called `ExpenseManager` this is meant to mimic the backend of a company like [Brex](https://brex.com/) or [Pleo](https://pleo.io/) that issues company credit cards to employees. Just a super simple version of it.

```js

```
