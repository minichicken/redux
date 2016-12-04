# Actions

First, let's define some actions.

**Actions(액션)** are payloads of information that send data from your application to your store. They are the *only* source of information for the store. You send them to the store using
[`store.dispatch()`](../api/Store.md#dispatch).
액션은 당신의 어플리케이션에서 store까지 보내는 데이터에 대한 정보를 적재합니다. 액션은 store의 정보의 원천이 된다.
당신은 store.dispatch()를 사용하여 store 에서 액션을 보낼수 있다.

    
Here's an example action which represents adding a new todo item:
여기 new todo item 을 추가하여 나타내는 예가 있다.

```js
const ADD_TODO = 'ADD_TODO'
```

```js
{
  type: ADD_TODO,
  text: 'Build my first Redux app'
}
```

Actions are plain JavaScript objects. Actions must have a `type` property that indicates the type of action being performed. Types should typically be defined as string constants. Once your app is large enough, you may want to move them into a separate module.

액션들을 순수한 자바스크립트 객체들이다. 액션들은 동작되는 행위에 대한 유형을 나타내는 'type'이라는 속성을 가져야 한다.
Types은 일바적으로 문자열 상수로 정의할수 있다. 어플리케이션이 확장되려고 할때, 분리된 모듈로 액션을 이동시켜야 합니다.

```js
import { ADD_TODO, REMOVE_TODO } from '../actionTypes'
```

>##### Note on Boilerplate  Boilerplate code에 주의하라

>You don't have to define action type constants in a separate file, or even to define them at all. For a small project, it might be easier to just use string literals for action types. However, there are some benefits to explicitly declaring constants in larger codebases. Read [Reducing Boilerplate](../recipes/ReducingBoilerplate.md) for more practical tips on keeping your codebase clean. 당신은 분리된 파일로 액션 type 상수들을 정의하면 안되고 , 전혀 그렇게 하지 않아도 안된다. 작은 프로젝트라면, 단지 쉽게 리터럴로 액션타입을 쉽게 사용할수 있다. 그러나 , 커다란 코드틀에서는 상수를 정확하게 정의하는 것은 이점이 있다. 당신의 코트드틀에 대한 더 실질적인 팁에 대해서 읽어 보세요  [Reducing Boilerplate](../recipes/ReducingBoilerplate.md)  


Other than `type`, the structure of an action object is really up to you. If you're interested, check out [Flux Standard Action](https://github.com/acdlite/flux-standard-action) for recommendations on how actions could be constructed.
'type' 외에 ,액션 객체의 구조는 당신에게 달렸다. 당신이 관심이 있다면, [Flux Standard(Flux 기본 액션) Action](https://github.com/acdlite/flux-standard-action) 어떻게 액션을 생성하는지에 대한 조언들을 확인해보세요.  

We'll add one more action type to describe a user ticking off a todo as completed. We refer to a particular todo by `index` because we store them in an array. In a real app, it is wiser to generate a unique ID every time something new is created. 우리는  todo 가 완료되면 유저가 todo 가 완료됨을 체크하는 것을 표현하는 한개 이상의 액션 유형을 추가할수 있다. 우리는 특히 배열에 우리가 액션 타입을 저장했기 때문에 'index' 로써 특히 todo 를 참조할수 있다. 실제 어플리케이션에서는, 매번 새로운 todo가 생성될때마다 새로운 유니크 Id 키를 생성하는데에 알맞다.

```js
{
  type: TOGGLE_TODO,
  index: 5
}
```

It's a good idea to pass as little data in each action as possible. For example, it's better to pass `index` than the whole todo object. 각각의 액션에 작은 데이터를 통과시키는 것을 좋은 생각이다. 예를들면, 전체 todo Object 보다는 'index' 통과 시키는것이 낫다.

Finally, we'll add one more action type for changing the currently visible todos.마지막으로, 현재 보이는 todo 들과 연관됨에 대해서 나타내는 더 많은 액션 유형을 추가한다.

```js
{
  type: SET_VISIBILITY_FILTER,
  filter: SHOW_COMPLETED
}
```

## Action Creators

**Action creators** are exactly that—functions that create actions. It's easy to conflate the terms “action” and “action creator,” so do your best to use the proper term.
**Action creators** 는 액션을 생성하는 함수이다. 이것은 쉽게 액션 그리고 액션 생성자 구문를 합질수 있다, 그래서 적절한 구문으로 사용하는 좋으 방법을 써라.

In Redux action creators simply return an action: Redux에서 액션 생성자는 간단하게 액션을 리턴한다.

```js
function addTodo(text) {
  return {
    type: ADD_TODO,
    text
  }
}
```

This makes them portable and easy to test.이것은 변형하기 쉽고, 테스트하기 쉽다.

In [traditional Flux](http://facebook.github.io/flux), action creators often trigger a dispatch when invoked, like so: 전통적인 Flux 에서는, 액션 생성자들은 종종 invoked 할때, dispatch에 호출한다.

```js
function addTodoWithDispatch(text) {
  const action = {
    type: ADD_TODO,
    text
  }
  dispatch(action)
}
```

In Redux this is *not* the case.  Redux 안에서는 이 케이스는 아니다.
Instead, to actually initiate a dispatch, pass the result to the `dispatch()` function: 대신에 , 실재로 dispatch를 초기화 할때, `dispatch()` 함수에 해당 결과를 전달한다.

```js
dispatch(addTodo(text))
dispatch(completeTodo(index))
```

Alternatively, you can create a **bound action creator** that automatically dispatches: 대체적으로 , 우리는 자동적으로 보내는 **bound action creator** 를 생성할수 있다.

```js
const boundAddTodo = (text) => dispatch(addTodo(text))
const boundCompleteTodo = (index) => dispatch(completeTodo(index))
```

Now you'll be able to call them directly:
지금 우리는 직접적으로 호출할수 있다.
```
boundAddTodo(text)
boundCompleteTodo(index)
```

The `dispatch()` function can be accessed directly from the store as [`store.dispatch()`](../api/Store.md#dispatch), but more likely you'll access it using a helper like [react-redux](http://github.com/gaearon/react-redux)'s `connect()`. You can use [`bindActionCreators()`](../api/bindActionCreators.md) to automatically bind many action creators to a `dispatch()` function.


`dispatch()` 함수는 직접적으로 'store.dispatch()' 로서 스토어  로부터 직접적으로 접근할수 있지만, react-redux의 `connect()` 같은 헬퍼를 사용하여 접근 할수있다.

Action creators can also be asynchronous and have side-effects. You can read about [async actions](../advanced/AsyncActions.md) in the [advanced tutorial](../advanced/README.md) to learn how to handle AJAX responses and compose action creators into async control flow. Don't skip ahead to async actions until you've completed the basics tutorial, as it covers other important concepts that are prerequisite for the advanced tutorial and async actions.Action creators는 비동기적이고 , side-effects를 가진다.우리는 어떻게 ajax 응답을에 대해서 다루고 동기 컨트롤 흐름에서 생성자를 어떻게 조합 시키는지 대한 async 액션 대해서 the [advanced tutorial](../advanced/README.md)에서 읽을 수 있다.

## Source Code

### `actions.js`

```js
/*
 * action types
 */

export const ADD_TODO = 'ADD_TODO'
export const TOGGLE_TODO = 'TOGGLE_TODO'
export const SET_VISIBILITY_FILTER = 'SET_VISIBILITY_FILTER'

/*
 * other constants
 */

export const VisibilityFilters = {
  SHOW_ALL: 'SHOW_ALL',
  SHOW_COMPLETED: 'SHOW_COMPLETED',
  SHOW_ACTIVE: 'SHOW_ACTIVE'
}

/*
 * action creators
 */

export function addTodo(text) {
  return { type: ADD_TODO, text }
}

export function toggleTodo(index) {
  return { type: TOGGLE_TODO, index }
}

export function setVisibilityFilter(filter) {
  return { type: SET_VISIBILITY_FILTER, filter }
}
```

## Next Steps

Now let's [define some reducers](Reducers.md) to specify how the state updates when you dispatch these actions!
