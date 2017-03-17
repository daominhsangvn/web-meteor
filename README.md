# react-meteor
![build](https://travis-ci.org/ZevenFang/react-meteor.svg?branch=master)

Meteor Reactivity for your React application :)

Looking for a demo? Try to check out [react-meteor-todomvc](https://github.com/ZevenFang/react-meteor-todomvc).

## What is it for ?

The purpose of this library is :
* to set up and maintain a ddp connection with a ddp server, freeing the developer from having to do it on their own.
* be fully compatible with react and help react developers.
* **to match with [Meteor documentation](http://docs.meteor.com/) used with React.**

## Install

    npm i --save react-web-meteor

## Example usage

```javascript

import React, { Component } from 'react';
import Meteor, { createContainer } from 'react-web-meteor';

Meteor.connect('ws://192.168.X.X:3000/websocket');//do this only once

class Todo extends Component {
  renderRow(todo) {
    return (
      <span>{todo.title}</span>
    );
  }
  render() {
    const { settings, todosReady, todos } = this.props;
    return(
      <div>
        <div>{settings.title}</div>
        {!todosReady && <span>Not ready</span>}
        <div>{todos.map(v=>this.renderRow(v))}</div>
      </div>
    )
  }
}

export default createContainer(params=>{
  const handle = Meteor.subscribe('todos');
  Meteor.subscribe('settings');
  return {
    todosReady: handle.ready(),
    settings: Meteor.collection('settings').findOne()
  };
}, Todo)
```

# Connect your components

[Since Meteor 1.3, createContainer is the recommended way to populate your React Components](http://guide.meteor.com/v1.3/react.html#using-createContainer).

## createContainer

 Very similar to getMeteorData but your separate container components from presentational components.

### Example

```javascript
import Meteor, { createContainer } from 'react-web-meteor';


class Orders extends Component {
  render() {
    const { pendingOrders } = this.props;

    //...
    );
  }
}

export default createContainer(params=>{
  return {
    pendingOrders: Meteor.collection('orders').find({status: "pending"}),
  };
}, Orders)
```

# Reactive variables

These variables can be used inside getMeteorData or createContainer. They will be populated into your component if they change.

* [Meteor.subscribe()](http://docs.meteor.com/#/full/meteor_subscribe)
* Meteor.collection(collectionName, options)
  * [.find(selector, options)](http://docs.meteor.com/#/full/find)
  * [.findOne(selector, options)](http://docs.meteor.com/#/full/findone)
* [Meteor.user()](http://docs.meteor.com/#/full/meteor_user)
* [Meteor.userId()](http://docs.meteor.com/#/full/meteor_userid)
* [Meteor.status()](http://docs.meteor.com/#/full/meteor_status)
* [Meteor.loggingIn()](http://docs.meteor.com/#/full/meteor_loggingin)
* [ReactiveDict()](https://atmospherejs.com/meteor/reactive-dict)

# Additionals collection methods

These methods (except update) work offline. That means that elements are correctly updated offline, and when you reconnect to ddp, Meteor calls are taken care of.

* Meteor.collection(collectionName, options)
  * [.insert(doc, callback)](http://docs.meteor.com/#/full/insert)
  * [.update(id, modifier, [options], [callback])](http://docs.meteor.com/#/full/update)
  * [.remove(id, callback(err, countRemoved))](http://docs.meteor.com/#/full/remove)

# API

## Meteor Collections

### Meteor.subscribe
[Meteor.subscribe()](http://docs.meteor.com/#/full/meteor_subscribe) returns an handle. If the component which called subscribe is unmounted, the subscription is automatically canceled.

### Meteor.collection(collectionName, options)
You need pass the `cursoredFind` option when you get your collection if you want to use cursor-like method:

```‍‍‍javascript
Meteor.collection("collectionName", { cursoredFind: true })
```

Or you can simply use `find()` to get an array of documents. The option default to false for backward compatibility. Cursor methods are available to share code more easily between a react app and a standard Meteor app.


## Meteor DDP connection

### Meteor.connect(endpoint, options)

Connect to a DDP server. You only have to do this once in your app.

*Arguments*

- `url` **string** *required*
- `options` **object** Available options are :
  - autoConnect **boolean** [true] whether to establish the connection to the server upon instantiation. When false, one can manually establish the connection with the Meteor.ddp.connect method.
  - autoReconnect **boolean** [true] whether to try to reconnect to the server when the socket connection closes, unless the closing was initiated by a call to the disconnect method.
  - reconnectInterval **number** [10000] the interval in ms between reconnection attempts.

### Meteor.disconnect()

Disconnect from the DDP server.

## Meteor methods

* [Meteor.call](http://docs.meteor.com/#/full/meteor_call)
* [Meteor.loginWithPassword](http://docs.meteor.com/#/full/meteor_loginwithpassword) (Please note that user is auto-resigned in - like in Meteor Web applications.)
* [Meteor.logout](http://docs.meteor.com/#/full/meteor_logout)
* [Meteor.logoutOtherClients](http://docs.meteor.com/#/full/meteor_logoutotherclients)

## Availables packages

###  Convenience packages
Example `import { composeWithTracker } from 'react-web-meteor';``

* EJSON
* Tracker
* composeWithTracker: If you want to use [react-komposer](https://github.com/kadirahq/react-komposer), you can use react-web-meteor compatible composeWithTracker
* Accounts (see below)

### ReactiveDict

See [documentation](https://atmospherejs.com/meteor/reactive-dict).


### Meteor.Accounts

`import { Accounts } from 'react-web-meteor';``

* [Accounts.createUser](http://docs.meteor.com/#/full/accounts_createuser)
* [Accounts.changePassword](http://docs.meteor.com/#/full/accounts_forgotpassword)
* [Accounts.forgotPassword](http://docs.meteor.com/#/full/accounts_changepassword)
* [Accounts.resetPassword](http://docs.meteor.com/#/full/accounts_resetpassword)
* [Accounts.onLogin](http://docs.meteor.com/#/full/accounts_onlogin)
* [Accounts.onLoginFailure](http://docs.meteor.com/#/full/accounts_onloginfailure)

### Meteor.ddp

Once connected to the ddp server, you can access every method available in [ddp.js](https://github.com/mondora/ddp.js/).
* Meteor.ddp.on('connected')
* Meteor.ddp.on('added')
* Meteor.ddp.on('changed')
* ...

# Author

* Zeven ([@ZevenFang](https://github.com/zevenfang))

# Want to help ?

Pull Requests and issues reported are welcome! :)
