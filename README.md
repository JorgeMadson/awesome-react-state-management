# Awesome React State Management [![Awesome](https://cdn.rawgit.com/sindresorhus/awesome/d7305f38d29fed78fa85652e3a63e154dd8e8829/media/badge.svg)](https://github.com/sindresorhus/awesome)
This repository provides a deeper explanation of the various state management libraries for React, as listed in the [Awesome React repository](https://github.com/enaqx/awesome-react#react-state-management ).

- [context](#context) - React Context API (useContext)
- [redux](#redux) - Predictable State Container for JavaScript Apps
- [mobx](#mobx) - Simple, scalable state management
- [react-query](#react-query) - Hooks for fetching, caching and updating asynchronous data in React
- [flux](#flux) - Application architecture for building user interfaces
- [recoil](#recoil) - Experimental state management library for React apps
- [jotai](#jotai) - Bottom-up approach to React state management with an atomic model
- [xstate-react](#xstate-react) - State machines and statecharts for the modern web
- [zustand](#zustand) - Bear necessities for state management in React
- [easy-peasy](#easy-peasy) - Vegetarian friendly state for React
- [hookstate](#hookstate) - The simple but very powerful and incredibly fast state management for React that is based on hooks
- [effector](#effector) - Fast and powerful reactive state manager
- [reactn](#reactn) - React, but with built-in global state management
- [react-facet](#react-facet) - Observable-based state management for performant game UIs built in React

## Explanation of each State Management
### Context
- [context](https://beta.reactjs.org/reference/react/useContext) - React Context API (useContext)
> This is a built-in way to share data between components without passing props down multiple levels. It is lightweight and easy to use, making it a great option for smaller projects.
#### How to use
```javascript
// Context.js
import React from 'react';

const UserContext = React.createContext();

export default UserContext;

// App.js
import React, { useState } from 'react';
import UserContext from './Context';

function App() {
  const [user, setUser] = useState({ name: 'John Doe' });

  return (
    <UserContext.Provider value={{ user, setUser }}>
      <Main />
    </UserContext.Provider>
  );
}

// Main.js
import React, { useContext } from 'react';
import UserContext from './Context';

function Main() {
  const { user, setUser } = useContext(UserContext);

  return (
    <div>
      <h1>Hello, {user.name}!</h1>
      <button onClick={() => setUser({ name: 'Jane Doe' })}>
        Change Name
      </button>
    </div>
  );
}

```
### Redux
- [redux](https://github.com/reduxjs/redux) - Predictable State Container for JavaScript Apps
> This is a predictable state container for JavaScript apps that helps you write applications that behave consistently, run in different environments (client, server, and native), and are easy to test.
#### How to use
```javascript
// actions.js
export const updateUser = (user) => ({
  type: 'UPDATE_USER',
  user,
});

// reducer.js
export default function userReducer(state = { name: 'John Doe' }, action) {
  switch (action.type) {
    case 'UPDATE_USER':
      return action.user;
    default:
      return state;
  }
}

// store.js
import { createStore } from 'redux';
import userReducer from './reducer';

const store = createStore(userReducer);

export default store;

// App.js
import React, { useState } from 'react';
import { connect } from 'react-redux';
import { updateUser } from './actions';

function App({ user, updateUser }) {
  return (
    <div>
      <h1>Hello, {user.name}!</h1>
      <button onClick={() => updateUser({ name: 'Jane Doe' })}>
        Change Name
      </button>
    </div>
  );
}

const mapStateToProps = (state) => ({
  user: state,
});

const mapDispatchToProps = (dispatch) => ({
  updateUser: (user) => dispatch(updateUser(user)),
});

export default connect(mapStateToProps, mapDispatchToProps)(App);

```
### Mobx
- [mobx](https://github.com/mobxjs/mobx) - Simple, scalable state management
> MobX is a simple, scalable state management library that makes it easy to react to changes in your application state. With MobX, you can quickly create reactive, performant applications with a minimal amount of boilerplate code.
#### How to use
```javascript
// store.js
import { observable, action } from 'mobx';

class UserStore {
  @observable user = { name: 'John Doe' };

  @action updateUser = (user) => {
    this.user = user;
  };
}

const store = new UserStore();

export default store;

// App.js
import React from 'react';
import { observer } from 'mobx-react';
import store from './store';

const App = observer(() => (
  <div>
    <h1>Hello, {store.user.name}!</h1>
    <button onClick={() => store.updateUser({ name: 'Jane Doe' })}>
      Change Name
    </button>
  </div>
));

export default App;
```
### React-query
- [react-query](https://github.com/tannerlinsley/react-query) - Hooks for fetching, caching and updating asynchronous data in React
> This is a set of hooks for fetching, caching, and updating asynchronous data in React. It works seamlessly with the React context API and provides a powerful set of tools for managing your application data.
#### How to use
```javascript
// api.js
export const fetchUser = () => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve({ name: 'John Doe' });
    }, 1000);
  });
};

// App.js
import React from 'react';
import { useQuery } from 'react-query';
import { fetchUser } from './api';

function App() {
  const { data, isLoading, error } = useQuery('user', fetchUser);

  if (isLoading) {
    return <div>Loading...</div>;
  }

  if (error) {
    return <div>Error: {error.message}</div>;
  }

  return (
    <div>
      <h1>Hello, {data.name}!</h1>
    </div>
  );
}

export default App;
```
### Flux
- [flux](http://facebook.github.io/flux/) - Application architecture for building user interfaces
> Flux is an application architecture for building user interfaces that enforces a unidirectional data flow and helps keep your code organized and maintainable.
#### How to use
```javascript
// constants.js
export const UPDATE_USER = 'UPDATE_USER';

// actions.js
import { UPDATE_USER } from './constants';

export const updateUser = (user) => ({
  type: UPDATE_USER,
  user,
});

// store.js
import { Dispatcher } from 'flux';

const dispatcher = new Dispatcher();

const UserStore = {
  user: { name: 'John Doe' },

  register(callback) {
    dispatcher.register(callback);
  },

  updateUser(user) {
    this.user = user;
    dispatcher.dispatch({ type: UPDATE_USER, user });
  },
};

export default UserStore;

// App.js
import React, { useState, useEffect } from 'react';
import UserStore from './store';
import { updateUser } from './actions';

function App() {
  const [user, setUser] = useState(UserStore.user);

  useEffect(() => {
    UserStore.register((action) => {
      setUser(action.user);
    });
  }, []);

  return (
    <div>
      <h1>Hello, {user.name}!</h1>
      <button onClick={() => updateUser({ name: 'Jane Doe' })}>
        Change Name
      </button>
    </div>
  );
}

export default App;
```
### Recoil
- [recoil](https://github.com/facebookexperimental/Recoil) - Experimental state management library for React apps
> This is an experimental state management library for React apps that provides a powerful set of tools for managing and updating your application state. It uses a novel approach to state management that makes it easy to manage complex, interrelated data structures.
#### How to use
```javascript
// atoms.js
import { atom } from 'recoil';

export const userState = atom({
  key: 'userState',
  default: { name: 'John Doe' },
});

// App.js
import React from 'react';
import { useRecoilState } from 'recoil';
import { userState } from './atoms';

function App() {
  const [user, setUser]
  const [user, setUser] = useRecoilState(userState);

  return (
    <div>
      <h1>Hello, {user.name}!</h1>
      <button onClick={() => setUser({ name: 'Jane Doe' })}>
        Change Name
      </button>
    </div>
  );
}

export default App;
```
### Jotai
- [jotai](https://github.com/pmndrs/jotai) - Bottom-up approach to React state management with an atomic model
> This library takes a bottom-up approach to React state management with an atomic model. It provides a set of tools for breaking down your application state into smaller, more manageable pieces, making it easier to reason about and debug your code.
#### How to use
```javascript
// store.js
import { createStore } from 'jotai';

const userStore = createStore({ name: 'John Doe' });

export default userStore;

// App.js
import React from 'react';
import userStore from './store';

function App() {
  const [user, setUser] = userStore.useStore();

  return (
    <div>
      <h1>Hello, {user.name}!</h1>
      <button onClick={() => setUser({ name: 'Jane Doe' })}>
        Change Name
      </button>
    </div>
  );
}

export default App;
```
### Xstate-react
- [xstate-react](https://github.com/davidkpiano/xstate/tree/master/packages/xstate-react) - State machines and statecharts for the modern web
> Xstate is a library for state machines and statecharts that makes it easy to model complex, dynamic user interfaces. With Xstate-react, you can use state machines and statecharts to manage your application state in a clear and concise way.
#### How to use
```javascript
// state-machine.js
import { Machine } from 'xstate';

const userMachine = Machine({
  id: 'user',
  initial: 'idle',
  context: {
    name: 'John Doe',
  },
  states: {
    idle: {
      on: {
        UPDATE: {
          actions: (ctx, event) => {
            ctx.name = event.name;
          },
        },
      },
    },
  },
});

export default userMachine;

// App.js
import React from 'react';
import { useMachine } from '@xstate/react';
import userMachine from './state-machine';

function App() {
  const [current, send] = useMachine(userMachine);

  return (
    <div>
      <h1>Hello, {current.context.name}!</h1>
      <button onClick={() => send('UPDATE', { name: 'Jane Doe' })}>
        Change Name
      </button>
    </div>
  );
}

export default App;
```
### Zustand
- [zustand](https://github.com/pmndrs/zustand) - Bear necessities for state management in React
> Zustand is a minimalist state management library for React that provides a simple, lightweight way to manage your application state. With Zustand, you can keep your code simple and fast while still getting the benefits of a robust state management solution.
#### How to use
```javascript
// store.js
import create from 'zustand';

const userStore = create((set) => ({
  user: { name: 'John Doe' },
  updateUser: (user) => set({ user }),
}));

export default userStore;

// App.js
import React from 'react';
import userStore from './store';

function App() {
  const [user, updateUser] = userStore(({ user, updateUser }) => [user, updateUser]);

  return (
    <div>
      <h1>Hello, {user.name}!</h1>
      <button onClick={() => updateUser({ name: 'Jane Doe' })}>
        Change Name
      </button>
    </div>
  );
}

export default App;
```
### Easy-peasy
- [easy-peasy](https://github.com/ctrlplusb/easy-peasy) - Vegetarian friendly state for React
> Easy-Peasy is a vegetarian friendly state management library for React that provides a simple, intuitive way to manage your application state. With Easy-Peasy, you can keep your code clean and organized while still getting the power and performance you need.
#### How to use
```javascript
// store.js
import { createStore } from 'easy-peasy';

const userStore = createStore({
  user: { name: 'John Doe' },
  updateUser: (state, user) => {
    state.user = user;
  },
});

export default userStore;

// App.js
import React from 'react';
import { useStore } from 'easy-peasy';
import userStore from './store';

function App() {
  const { user, updateUser } = useStore(userStore);

  return (
    <div>
      <h1>Hello, {user.name}!</h1>
      <button onClick={() => updateUser({ name: 'Jane Doe' })}>
        Change Name
      </button>
    </div>
  );
}

export default App;
```
### Hookstate
- [hookstate](https://github.com/avkonst/hookstate) - The simple but very powerful and incredibly fast state management for React that is based on 
hooks
> Hookstate is a fast and powerful state management library for React that is based on hooks. With Hookstate, you can easily manage your application state in a simple, intuitive way that is optimized for performance.
#### How to use
```javascript
// App.js
import React from 'react';
import { useState } from 'hookstate';

const userState = useState({ name: 'John Doe' });

function App() {
  const [user, setUser] = userState;

  return (
    <div>
      <h1>Hello, {user.name}!</h1>
      <button onClick={() => setUser({ name: 'Jane Doe' })}>
        Change Name
      </button>
    </div>
  );
}

export default App;
```
### Effector
- [effector](https://github.com/zerobias/effector) - Fast and powerful reactive state manager
> Effector is a fast and powerful reactive state manager that makes it easy to react to changes in your application state. With Effector, you can create fast, responsive applications with a minimal amount of boilerplate code. 
#### How to use
```javascript
// store.js
import { createEvent, createStore } from 'effector';

const updateUserEvent = createEvent();

const userStore = createStore({ name: 'John Doe' })
  .on(updateUserEvent, (_, user) => user);

export { userStore, updateUserEvent };

// App.js
import React from 'react';
import { useStore } from 'effector-react';
import { userStore, updateUserEvent } from './store';

function App() {
  const user = useStore(userStore);

  return (
    <div>
      <h1>Hello, {user.name}!</h1>
      <button onClick={() => updateUserEvent({ name: 'Jane Doe' })}>
        Change Name
      </button>
    </div>
  );
}

export default App;
```
### Reactn
- [reactn](https://github.com/CharlesStover/reactn) - React, but with built-in global state management
> Reactn is a global state management library for React that provides a simple, intuitive way to manage your application state. With Reactn, you can easily manage your application state in a way that is optimized for performance and maintainability. 
#### How to use
```javascript
// store.js
import ReactN from 'reactn';

ReactN.init({
  user: { name: 'John Doe' },
});

// App.js
import React from 'react';
import ReactN from 'reactn';

function App() {
  const [user, setUser] = ReactN.useGlobal('user');

  return (
    <div>
      <h1>Hello, {user.name}!</h1>
      <button onClick={() => setUser({ name: 'Jane Doe' })}>
        Change Name
      </button>
    </div>
  );
}

export default App;
```
### React-facet
- [react-facet](https://github.com/Mojang/ore-ui/tree/main/packages/%40react-facet/) - Observable-based state management for performant game UIs built 
in React
> React-Facet is an observable-based state management library for performant game UIs built in React. With React-Facet
#### How to use
```javascript
// store.js
import React from 'react';
import { createObservable } from 'react-facet';

const userObservable = createObservable({ name: 'John Doe' });

export default userObservable;

// App.js
import React from 'react';
import userObservable from './store';

function App() {
  const user = userObservable.useValue();

  return (
    <div>
      <h1>Hello, {user.name}!</h1>
      <button
        onClick={() => userObservable.update(user => ({ name: 'Jane Doe' }))}
      >
        Change Name
      </button>
    </div>
  );
}

export default App;
```
