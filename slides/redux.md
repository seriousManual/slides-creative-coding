<!-- Redux - https://commons.wikimedia.org/wiki/File:Redux.png -->
<img src="media/Redux.png" style="border:none;box-shadow:none;width:35%" />



### State management libraries

* With SPAs the application state can get quite big
* Mutating state can get really complex
* Especially when we mix mutation and asynchronicity

Notes:
See this codecartoon on why we need state management tools:

<https://code-cartoons.com/a-cartoon-guide-to-flux-6157355ab207>



### Store

* This is where the state lives
* Single source of truth
* Read only - the state is never mutated



<!-- twitter redux store - https://medium.com/statuscode/dissecting-twitters-redux-store-d7280b62c6b1 -->
<img src="media/twitter-store.png" height="80%" />



### Actions

* Actions express the intent to change the state
* Consists of a type and data ("payload")
* Contains no logic



```js
export function setFavorite(id) {
  return {
    type: SET_FAVORITE,
    id: id
  };
}
```



### Dispatch

Brings actions into the store

```js
class FavButton extends React.Component {
  favorite() {
    const { id, dispatch } = this.props;
    dispatch(setFavorite(id));
  }

  render() {
    return (
      <button onClick={() => this.favorite()}>
        <Heart />
      </button>
    );
  }
}
```



### Reducers

* The store passes actions to reducers
* Reducers are pure functions
* They don't mutate the state
* They return a new state, based on<br>
  the existing state and an action<br>
  → Reducer pattern



```js
export function favoritesReducer(favorites = [], action) {
  switch (action.type) {
    case SET_FAVORITE:
      return [...favorites, action.id];

    default:
      return favorites;
  }
}
```



### Data flow

<!-- redux data flow https://www.slideshare.net/gilfink/redux-data-flow-with-angular-77198205 -->
<img src="media/redux-data-flow.png" height="70%" />



### Initialize redux

```js
import {createStore} from 'redux';

const store = createStore(reducers,
  // optional middleware for chrome dev tools extensions
  window.__REDUX_DEVTOOLS_EXTENSION__ &&
  window.__REDUX_DEVTOOLS_EXTENSION__());
```



### Unit testing

* There are two things with logic that can be tested:
  * Action creators
  * Reducers
* Both are pure functions
* Standard unit testing: arrange, act, assert

Notes:

This is really easy, there is usually no mocking involved, there is
no asynchronicity, etc.



### Examples

```js
it("returns an action with the given id", () => {
  const id = 23;

  const action = setFavorite(id);

  expect(action).toEqual({ type: SET_FAVORITE, id });
});
```



```js
it("adds the session id to the favorites list", () => {
  const state = { favorites: [23, 1337] };
  const id = 42;
  const action = setFavorite(id);

  const newState = reducer(state, action);

  const expectedState = [23, 1337, 42];
  expect(newState.favorites).toEqual(expectedState);
});
```



If you want to make sure, that the state is not mutated

```js
import deepFreeze from 'deep-freeze';

it("does not mutate the state", () => {
  const id = 42;
  const action = setFavorite(id);

  const state = { favorites: [23, 1337] };
  deepfreeze(state);

  const newState = reducer(state, action);
});
```



## ASSIGNMENT TIME!!!

![](media/dance.webp)



### Connect redux and react

Wrap the App in a `Provider` and<br>pass it the redux store

```js
import { Provider } from 'react-redux'

ReactDOM.render((
  <Provider store={store}>
    <App />
  </Provider>
), document.getElementById('root'));
```



### Connect components to the store

Remember our `FavButton` component from before

```js
class FavButton extends React.Component {
  favorite() {
    const { id, dispatch } = this.props;
    dispatch(setFavorite(id));
  }

  render() {
    return (
      <button onClick={() => this.favorite()}>
        <Heart />
      </button>
    );
  }
}
```



Let's hide the button if it was favorited

```js
function mapStateToProps(state, ownProps) {
  const favs = state.favorites;
  const findOwnId = (favId) => favId === ownProps.id;
  const isFavorite = favs.find(findOwnId) !== undefined;

  return { isFavorite: isFavorite };
}

export default connect(mapStateToProps)(FavButton);
```



Use the new property inside the component

```js
render() {
  const { isFavorite } = this.props;

  if (isFavorite) {
    return '';
  }

  return (
    <button onClick={() => this.favorite()}>
      <Heart />
    </button>
  );
}
```



How to dispatch?

* Dispatch is injected into the props by `connect()`
* To completely separate the base component
  from redux, you can use `mapDispatchToProps()`
* If `mapDispatchToProps()` is used, the `dispatch`<br>
  property is no longer injected into `this.props`!



```js
function mapDispatchToProps(dispatch, ownProps) {
  return {
    favorite: () => dispatch(setFavorite(ownProps.id))
  }
}

export default connect(mapStateToProps, mapDispatchToProps)(FavButton);
```

```js
favorite() {
  this.props.favorite();
}
```



## ASSIGNMENT TIME!!!

![](media/dance.webp)
