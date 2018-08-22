# vanilla-js-application

## JavaScript Components
The application uses ES6 Classes for components. They organize the front-end code.

**$ Convention**
1. Where a variable is prefixed with a $ it is a DOM element.
2. Where a component is prefixed with a $ it is a presentational component.

### What is in a component?
#### Constructor
The constructor of each component takes a [redux](redux.js.org) store.
#### Methods
##### init
Called when component is first created. Takes a config object with the keys `actionTypes` and `actions`. Calls `addEvents` method. Calls `store.subscribe()` with subscriber as it's argument.
```javascript
init ({ actionTypes, actions }) {
  this.actionTypes = actionTypes
  this.actions = actions
  this.addEvents()
  this.store.subscribe(this.subscriber.bind(this))
}
```
##### refresh
Called when route changes. Takes a config object with the keys `actionTypes`, `actions`.
```javascript
refresh ({ actionTypes, actions }) {
  this.actionTypes = actionTypes
  this.actions = actions
}
```
##### addEvents (optional)
Takes zero arguments. configure the event listeners for the components
```javascript
addEvents () {
  this.$list.addEventListener('click', this.onClick.bind(this))
}
```
##### subscriber (optional)
Listens for redux store changes. Switches over the lastAction type to determine updates to the view.
```javascript
subscriber () {
  const state = this.store.getState()
  const lastAction = state.lastAction
  switch (lastAction.type) {
    case pageActionTypes.NAVIGATE_END: {
      this.$input.value = select.selectSearchQuery(state)
      break
    }
    default: {
      break
    }
  }
}
```

## Redux
### Async Action Creators
[redux-thunk](https://github.com/reduxjs/redux-thunk) is used for async actions.
Example Action Creator:
```javascript
{
  deleteOneComplete: (payload) => ({
    type: actionTypes.DELETE_ONE_COMPLETE,
    payload
  }),
  deleteOneError: (payload) => ({
    type: actionTypes.DELETE_ONE_ERROR,
    payload
  }),
  deleteOne: function (payload) {
    return async (dispatch) => {
      try {
        await http.delete((`${URL}/api/space-vehicles/${payload}`))
        dispatch(this.deleteOneComplete(payload))
        return true
      } catch (e) {
        dispatch(this.deleteOneError(e.message))
        return false
      }
    }
  }
}
```
Dispatching from a Component Method:
```javascript
remove(uuid) {
  this.store.dispatch(this.actions.deleteOne(uuid))
}
```