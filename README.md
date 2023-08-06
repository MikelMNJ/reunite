# State Wrangler

State Wrangler is a light-weight state management tool meant to work with React state libraries, like Redux or React's own Context API.

### Installation
`npm i state-wrangler --save` or `yarn add state-wrangler`

### Using in your reducer
import with: `import StateManager from 'state-wrangler';` and in your reducer, declare the following, with your initial state object as the only argument:
`const state = new StateManager(initialState);`

```jsx
import StateManager from 'state-wrangler';
import constants from './appConstants';
import _ from 'lodash';

const initial = {
  [constants.STATE_KEY_NOTIFICATIONS]: [],
};

const reducer = (initialState = initial, action = {}) => {
  const { payload } = action;
  const state = new StateManager(initialState);

  switch(action.type) {
    case constants.SAMPLE_ACTION:
      return state.update(constants.STATE_KEY_SAMPLE_SELECTOR, payload);

    case constants.ADD_NOTIFICATION:
      return state.add(constants.STATE_KEY_NOTIFICATIONS, payload);

    case constants.REMOVE_NOTIFICATION:
      const index = payload;
      return state.remove(constants.STATE_KEY_NOTIFICATIONS, index);

    case constants.SAMPLE_API_CALL:
      return state.update(constants.STATE_KEY_SAMPLE_API_RESPONSE, payload);

    default:
      return initialState;
  }
};

export default reducer;
```

### About StateManager()
State updates are handled in an immutable manner, see `StateManager()` in *src/package/stateManager.js* &mdash;
`StateManager()`, is made to be user friendly. It is intelligent enough to know if the state key being modified is a basic type,
such as a string or number, or more complex, like an Array or Object.  Meaning you **won't** have to call methods such as `state.getIn()`, `state.setIn()` etc. to update something like an array.

### Modifying state: basic or complex key values in state
`state.get(STATE_KEY_TO_GET)`: Returns the value from the target key in state.<br />
`state.add(STATE_KEY_TO_ADD, payload)`: Adds a completely new key to state with payload.<br />
`state.update(STATE_KEY_TO_UPDATE, payload)`: Replaces existing state key with payload.<br />
`state.remove(STATE_KEY_TO_REMOVE)`: Removes state key, completely.<br />

### Modifying state: arrays
`state.get(STATE_KEY_TO_GET, index)`: Returns the value of the index from the targeted state key array.<br />
`state.add(STATE_KEY_TO_ADD, payload, index)`: Adds new item to state key array with payload.<br />
`state.update(STATE_KEY_TO_UPDATE, payload, index)`: Updates specific index of state key array with payload.<br />
`state.remove(STATE_KEY_TO_REMOVE, index)`: Removes specific index from state key array.<br />

### Modifying state: objects
`state.get(STATE_KEY_TO_GET, "keyName")`: Returns the value of the key from the targeted state key object.<br />
`state.add(STATE_KEY_TO_ADD, payload, "keyName")`: Adds new key to state key object with payload as value.<br />
`state.update(STATE_KEY_TO_UPDATE, payload, "keyName")`: Updates specific key of state key object with payload as value.<br />
`state.remove(STATE_KEY_TO_REMOVE, "keyName")`: Removes specific key from state key object.<br />

### Modifying multiple state values
There are times where you may need to alter multiple state values at once, this can be done with `state.merge()`, using an array as the only argument that contains object with the following keys: `{ method, key, payload }`.

* method: `update`, `remove`, or `add` are the only valid values &mdash; `get` is has no application in a merge operation.
* `key`: the key name, as a string, of the state key you are targeting.
* `payload`: the new value of the key defined above &mdash; please note the `remove` method does not require a payload.

**Additional Note**: Due to the complexity of the merge operation, drilling in to nested objects or arrays to target specific nested keys or indices is not supported.  It is recommended to handle this ahead of time, either from your server response, or in your application before the payload hits the reducer for merging with other payloads.

```jsx
state.merge([
  { method: 'add', key: STATE_KEY_TO_ADD, payload },
  { method: 'update', key: STATE_KEY_TO_UPDATE, payload },
  { method: 'remove', key: STATE_KEY_TO_REMOVE },
]);
```