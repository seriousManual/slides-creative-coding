# Side effects

<!-- source: https://www.thewellproject.org/hiv-information/side-effects -->
<img src="media/sideeffects.jpg" style="border:none;box-shadow:none;width:50%">



## Side effects

<!-- source: https://practicalli.github.io/clojure/thinking-functionally/side-effects.html -->
<img src="media/functional-programmig-side-effects.png" style="border:none;box-shadow:none;width:50%">

Notes:

In computer science, a function or expression is said to have a side effect
if it modifies some state outside its scope or has an observable interaction
with its calling functions or the outside world besides returning a value.



### Examples of side effects

If a function modifies anything outside<br>
of its local scope, it has side effects:

* modify the DOM
* console.log
* server calls



### Why side effects are bad

* Changes caused by side effects are<br>
  not always visible from the outside
* Code with side effects is harder to<br>
  debug especially with concurrency
* Side effects can complicate testing

Notes:

There will always be side effects. Whenever we download data from a server,
that's a side effect. User I/O are side effects. It gets really interesting
when concurrency is involved. And that's pretty much standard in a web
environment.

Our data is delivered by a web server and that takes some time until the data
arrives in our app. During that time the app should be useable and not blocked.



### Example

```javascript
function storeUserData(state, userData) {
  localStorage.set('userData', userData);
}

// ⚠ not a pure function ⚠
function userDataReducer(state, action) {
  const userData = await fetch(action.url);
  return storeUserData(state, userData);
}
```

Notes:

We have data we want to fetch asynchronously and we want to process that
data as soon as it arrives. Up until recently the standard implementation
involves callbacks. Nowadays we use Promises (with callbacks) or async/await.

Problems:

* We have to mock `fetch`. This gets complicated as soon as there
  is more than one `fetch()` call.
* We have to be careful to make our test asynchronous. Otherwise we get
  a test that runs green even if it fails.



### Example: Test

```javascript
global.fetch = require('jest-fetch-mock');
global.localStorage = …;

it('fetches and processes user data', async () => {
  fetch.mockResponse('…');

  const response = await userDataReducer();

  expect(global.localStorage.get('userData')).toBe(…);
});
```

Notes:

* We have to replace fetch with a mocking library
* The test has to be async or needs a callback
* We have to mock fetch
* we have to mock the local storage



### Let someone else handle this

```javascript
function handleUserDataRequest(state, action) {
  returnButNotReally fetch(action.url);
  // suspend execution and let caller wait for promise
  let userData = // get the data from somewhere (caller?)
  storeUserData(state, userData);
}
```



### Solution: Generators

<pre><code class="javascript" data-noescape><mark>function*</mark> handleUserDataRequest(state, action) {
  const userData = <mark>yield</mark> fetch(action.url);
  storeUserData(state, userData);
}

let handler = handleUserDataRequest(state, {url: 'http://example.com'});
let {value} = handler.next();
value.then(data => handler.next(data));</code></pre>

Notes:

Generators can suspend their execution and return control
to the caller. The value given to `yield` is return to the
caller. The parameter given to `next()` is return by the
`yield` statement.



### Effects: Tell me what to do

```javascript
function* handleUserDataRequest(state, action) {
  try {
    let userData = yield {
      action: "call",
      operation: fetch,
      parameters: [action.url]
    };

    yield {
      action: "call",
      operation: storeUserData,
      parameters: [state, userData]
    };
  } catch (e) {
    …
  }
}
```

Notes:

Instead of calling fetch ourselves, we outsource this to our caller as well.
This is fairly easy to test now.

Saga comes with helper functions that can create these objects. They can do much
more than just "call" operations: They can also dispatch actions to the redux store
and can help model asynchronous workflows declaratively.



### Effects: Testing

```javascript
it('yields a fetch first', () => {
  let generator = handleUserDataRequest(state, {url});

  let fetchUserData = generator.next();

  expect(fetchUserData.value).toEqual({
    action: "call",
    operation: fetch,
    parameters: [url]
  });
});
```



# Saga



### Example: Fetch and store data

```javascript
import {call} from 'redux-saga';

function* handleUserDataRequest(state, action) {
  try {
    let userData = yield call(fetch, [action.url]);
    return call(storeUserData, [state, userData]);
  } catch (e) {
    …
  }
}
```



### Example: Fetch and store data

```javascript
import {call} from 'redux-saga';

it('yields a fetch first', () => {
  let generator = handleUserDataRequest(state, {url});

  let fetchUserData = generator.next();

  expect(fetchUserData.value).toEqual(call(fetch, [url]));
});
```



## Connect to redux



### Define middleware

```javascript
import { createStore, applyMiddleware } from 'redux'
import createSagaMiddleware from 'redux-saga'

import saga from './lib/state/saga';
import { reducer } from './lib/state/state';

const sagaMiddleware = createSagaMiddleware();
const store = createStore(reducer, applyMiddleware(sagaMiddleware));
sagaMiddleware.run(saga);
```

Notes:

We define a middleware that intercepts redux actions and routes them to our sagas

We first create the saga middleware, pass it to the createStore call and then
pass our saga to the middleware.



### Grab actions

`takeEvery`, `takeLatest`, `throttle`

These functions connect the sagas to the redux store: They define what redux
actions the saga should react to and how.

Notes:

* takeEvery: Process every message
* takeLatest: Only process the latest message
* throttle: consider multiple messages but throttle the input



### Effects

* `put`: Dispatch redux actions
* `call` & `apply`: Call a function
* `select`: Extract information from the current state
* `fork`: Call a function but don't suspend the saga
* `join`: Join saga with a forked call
* `cancel`: Cancel a fork
* `spawn`: Like fork but without the possibility to join

<https://redux-saga.js.org/docs/api/>



### Example: saga to fetch user data

```javascript
import {call, put, takeLatest} from 'redux-saga';

function* userDataSaga() {
  yield takeLatest(USER_DATA_REQUESTED, handleUserDataRequest);
}

function* handleUserDataRequest() {
  try {
    const url = '/api/v1/users/';
    const users = yield call(() => fetch(url));
    yield put({type: USER_FETCH_SUCCEEDED, users: users.data});
  } catch (e) {
    yield put({type: USER_FETCH_FAILED, message: e.message});
  }
}
```



### Example: saga to store favorites

```javascript
import { select, takeLatest } from 'redux-saga/effects';

import { SET_FAVORITE, UNSET_FAVORITE } from '../state';
import { storeFavorites } from '../../localStorage';

export function* watchFavorites() {
  yield takeLatest([SET_FAVORITE, UNSET_FAVORITE], handleFavoriteChange);
}

function* handleFavoriteChange() {
  const favorites = yield select(state => state.favorites);
  storeFavorites(favorites);
}
```



## ASSIGNMENT TIME!!!

![](media/dance.webp)
