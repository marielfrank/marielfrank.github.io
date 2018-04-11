---
layout: post
title:      "Signup in React/Redux with JWT authentication"
date:       2018-04-11 14:00:19 -0400
permalink:  signup_in_react_redux_with_jwt_authentication
---

**A two-step (or three-step) process**


ReactJS changes really fast. So fast that by the time you read this, my code itself will probably be outdated. As a matter of fact, even [the Rails gem I used for JWT authentication](https://github.com/nsarno/knock) completely changed its installation instructions over the past couple years. 

But the important thing I want you to glean from this post is that setting up user signup in React with any kind of JWT authentication to an API or database is going to involve two (or three, depending on your needs) completely separate actions:


**1. Create the user**


This part should seem familiar if you've done any other kind of CRUD work in React with an API. In my case, I used Rails API and sent over a `fetch` request using the `'POST'` method. Here's how it works:

I collect the user data through the signup form and submit it through `handleOnSubmit` to the Redux `signup` action. This action is imported as a `prop` by connecting the prop to the Redux store. This could also have been done using `mapDispatchToProps` but I like this way better since it's quite a bit cleaner.

```
// Signup.js

import React from 'react';
import { connect } from 'react-redux';
import { withRouter } from 'react-router-dom';
import { signup } from '../actions/authActions';

class Signup extends React.Component {
    constructor(props){
      super(props);
  
      this.state = {
        username: '',
        email: '', 
        password: ''
      };
    }

    handleOnChange = (e) => {
        const {name, value} = e.target;
        this.setState({
          [name]: value
        });
    }

    handleOnSignup = (e) => {
        e.preventDefault();
        if (this.props.signup(this.state)) {
            this.props.history.push('/puzzles')
         }
    }

    render() {
        return (
            <form onSubmit={this.handleOnSignup}>
                <label htmlFor="email">Username: </label>
                <br />
                <input
                    name="username"
                    id="username"
                    type="text"
                    value={this.state.username}
                    onChange={this.handleOnChange}
                />
                <br /><br />
                <label htmlFor="email">Email: </label>
                <br />
                <input
                    name="email"
                    id="email"
                    type="email"
                    value={this.state.email}
                    onChange={this.handleOnChange}
                />
                <br /><br />
                <label htmlFor="password">Password:</label>
                <br />
                <input
                    name="password"
                    id="password"
                    type="password"
                    value={this.state.password}
                    onChange={this.handleOnChange}
                />
                <br /><br />
                <input
                    type="submit"
                    value="Create Account"
                />
            </form>
        );
    }
}

export default Signup = withRouter(connect(null, {signup})(Signup));
```

Next, I take that data and dispatch a `fetch` `'POST'` request to my Rails API `/users` route, using some headers to ensure that my request is understood as JSON data. If my Rails API is set up correctly, this should hit my `users_controller#create` method and create the user for me as it normally would in any functioning CRUD API. I want to be clear here: **you do NOT need to add any extra code to your API's `create` method for JWT authentication.** 

```
// authActions.js

import fetch from 'isomorphic-fetch';

...

export const signup = (user) => {
    const newUser = user
    return dispatch => {
        
        return fetch(`${API_URL}/users`, {
            method: 'POST',
            body: JSON.stringify({user: user}),
            headers: {
                "Accept":"application/json",
                "Content-Type":"application/json"
            }
        })
            .then(resp => resp.json())
            
						...
						
    }
}
```

You will then get a response in JSON which brings us to step two:

**2. Now authenticate the user that you just created**

I don't know about you, but this part totally blew my mind:

> Creating the user has NOTHING to do with authenticating the user.

So when a user signs up, your app takes their data, persists it to the API, takes the same credentials and THEN authenticates the user by sending an authentication request back to the API again. 

I'll pick up in my `authActions signup` where I left off to show you how this works:

```
.then(resp => resp.json())
            .then(jresp => {
                dispatch(authenticate({email: newUser.email, password: newUser.password}));
            })
```

In the same file, this is the authenticate action I'm dispatching in `signup`. Please note that I'm sending the fetch `'POST'` request to the `/user_token` route because that is where Rails' knock gem does its magic. If you're using another JWT authentication gem or library, you'll have to check where to send your request to.

```
export const authenticate = (credentials) => {
    return dispatch => {
        dispatch({type: 'AUTHENTICATION_REQUEST'}) 
        // this line above just lets my reducer know that we are currently authenticating someone

        return fetch(`${API_URL}/user_token`, { 
            method: 'POST',
            headers: {'Content-Type': 'application/json'},
            body: JSON.stringify({auth: credentials})
        })
            .then(res => res.json())
            .then((response) => {
                const token = response.jwt;
                localStorage.setItem('token', token);
                return getUser(credentials)
            })
            
						...
						
    }
}
```

I take the response data in JSON form and then grab the JWT token and set it to local storage in my browser, letting my app know that someone has been authenticated. In terms of authentication, that's basically all you need to do. However, all I have now is the user credentials and the token. 

***Bonus --> 3. Get your user info back from the API***

Wouldn't it be much nicer to have the user's info in the global state so that I can use their username, email address, and items that belong to them throughout the app's interface? That is why I added a third action in (`getUser`) that then requests that information from the API. Yes, ANOTHER fetch request, this time to a custom method I built in Rails that finds users by email. You'll notice that this method requires authorization to access though, so I pass in my original credentials:

```
const getUser = (credentials) => { 
    const request = new Request('http://localhost:3001/find_user', {
      method: "POST",
      headers: new Headers({
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${localStorage.token}`,
      }),
      body: JSON.stringify({user: credentials})
    })
    
    return fetch(request)
        .then(response => response.json())
        .then(userJson => {return userJson})
        .catch(error => {
            return error;
        });
}
```

Notice that I don't export or dispatch `getUser` -- my React interface doesn't need to know that this action exists. It passes the user info back up into `authenticate` and from there I can dispatch my successfully logged in user and its JWT token from local storage as actions over to my reducer where I can store them safely in my state:

```
// back in authActions.authenticate()

.then((response) => {
                const token = response.jwt;
                localStorage.setItem('token', token);
                return getUser(credentials)
            })
            .then((user) => {
                dispatch(authSuccess(user, localStorage.token))
            })
```

Also in authActions.js, our `authSuccess` function:

```
const authSuccess = (user, token) => {
    return {
        type: 'AUTHENTICATION_SUCCESS',
        user: user,
        token: token
    }
}
```

```
// authReducer.js

const initialState = {
    isAuthenticated: false,
    isAuthenticating: false,
    currentUser: {},
    token: null,
    errors: []
}

export default (state = initialState, action) => {
    switch(action.type) {
		
		...
		
		        case 'AUTHENTICATION_SUCCESS':
            return {
                ...state,
                isAuthenticated: true,
                isAuthenticating: false,
                currentUser: action.user,
                token: action.token
            }
		...
		
		}
}
```

You can check out the rest of my authentication process at [github.com/marielfrank/jigswap](https://github.com/marielfrank/jigswap). 

Happy coding!
