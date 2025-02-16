Vue
===

General
-------

- Initialize new vue webpack apps via `vue-cli`
- Views vs components (views are routable components)
- Vue uses one-way data binding between components. Since objects and arrays(?) are passed by reference, these can be accidentally mutated. Use computed components instead of using prop objects directly.


State
-----

Don't set state properties. Mutate them.

Only use:
- push
- pop
- shift
- unshift
- splice
- sort
- reverse


Vue component properties
------------------------

### Props

Used to pass around "arguments" between components.

```
# Just use `var` for static props
<ChildComponent message='hey' />

# Prepend with a colon for dynamic props (i.e., variables)
<ChildComponent :sets=sets />
```


### Methods

Component-local functions.


### Computed

Can be referenced by the template. Computed properties are cached based on their dependencie, but only on 'reactive dependencies'.

```
computed: {
    someComputedValue () {
        return `hello world ${name}`
    }
}
```
