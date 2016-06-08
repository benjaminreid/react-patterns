# Store, reducers and actions

This is the pattern I'm using to employ Redux to maintain React's state. It
allows these key things.

* Individual reducers to handle small parts of the state.
* An easy way to combine all the reducers into the store.
* Asynchronous actions to handle things like AJAX requests.
* Handle router state within the application state.
* Keeps reducer action type's in sync with action creators.

And assumes a few things

* [React Router](https://github.com/reactjs/react-router) is in use with
[redux bindings](https://github.com/reactjs/react-router-redux).
* [Thunk](https://github.com/gaearon/redux-thunk) middleware to allow for async actions.

## Structure

```
.
├── actions
│   └── posts.js
├── reducers
│   ├── index.js
│   └── posts.js
└── store
    └── index.js
```

## Store

This store makes it easy to combine multiple reducers of your own, apply
the router's reducer and apply the thunk middleware. Once this is in place,
it can be pretty much left alone.

`store/index.js`

```js
import { createStore, combineReducers, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import { routerReducer } from 'react-router-redux';
import reducers from 'reducers/';

const store = createStore(
  combineReducers({
    ...reducers,
    routing: routerReducer
  }),
  applyMiddleware(thunk)
);

export default store;
```

## Reducers

This is a pretty "dumb" file, it purely collects together all your applications
reducers and exports them back into a single object. This allows the store to
import all of them at once to be combined easily.

`reducers/index.js`

```js
import posts from 'reducers/posts';

export default {
  posts
};

```

A reducer should be created for each part of the state. It should load in the
action type constants from the related action to keep things in sync.

If a single reducer becomes a bit too unwiedly, you could consider splitting
it up into multiple files and composing it here.

`reducers/posts.js`

```js
import { LOAD_POSTS } from 'actions/posts';

const reducer = function(state = [], action) {
  switch(action.type) {
    case LOAD_POSTS:
      return [...action.posts];
  }

  return state;
};

export default reducer;
```

## Actions

An actions file should be created for each reducer. It should expose the action
creator constants and the actions themselves. This makes the constants easy
to import into the reducers and the actions easy to import into the places
where you need to call them.

`actions/posts.js`

```js
export const LOAD_POSTS = 'LOAD_POSTS';

export function loadPosts(posts) {
  return {
    type: LOAD_POSTS,
    posts
  }
}
```
