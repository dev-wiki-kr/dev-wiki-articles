# useQuery Callback (onSuccess, onError, onSettled) Deprecated

## 1. 개요

TanStack Query의 `useQuery` 훅에서 `onSuccess`, `onError`, `onSettled`와 같은 콜백 함수가 deprecated(사용 중지 예정)된 이유와 그에 따른 변화, 해결책을 다룹니다.

이러한 콜백 함수들은 과거에는 `useQuery`에서 비동기 작업의 성공, 실패, 완료 상태를 처리하는 데 사용되었습니다. 하지만 최신 버전에서는 이러한 콜백이 제거되었고, 대신 React의 `useEffect` 훅을 활용하는 방식으로 전환되었습니다.

## 2. 기존 배경

`useQuery`에서 `onSuccess`, `onError`, `onSettled` 콜백은 쿼리의 상태 변화에 따라 특정 로직을 실행하는 데 사용되었습니다. 예를 들어, 쿼리가 성공적으로 완료되었을 때 특정 작업을 하거나, 오류가 발생했을 때 오류 처리를 할 수 있었습니다.

이 callback은 **직관적**이여 개발자가 비동기 작업의 상태에 따라 다양한 후속 작업을 쉽게 구현할 수 있게 해주었습니다.

```javascript
const { data, error } = useQuery('todos', fetchTodos, {
  onSuccess: () => console.log('Data fetched successfully!'),
  onError: () => console.error('Error fetching data'),
  onSettled: () => console.log('Query has settled'),
})
```

## 3. 변경된 이유

### 3.1 렌더링과 맞지않는 상태 동기화

```jsx
export function useTodos() {
  const [todoCount, setTodoCount] = React.useState(0)
  const { data: todos } = useQuery({
    queryKey: ['todos', 'list'],
    queryFn: fetchTodos,
    onSuccess: (data) => {
      setTodoCount(data.length)
    },
  })

  return { todos, todoCount }
}

function Todos() {
  const { todos, todoCount } = useTodos()
  console.log(todos, todoCount)
  return (
    <div>
      {todos?.length}, {todoCount}
    </div>
  )
}
```

다음과 같은 예시에서 onSuccess에서 업데이트하는 todoCount 상태는 렌더링 사이클에 들어가지 않습니다. onSuccess는 query가 성공하면 즉각적으로 바로 실행하는 callback이기에 useEffect와 같이 렌더링이 끝난 뒤 실행되지 않습니다.

즉, 실제 상태들이 동기화되지 않은 상태로 onSuccess에서 처리되어 예상치 못한 버그를 발생시킬 수 있습니다.

### 3.2 실행되지 않는 callback

```jsx
export function useTodos(filters) {
  const { dispatch } = useDispatch()

  return useQuery({
    queryKey: ['todos', 'list', { filters }],
    queryFn: () => fetchTodos(filters),
    staleTime: 2 * 60 * 1000,
    onSuccess: (data) => {
      dispatch(setTodos(data))
    },
  })
}
```

다음 예시는 redux와 같은 전역 상태관리 라이브러리에 query 데이터를 넣는 예시입니다.

onSuccess같은 경우는 fetch가 성공하면 발생하는 callback이기에 staleTime동안은 추가 fetch를 하지 않는 위의 예시같은 경우에는 onSuccess가 실행되지 않아 `filters` 라는 key 값이 변경될때 예상치 못한 문제가 발생할 수 있습니다.

위의 예제의 흐름은 다음과 같습니다.

1. `filters: {done: true}` 를 넣은 상태로 fetch, onSuccess는 store에 저장
2. `filters: {done: false}`를 넣은 상태로 fetch, onSuccess는 동일하게 store에 저장
3. 다시 `filters: {done: true}`를 넣었지만 staleTime이 지속중이기에 fetch를 하지않고 onSuccess가 실행되지 않으며 이전 `2번` 과정에서의 데이터가 store에 유지됩니다.

다음과 같이 실제로 변경 되어야 할 지점에서 변경이 되지 않는 문제를 겪을 수 있습니다.

## 4. 해결법

### 4.1. 불필요한 상태 사용 피하기

위의 `3.1` 예시에서 onSuccess로 인해 동기화 되지 않는 상태를 참조할 수 있는 문제가 있었는데 이 같은 경우에는 추가 상태를 만들지 않고 `todos`를 그대로 사용하여 해결할 수 있습니다.

```jsx
export function useTodos() {
  const { data: todos } = useQuery({
    queryKey: ['todos', 'list'],
    queryFn: fetchTodos,
  })

  const todoCount = todos?.length ?? 0

  return { todos, todoCount }
}
```

### 4.2. useEffect를 활용

**useEffect는 onSuccess callback과 다르게 렌더링 사이클을 지키기에 컴포넌트의 상태 변화를 일관성 있게 관리할 수 있습니다.**

```jsx
const { data, error, isSuccess, isError } = useQuery('todos', fetchTodos)

useEffect(() => {
  if (isSuccess) {
    console.log('Data fetched successfully!')
  }
  if (isError) {
    console.error('Error fetching data')
  }
}, [isSuccess, isError])
```

## 5. useMutation과의 차이

하지만 `useMutation`의 경우, 여전히 `onSuccess`, `onError`, `onSettled` 콜백이 유지되고 있습니다.

`useQuery`의 callback은 상태에 대한 업데이트가 주 목적이였다면 `useMutation`의 callback은 fetch후 cache invalidation, queryClient의 조작과 같은 작업에 사용되기 때문입니다.

또한 tanstack query의 optimistic update를 위해서도 이는 제거하기 어렵기에 남겨져 있습니다.

```jsx
useMutation({
  mutationFn: updateTodo,
  onMutate: async (newTodo) => {
    await queryClient.cancelQueries({ queryKey: ['todos', newTodo.id] })

    const previousTodo = queryClient.getQueryData(['todos', newTodo.id])
    queryClient.setQueryData(['todos', newTodo.id], newTodo)

    return { previousTodo, newTodo }
  },
  onError: (err, newTodo, context) => {
    queryClient.setQueryData(['todos', context.newTodo.id], context.previousTodo)
  },
  onSettled: (newTodo) => {
    queryClient.invalidateQueries({ queryKey: ['todos', newTodo.id] })
  },
})
```
