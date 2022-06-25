# What is React context?
---
A [[react-js]] context is a way for [[react-js]] components to have controlled global state. it does this by rendering a context provider as wrapper component and make its value available to its children components rendered inside by using `React.useContext`

```tsx
import * as React from "react"

interface CountexContextProps {
  count: 0;
  setCount: (newCount: number) => void;
}

const counterContext = React.createContext<CounterContextProps>({
  count: 0,
  setCount: () => {}
});

export const useCounterContext = () => React.useContext(counterContext);

export const CounterContextManager: React.FC = ({children}) => {
  const [count, setCount] = React.useState(0);

  const api = React.useMemo(() => {
	return {
	  count,
	  setCount,
	};
  }, []);

  return (
    <counterContext.Provider value={api}>
      {children}
    </counterContext.Provider>
  );
};
```

When you render a `<CounterContextManager>{children}</CounterContextManager>` somewhere in your react tree, anything that is rendered inside that component (the subcomponents as well) will have access to the `api` object we created by using the `useCounterContext` custom hook we defined that is created inside of that manager.

```tsx
import {
  CounterContextManager, 
  useCounterContext
} from "features/counter/context"

const IncrementButton = () => {
  const { setCount, count } = useCounterContext()

  return (
    <button onClick={() => setCount(count + 1)}>Increment</button>
  )
}

const CountLabel = () => {
  const { count } = useCounterContext()

  return (
    <div>Count: {count}</div>
  )
}

// example of how they need to be rendered. 
// it doesn't matter how deep they get rendered
// both <CountLabel /> and <IncrementButton /> will have
// access to the values exposed by the <CounterContextManager />
const Counter = () => {
  return (
    <CounterContextManager>
      <CountLabel />
      <IncrementButton />
    </CounterContextManager>
  )
}
```

This concept can be used to expose functionality to the deeper components in the tree without needing to do [[prop drilling]].
