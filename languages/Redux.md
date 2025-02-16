Redux
=====

Redux is useful for handling react application state. Instead of passing state updates between components (via props or callbacks on props), it behaves via action bubbling into reducers, which updates a global state. The end result is that you solely end up with a bunch props being passed in (either from react or redux) instead of this.state.

In a single-page application, you'd start with a static initial state, and populate it either from AJAX or user interactions. If instead hydrating data from the backend, it will go into the initial state in the store.

Main react entrypoint (adds store hooks to all sub-components)
--------------------------------------------------------------

```
<Provider store={store}>
    <CalendarRedux />
</Provider>,
```


Store
-----

```
const today = new Date();
const reducers = combineReducers({
    "calendar": calendarReducer
});
const state = {
    "calendar": {
        "selectedDay": null,
        "currentYear": today.getFullYear(),
        "currentMonth": today.getMonth(),
        "events": []
    }
};

store = createStore(reducers, state);
```


Reducer
-------

```
export function calendarReducer (state = {}, action) {
    switch (action.type) {
    case "INCREMENT_DAY":
        const curMonth = new Date(state.currentYear, state.currentMonth, 1, 0, 0, 0, 0);
        const newMonth = dateFns.addMonths(curMonth, action.value);
        return {...state,
            "currentMonth": newMonth.getMonth(),
            "currentYear": newMonth.getFullYear()
        };
    default:
        return state;
    }
}
```


Component
---------

```
class Calendar extends React.Component {
    ...can refer to this.props.setDay() and this.props.events now...

}


const mapStateToProps = (state, ownProps) => {
    return {
        "events": state.calendar.events,
    };
};

const mapDispatchToProps = (dispatch) => (
    {
        "setDay": (id) => dispatch({"type": "INCREMENT_DAY", "id": id}),
    }
);

export const CalendarRedux = connect(mapStateToProps, mapDispatchToProps)(Calendar);
```
