## Introduction to Redux

### What is Redux?

Redux is a state management library for global UI state that needs frequent updates.

### Redux vs useReducer

| Aspect | useReducer | Redux |
|--------|------------|-------|
| Scope | Local component state | Global application state |
| Store | No centralized store | Centralized store container |
| Actions | Dispatched directly to reducer | Dispatched to store first |
| Complexity | Simpler | More complex with additional concepts |

### The Redux Cycle

1. Action Creator creates an action object
2. Action is dispatched to the store
3. Store passes action to reducer
4. Reducer calculates new state
5. Store updates state

### Action Creator Example

```javascript
function depositMoney(amount) {
  return {
    type: 'DEPOSIT_MONEY',
    payload: amount,
  };
}
```

### Redux Analogy

| Part | Analogy |
|------|---------|
| Action Creator | Person giving instructions |
| Action | The instructions themselves |
| Dispatcher | Bank teller |
| Store | Bank vault |
| Reducer | Process of updating money in vault |

---

## Creating a Reducer: Bank Account

### Reducer Function

```javascript
const initialState = {
  balance: 0,
  loan: 0,
  loanPurpose: '',
};

function reducer(state = initialState, action) {
  switch (action.type) {
    case 'account.deposit':
      return {
        ...state,
        balance: state.balance + action.payload,
      };
    case 'account.withdraw':
      return {
        ...state,
        balance: state.balance - action.payload,
      };
    case 'account.requestLoan':
      if (state.loan > 0) {
        return state;
      }
      return {
        ...state,
        loan: action.payload.amount,
        loanPurpose: action.payload.purpose,
      };
    case 'account.payLoanBack':
      return {
        ...state,
        loan: 0,
        loanPurpose: '',
        balance: state.balance - action.payload,
      };
    default:
      return state;
  }
}
```

### Key Points

| Point | Description |
|-------|-------------|
| Pure functions | Reducers calculate new state, never mutate |
| Action type format | "stateDomain.eventName" |
| Unknown actions | Return original state |
| Spread operator | Use to copy state before modifying |

---

## Creating a Redux Store

### Setup

```bash
npm install redux
```

### Creating Store

```javascript
import { createStore } from 'redux';
import reducer from './store';

const store = createStore(reducer);
```

### Dispatching Actions

```javascript
// Basic dispatch
store.dispatch({ type: 'account.deposit', payload: 500 });
console.log('State after deposit:', store.getState());

store.dispatch({ type: 'account.withdraw', payload: 200 });
console.log('State after withdraw:', store.getState());

// Action creator
const requestLoan = (amount, purpose) => ({
  type: 'account.requestLoan',
  payload: { amount, purpose },
});

store.dispatch(requestLoan(1000, 'Buy a car'));

// Action creator without payload
const payLoanBack = () => ({
  type: 'account.payLoanBack',
});

store.dispatch(payLoanBack());
```

---

## Working With Action Creators

### Creating Action Creators

```javascript
function deposit(amount) {
  return {
    type: 'ACCOUNT_DEPOSIT',
    amount: amount
  };
}

function withdraw(amount) {
  return {
    type: 'ACCOUNT_WITHDRAW',
    amount: amount
  };
}

function requestLoan(amount, purpose) {
  return {
    type: 'ACCOUNT_REQUEST_LOAN',
    amount: amount,
    purpose: purpose
  };
}

function payLoan() {
  return {
    type: 'ACCOUNT_PAY_LOAN'
  };
}
```

### Using Action Creators

```javascript
store.dispatch(deposit(100));
store.dispatch(withdraw(50));
store.dispatch(requestLoan(1000, 'Buy a cheap car'));
store.dispatch(payLoan());
```

---

## Adding More State: Customer

### Initial State

```javascript
const initialStateCustomer = {
  fullName: '',
  nationalID: '',
  createdAt: '',
};
```

### Action Creators

```javascript
function createCustomer(fullName, nationalID) {
  return {
    type: 'CUSTOMER_CREATE',
    payload: {
      fullName,
      nationalID,
      createdAt: new Date().toISOString(),
    },
  };
}

function updateName(fullName) {
  return {
    type: 'CUSTOMER_UPDATE_NAME',
    payload: { fullName },
  };
}
```

### Customer Reducer

```javascript
function customerReducer(state = initialStateCustomer, action) {
  switch (action.type) {
    case 'CUSTOMER_CREATE':
      return {
        ...state,
        fullName: action.payload.fullName,
        nationalID: action.payload.nationalID,
        createdAt: action.payload.createdAt,
      };
    case 'CUSTOMER_UPDATE_NAME':
      return {
        ...state,
        fullName: action.payload.fullName,
      };
    default:
      return state;
  }
}
```

### Combining Reducers

```javascript
import { combineReducers } from 'redux';
import accountReducer from './accountReducer';
import customerReducer from './customerReducer';

const rootReducer = combineReducers({
  account: accountReducer,
  customer: customerReducer,
});

export default rootReducer;
```

### Using Customer Actions

```javascript
store.dispatch(createCustomer('John Doe', '1234567890'));
store.dispatch(updateName('Jane Doe'));
console.log(store.getState().customer);
```

---

## Professional Redux File Structure: State Slices

### Folder Structure

```
src/
├── store.js
├── features/
│   ├── account/
│   │   └── index.js
│   └── customer/
│       └── index.js
```

### Account Slice (features/account/index.js)

```javascript
import { initialStateAccount } from './initialStateAccount';
import accountReducer from './accountReducer';
import { deposit, withdraw, requestLoan, payLoan } from './actionCreators';

export default accountReducer;
export { deposit, withdraw, requestLoan, payLoan };
```

### Customer Slice (features/customer/index.js)

```javascript
import { initialStateCustomer } from './initialStateCustomer';
import customerReducer from './customerReducer';
import { createCustomer, updateName } from './actionCreators';

export default customerReducer;
export { createCustomer, updateName };
```

### Combining Slices (store.js)

```javascript
import { combineReducers } from 'redux';
import accountReducer from './features/account/index';
import customerReducer from './features/customer/index';

const rootReducer = combineReducers({
  account: accountReducer,
  customer: customerReducer,
});

export default rootReducer;
```

### Benefits of Slices

| Benefit | Description |
|---------|-------------|
| Readability | Clear organization of related code |
| Maintainability | Easier to find and update code |
| Separation of concerns | Each slice manages its own state |

---

## Back to React: Connecting Redux App With React

### Setup

```bash
npm install react-redux
```

### Wrapping App with Provider

```javascript
import React from 'react';
import { Provider } from 'react-redux';
import { store } from './store';

const App = () => {
  return (
    <Provider store={store}>
      {/* Your application components */}
    </Provider>
  );
};
```

### Reading Data with useSelector

```javascript
import React from 'react';
import { useSelector } from 'react-redux';

const Customer = () => {
  const customer = useSelector(state => state.customer);
  const fullName = customer.firstName + ' ' + customer.lastName;

  return (
    <div>
      Welcome, {fullName}!
    </div>
  );
};
```

---

## Dispatching Actions from React App

### Using useDispatch

```javascript
import React from 'react';
import { useDispatch } from 'react-redux';

const CreateCustomer = () => {
  const dispatch = useDispatch();

  const handleClick = () => {
    const fullName = firstName + ' ' + lastName;
    const nationalID = idNumber;

    if (!fullName || !nationalID) {
      return;
    }

    dispatch(createCustomer({ fullName, nationalID }));
  };

  return (
    <div>
      <input type="text" placeholder="First Name" />
      <input type="text" placeholder="Last Name" />
      <input type="text" placeholder="National ID" />
      <button onClick={handleClick}>Create Customer</button>
    </div>
  );
};
```

### Action Creator Example

```javascript
// action creator in customerSlice.js
export const createCustomer = (customer) => ({
  type: 'CREATE_CUSTOMER',
  payload: customer,
});
```

---

## The Legacy Way of Connecting Components to Redux

### Using connect API

```javascript
import React from 'react';
import { connect } from 'react-redux';

const mapStateToProps = (state) => ({
  balance: state.account.balance,
});

const BalanceDisplay = (props) => {
  const { balance } = props;

  return (
    <div>
      Balance: ${formatCurrency(balance)}
    </div>
  );
};

export default connect(mapStateToProps)(BalanceDisplay);
```

### How connect Works

| Step | Description |
|------|-------------|
| mapStateToProps | Selects parts of Redux state as props |
| connect | Higher-order component that injects props |
| Wrapped component | Receives selected state as props |

---

## Redux Middleware and Thunks

### What is Middleware?

Middleware sits between dispatching an action and the store, allowing code to run after dispatching but before reaching the reducer.

### Why Use Middleware?

| Reason | Description |
|--------|-------------|
| Async operations | Handle API calls without blocking |
| Side effects | Logging, timers, action cancellation |
| Logic encapsulation | Keep complex logic away from components |

### Redux Thunk Setup

```bash
npm install redux-thunk
```

```javascript
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import reducer from './reducer';

const store = createStore(reducer, applyMiddleware(thunk));
```

### Thunk Action Creator Example

```javascript
const fetchUserData = (userId) => {
  return (dispatch) => {
    fetch(`https://api.example.com/users/${userId}`)
      .then(response => response.json())
      .then(userData => {
        dispatch({
          type: 'FETCH_USER_DATA',
          payload: userData
        });
      });
  };
};
```

---

## Making an API Call With Redux Thunks

### Deposit Money with Currency Conversion

```javascript
import thunk from 'redux-thunk';

export const depositMoney = (amount, currency) => {
  if (currency === 'USD') {
    return {
      type: 'ACCOUNT_DEPOSIT',
      payload: { amount }
    };
  } else {
    return async (dispatch, getState) => {
      const response = await fetch('https://frankfurter.app/latest?from=' + currency + '&to=USD');
      const data = await response.json();
      const convertedAmount = amount * data.rates.USD;

      dispatch({
        type: 'ACCOUNT_DEPOSIT',
        payload: { amount: convertedAmount }
      });
    };
  }
};
```

### Adding Loading State

```javascript
const initialState = {
  amount: 0,
  isLoading: false
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case 'ACCOUNT_DEPOSIT':
      return {
        ...state,
        amount: action.payload.amount
      };
    case 'ACCOUNT_CONVERTING_CURRENCY':
      return {
        ...state,
        isLoading: true
      };
    case 'ACCOUNT_CONVERSION_COMPLETE':
      return {
        ...state,
        isLoading: false
      };
    default:
      return state;
  }
};

export const depositMoney = (amount, currency) => {
  if (currency === 'USD') {
    return {
      type: 'ACCOUNT_DEPOSIT',
      payload: { amount }
    };
  } else {
    return async (dispatch, getState) => {
      dispatch({ type: 'ACCOUNT_CONVERTING_CURRENCY' });

      const response = await fetch('https://frankfurter.app/latest?from=' + currency + '&to=USD');
      const data = await response.json();
      const convertedAmount = amount * data.rates.USD;

      dispatch({ type: 'ACCOUNT_CONVERSION_COMPLETE' });
      dispatch({
        type: 'ACCOUNT_DEPOSIT',
        payload: { amount: convertedAmount }
      });
    };
  }
};
```

### UI with Loading Indicator

```javascript
<button disabled={isLoading}>
  {isLoading ? 'Loading...' : 'Deposit Money'}
</button>
```

---

## The Redux DevTools

### Installation

1. Install Chrome Extension: "Redux DevTools"
2. Install NPM package:

```bash
npm install redux-devtools-extension
```

### Setup

```javascript
import { createStore, applyMiddleware } from 'redux';
import { composeWithDevTools } from 'redux-devtools-extension';
import thunk from 'redux-thunk';

const store = createStore(reducer, composeWithDevTools(applyMiddleware(thunk)));
```

### Features

| Feature | Description |
|---------|-------------|
| Action list | See all dispatched actions |
| State inspection | View state before/after each action |
| Time travel | Jump back to previous states |
| Manual dispatch | Dispatch actions manually |

---

## What is Redux Toolkit (RTK)?

### Overview

Redux Toolkit is an opinionated approach to writing Redux code that encourages best practices.

### Features

| Feature | Description |
|---------|-------------|
| Automatic action creators | Generated from reducer functions |
| Immutable state updates | Uses Immer library |
| Middleware setup | Automatic Thunk and DevTools |
| Reduced boilerplate | Less code to write |

### Advantages

| Advantage | Description |
|-----------|-------------|
| Less boilerplate | Significantly reduces code |
| Easier immutability | Write mutable-looking code |
| Out-of-box tools | Thunk and DevTools pre-configured |
| Compatibility | 100% compatible with classic Redux |

---

## Creating the Store With RTK

### Setup

```bash
npm install @reduxjs/toolkit
```

### Store Configuration

```javascript
import { configureStore } from '@reduxjs/toolkit';
import accountReducer from './features/account/accountSlice';
import customerReducer from './features/customer/customerSlice';

const store = configureStore({
  reducer: {
    account: accountReducer,
    customer: customerReducer
  }
});

export default store;
```

### Benefits of configureStore

| Benefit | Description |
|---------|-------------|
| Auto combines reducers | No need for combineReducers |
| Auto adds middleware | Thunk included by default |
| Auto sets up DevTools | No manual composeWithDevTools |

---

## Creating the Account Slice

### Using createSlice

```javascript
import { createSlice } from '@reduxjs/toolkit';

const accountSlice = createSlice({
  name: 'account',
  initialState: {
    balance: 0,
    loan: 0,
    loanPurpose: '',
  },
  reducers: {
    deposit: (state, action) => {
      state.balance += action.payload;
    },
    withdraw: (state, action) => {
      state.balance -= action.payload;
    },
    requestLoan: (state, action) => {
      if (state.loan === 0) {
        state.loan = action.payload.amount;
        state.loanPurpose = action.payload.purpose;
      }
    },
    payLoan: (state) => {
      state.loan = 0;
      state.loanPurpose = '';
      state.balance -= state.loan;
    },
  },
});

export default accountSlice.reducer;
export const { deposit, withdraw, requestLoan, payLoan } = accountSlice.actions;
```

### Key Points

| Point | Description |
|-------|-------------|
| Immer | Allows mutable-looking code |
| Auto actions | Actions created from reducer names |
| Direct export | Reducer and actions exported separately |

---

## Back to Thunks

### Reusing Action Creators

```javascript
// Reuse the deposit action creator
export const deposit = (amount, currency) => ({
  type: 'account/deposit',
  payload: {
    amount: amount,
    currency: currency,
  },
});

// Reducer for currency conversion
const convertCurrency = (state) => {
  state.loading = true;
  // Convert currency here
  state.loading = false;
};

export const convertCurrency = accountSlice.actions.convertCurrency;
```

### Thunk with createAsyncThunk

```javascript
import { createAsyncThunk } from '@reduxjs/toolkit';

export const depositAsync = createAsyncThunk(
  'account/depositAsync',
  async (amount, { dispatch }) => {
    // Async logic here
    return amount;
  }
);
```

---

## Creating the Customer Slice

### Customer Slice with RTK

```javascript
import { createSlice } from '@reduxjs/toolkit';

const customerSlice = createSlice({
  name: 'customer',
  initialState: {
    customers: [],
  },
  reducers: {
    createCustomer: {
      prepare: (fullName, nationalID) => ({
        payload: {
          fullName,
          nationalID,
          createdAt: new Date().toISOString(),
        },
      }),
      reducer: (state, action) => {
        state.customers.push(action.payload);
      },
    },
    updateName: (state, action) => {
      const customerIndex = state.customers.findIndex(
        customer => customer.nationalID === action.payload.nationalID
      );
      state.customers[customerIndex].fullName = action.payload.fullName;
    },
  },
});

export const { createCustomer, updateName } = customerSlice.actions;
export default customerSlice.reducer;
```

### Prepare Function

The prepare function formats the action payload before it reaches the reducer.

---

## Redux vs. Context API

### Comparison Table

| Aspect | Context API + useReducer | Redux |
|--------|--------------------------|-------|
| Built-in | Yes, no extra packages | No, requires packages |
| Setup | Simple for single context | More complex initially |
| Async operations | No built-in support | Built-in with middleware |
| Performance | Manual optimization needed | Highly optimized |
| DevTools | Limited | Excellent |
| Bundle size | Smaller | Larger |

### When to Use Context API

| Use Case | Example |
|----------|---------|
| Small applications | Simple projects |
| Values that don't change often | Theme, language, auth user |
| Simple prop drilling | Passing props down 2-3 levels |
| Local sub-tree state | State within a component branch |

### When to Use Redux

| Use Case | Example |
|----------|---------|
| Large applications | Complex UI state |
| Frequent updates | Shopping cart, tabs, filters |
| Complex state | Nested objects |
| Many state slices | Multiple domains to manage |

### Recommendation

| Project Size | Recommendation |
|--------------|----------------|
| Small | Context API + useReducer |
| Medium | Evaluate both options |
| Large | Redux |
