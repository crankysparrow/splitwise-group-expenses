# splitwise-group-expenses

My partners and I use [splitwise](https://www.splitwise.com/) to manage our household expenses, but a few things like groceries we split un-evenly. I was tired of always having to re-type the perentages into the Splitwise form, which is a bit clunky and unwieldy. So I made a little app to make it easier!

#### Vue3 :star: Quasar :star: Vite :star: Vue Router :star: Pinia :star: Axios

## Using the App

-  `git clone https://github.com/crankysparrow/splitwise-group-expenses.git`
-  `cd splitwise-group-expenses`
-  Add an API key to `.env` using the steps below
-  `npm install`
-  `npm run dev`
-  The app will open on a localhost port. Click "Login" in the top right corner to fetch your user info. Once you've done this, your groups should show up in the left sidebar. Choose a group and visit the other links in the sidebar to see recent expenses in your group, or to create a new expense.

**Note**: You must have an active Splitwise account and be a member of at least one group which has had activity in the past 6 months. This won't work for one-off transactions between individuals who are not already in a group with you.

## API Authentication

Initially this app included auth with PizzlyJS, but then Pizzly became [Nango](https://docs.nango.dev/) and I've been unable to make Nango play nicely with Quasar. So we are using an API key for now.

Note that the API key provides access to your account. Do not share it!

-  At https://secure.splitwise.com/apps/ click "Register your application"
-  Create a new app. The app's name, description, and URL can be whatever you like.
-  Once your app is created, create an API key for it and copy it.
-  In the root folder of this project, create a `.env` file and add your API key like this:

```
VITE_SPLITWISE_API_KEY=YOURAPIKEY
```
