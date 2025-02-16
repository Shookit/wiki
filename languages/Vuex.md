Vuex
====

Mutations vs actions
--------------------

Mutations are the only way to modify state.
Mutations don't care about business logic, just about "state".
Mutations are synchronous.
Actions are business logic.
Actions can dispatch more than 1 mutation at a time (or as necessary) to create the business logic.


Store properties
----------------

### State

Defines the state of the application. This can be mutated via mutations or actions.

```
state: {
    someStateVariable: []
}
```


### Getters

Used to create views over the state. e.g., flatten the canonical state for an outgoing request.

```
getters: {
    reverseArray (state) {
        return state.targetArray.reverse
    }
}
```


### Mutations

Modifies state of application.

```
mutations: {
    increment (state) {
        state.count++
    }
}
```

### Actions

Contains business logic, and is commonly referenced by components.
May dispatch mutations in order to mutate application state.

```
actions: {
    someAction (store) {
        store.dispatch('increment')
    }
}
```


### Modules

This is used to modularize the store, rather than having a single massive application.

```
import someModule from '@/store/modules/someModule'
export default new Vuex.Store({
  modules: {
    someModule: someModule
  }
})
```

