# React Template for WAX (UAL)

This template was created to make the use of WAX with React easier.

Please remember to install all needed dependencies beforehand: 
```
> npm i
```
You can start the project by: 
```
> npm run start
```

## Dependencies used
- Dependencies for WAX
    - @eosdacio/ual-wax
    - ual-anchor
    - ual-plainjs-renderer
    - anchor-link
- Dependencies for React routes
    - react-router-dom

- Global state dependencies
    - redux
    - react-redux
    - @reduxjs/toolkit

- Style and design dependencies
    - bootstrap
    - glamor

- Help dependencies
    - lodash

## Key files

- **UserService.js**: A file needed to manage the user's login, UAL configuration and different login methods. It also has a certain mode that lets us know the user's status (**isLogged()**) with the answers **true** or **false** if the user has previously logged in.

    **Remember** to comment and uncomment as needed, depending on wether you will be working with mainnet or testnet.

    When the user logs in, this data will be saved in the app's global state thanks to Redux and @reduxjs/toolkit (we will also save an **isLogged** in order to maintain a live actualization in React).

- **GlobalState/**: In this folder we will keep our configuration and **store** from **redux**, so we can save and manage the user's data in a global state.

- **App.js**: With this file we can manage the web's route system.

-  **index.js**: This file initiates **UserService** (UserService.init()) and also contains the <Provider> for **store** from **redux**.

- **pages**: In this folder we will save our web's pages. **Remember to configure new pages' routes in your App.js**.

- **components**: In this folder we will keep our app's components. For this example we only have **Menu.jsx**, a component for our menu, which will help us redirect the user when they log in.

## File Menu.jsx
The file **components/Menu.jsx** is a component from our app or web's menu, which has four tabs: Main, Home, Page2, Login/Logout.

As we can see, we have two tabs which are disabled and we are not allowed to access them. These are: **Home** and **Page2**. To achieve this, we will simply check out if the user has logged in or not, thanks to **UserState.isLogged** (in the redux state), and we will show or lock them with any **CSS** style.

As for the Login/Logout tab, we will show one or the other depending on the user's state. This is easily ckeched by opening the file, but you will probably see something like this: 
```jsx
 {
    !UserState.isLogged &&
    <button className="..." onClick={handleLogin}><img .../> Login</button>
}
{
    UserState.isLogged &&
    <Link to="/" className="..." onClick={onHandleLogout}>Logout <img .../></Link>
}
```
## Login system

The system to start login is located in **components/Menu.jsx**. Once we click on the login button in the menu, this will trigger **handleLogin** and at the same time will activate the **UserService.login()** fuction. In this function it is possible to add an anonymous function as a callback, and the answer will be delivered once we log in. This callback will allow us to check if the user has logged in, if they have.
If that is the case, we will be redirected to a page (**/home, in this case**). Otherwise, it will log out in order to clear data.

```jsx
const handleLogin = () => {
    UserService.login(() => {
        if (UserService.isLogged()) {
            locationHistory.push('/home');
        } else {
            dispatch(setPlayerLogout());
        }
    });
}
```

## React routes' protection

Routes must be protected. For that, we will need to create a component called **ProtectedRouter.jsx**. We have created one for this example, and its function will be to ckeck the route we're in and to show us the user's state so we can know if they have logged in or not (**UserService.isLogged()**). If the user has not logged in, it will take the user from this route and redirect them to another one we have configured previously, which in this case leads to **/login**.

In order to use this component, we will simply open our **App.js**, where we will substitute <Route> for our <ProtectedRouter>. You will see this if you open **App.js**: 

```jsx
function App() {
  return (
    <div className="App">
      <BrowserRouter>
        <Menu />
        <Switch>
          <Route exact path="/" component={LandingPage} />
          <ProtectedRoute exact path="/page2" component={Page2} />
          <ProtectedRoute exact path="/home" component={Home} />
          <Redirect to="/"  />
        </Switch>
      </BrowserRouter>
    </div>
  );
}
```

## Send WAX

You can see an example of how to send items from WAX in **pages/Page2.jsx**. The **UserService** saves the session and we simply have to access this session and sing any transaction. The code you will see in this example looks like this: 

```js
UserService.session.signTransaction(
    {
        actions: [{
            account: 'eosio.token',
            name: 'transfer',
            authorization: [{
                actor: UserService.authName,
                permission: 'active'
            }],
            data: {
                from: UserService.authName,
                to: '3dkrenderwax',
                quantity: '1.00000000 WAX',
                memo: 'This works!'
            }
        }]
    },
    {
        blocksBehind: 3,
        expireSeconds: 30
    }

).then((response) => {
    if(response.status === 'executed') {
        UserService.getBalance();
    }
});
```
