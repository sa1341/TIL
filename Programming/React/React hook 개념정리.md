# React Hooks

Hooks는 React v16.8에 새로 도입된 기능으로서, 함수형 컴포넌트에서도 상태 관리를 할 수 있는 useState, 그리고 렌더링 직후 작업을 설정하는 useEffect 등의 기능등을 제공하여 기존의 함수형 컴포넌트에서 할 수 없었던 다양한 작업을 할 수 있게 해줍니다.

## useState

useState는 가장 기본적인 Hook으로서, 함수형 컴포넌트에서도 가변적인 상태를 지니고 있을 수 있게 해줍니다. 만약에 함수형 컴포넌트에서 상태를 관리해야 되는 일이 발생하면 이 Hook을 사용하시면 됩니다.

한번 이 기능을 사용해서 숫자 카운터를 구현해보았습니다.

### Counter.js

```javascript
import React, { useState } from 'react';

const Counter = () => {
    const [ value, setValue ] = useState(0);
    return (
        <div>
          <p>
            현재 카운터 값은 <b>{value}</b> 입니다.  
          </p>
          <button onClick={() => setValue(value + 1)}>+1</button>
          <button onClick={() => setValue(value - 1)}>-1</button>
        </div>
    );
};

export default Counter;
```

useState를 사용할 땐 코드의 상단에 import 구문을 통하여 불러오고, 비구조화 할당 문법으로 사용합니다.

```javascript
const [ value, setValue ] = useState(0);
```

이 함수의 파라미터에는 상태의 기본값을 넣어줍니다. 여기서는 현재 0을 넣어줬는데, 결국 카운터의 기본 값을 0으로 설정하겠다는 의미입니다. 이 함수가 호출되고 나면 배열을 반환하는데요, 그 배열의 첫번째 원소는 상태 값이고, 두번째 원소는 상태를 설정하는 함수 입니다. 이 함수에 파라미터를 넣어서 호출하게 되면 전달받은 파라미터로 값이 바뀌게 되고 컴포넌트는 정상적으로 리렌더링 됩니다.

App 컴포넌트를 열어서 기존에 보여주고 있던 내용을 다 지우고 Counter 컴포넌트를 렌더링하였습니다.

### App.js

```javascript
import React, { useState } from 'react';

render() {
    return (
        <div>
          <Counter/>
        </div>
    );
};
```

### 실행 결과

![image](https://user-images.githubusercontent.com/22395934/104813857-274dc180-584f-11eb-953f-c03d8346f26e.png)


이렇게 useState를 사용하면 함수형 컴포넌트에서 상태 관리를 위하여 굳이 클래스 형태로 변환 할 필요가 없어서 매우 편리합니다.

### useState를 여러번 사용하기

하나의 useState 함수는 하나의 상태 값만 관리할 수 있기 때문에 만약 컴포넌트에서 관리해야 할 상태가 여러 개라면 useState를 여러번 사용하면 됩니다.

```javascript
import React, { useState } from 'react';

const Info = () => {

    const [ name, setName ] = useState('');
    const [ nickname, setNickname ] = useState('');

    const onChangeName = (e) => {
        const { value } = e.target;
        setName(value);
    }

    const onChangeNickname = (e) => {
        const { value } = e.target;
        setNickname(value);
    }


    return (
        <div>
            <div>
                <input value={name} onChange={onChangeName} />
                <input value={nickname} onChange={onChangeNickname} />
            </div>
            <div>
                <div>
                    <b>이름:</b>{name}
                </div>
                <div>
                    <b>닉네임:</b>{nickname}
                </div>
            </div>
        </div>
    );
};

export default Info;
```

그 다음에 App 컴포넌트에서 이 컴포넌트를 렌더링 하면 됩니다.

```javascript
import React from 'react';
import Info from './Info';

const App = () => {
  return <Info />;
};

export default App;
```

### 실행 결과

![image](https://user-images.githubusercontent.com/22395934/104814252-3afa2780-5851-11eb-9d61-8ae78ed8f8fe.png)

관리할 상태가 여러개인 경우에도, useState로 편리하게 관리할 수 있습니다.

### useEffect

useEffect는 리액트 컴포넌트가 렌더링 될 때마다 특정 작업을 수행하도록 설정 할 수 있는 Hook 입니다.

클래스형 컴포넌트의 ComponentDidMount와 componentDidUpdate를 합친 형태로 보아도 무방합니다.

위에서 작성한 기존에 만들었던 Info 컴포넌트에 useEffect를 적용해보겠습니다.


```javascript
import React, { useState, useEffect } from 'react';

const Info = () => {

    const [ name, setName ] = useState('');
    const [ nickname, setNickname ] = useState('');
    useEffect(() => {
        console.log('렌더링이 완료 돠었습니다.');
        console.log({
            name,
            nickname
        });
    });

    const onChangeName = (e) => {
        const { value } = e.target;
        setName(value);
    }

    const onChangeNickname = (e) => {
        const { value } = e.target;
        setNickname(value);
    }


    return (
        <div>
            <div>
                <input value={name} onChange={onChangeName} />
                <input value={nickname} onChange={onChangeNickname} />
            </div>
            <div>
                <div>
                    <b>이름:</b>{name}
                </div>
                <div>
                    <b>닉네임:</b>{nickname}
                </div>
            </div>
        </div>
    );
};

export default Info;
```

### 실행 결과

![image](https://user-images.githubusercontent.com/22395934/104814705-ca083f00-5853-11eb-8fe6-8fd321ff67c8.png)


## 마운트 될 때만 실행하고 싶을 때

만약 useEffect에서 설정한 함수가 컴포넌트가 화면에 가장 처음 렌더링 될 때만 실행되고 업데이트 할 경우에는 실행 할 필요가 없는 경우엔 함수의 두번째 파라미터로 비어있는 배열을 넣어주면 됩니다.

```javascript
useEffect(() => {
    console.log('렌더링이 완료 돠었습니다.');
    console.log({
        name,
        nickname
    });
}, []);
```

코드를 수정하고 다시 브라우저를 열어보면 컴포넌트가 처음 나타날 때만 콘솔에 문구가 나타나고 그 이후에는 나타나지 않을 것입니다.

## 특정 값이 업데이트 될 때만 실행하고 싶을 때

useEffect를 사용할 때 특정 값이 변경이 될 때만 호출하게 하고 싶을 경우도 있을 것입니다. 만약 클래스형 컴포넌트라면 다음과 같이 작성합니다.

```javascript
componentDidUpdate(prevProps, prevState) {
    if (prevProps.value !== this.props.value) {
        doSomething();
    }
}
```

위 코드에서 props 안에 들어있는 value 값이 바뀔 때에만 특정 작업을 수행하도록 하였습니다. 만약 이러한 작업을 useEffect에서 해야한다면 어떻게 해야 할까요?

바로, useEffect의 두번쨰 파라미터로 전달되는 배열 안에 검사하고 싶은 값을 넣어주면 됩니다.

한번 Info 컴포넌트의 useEffect 부분을 수정해보겠습니다.

```javascript
 useEffect(() => {
        console.log('렌더링이 완료 돠었습니다.');
        console.log(name);
}, [name]);
```

배열 안에는 useState를 통해 관리하고 있는 상태를 넣어줘도 되고, props로 전달받은 값을 넣어주어도 됩니다.

### 실행 결과

![image](https://user-images.githubusercontent.com/22395934/104814965-0d16e200-5855-11eb-9a4f-325cb3acf9bd.png)


## 뒷정리 하기

useEffect는 기본적으로 렌더링 되고난 직후마다 실행되며, 두번째 파라미터 배열에 무엇을 넣느냐에 따라 실행되는 조건이 달라집니다.

만약 컴포넌트가 언마운트 되기 전이나, 업데이트 되기 직전에 어떠한 작업을 수행하고 싶다면 useEffect에서 뒷 정리(cleanup) 함수를 반환해주어야 합니다.

info 컴포넌트의 useEffect 부분을 다음과 같이 수정해보세요.

### Info.js

```javascript
import React, { useState, useEffect } from "react";

const Info = () => {
  const [name, setName] = useState("");
  const [nickname, setNickname] = useState("");
  useEffect(() => {
    console.log("effect");
    console.log(name);
    return () => {
      console.log("cleanup");
      console.log(name);
    };
  });

  const onChangeName = (e) => {
    const { value } = e.target;
    setName(value);
  };


  const onChangeNickname = (e) => {
    const { value } = e.target;
    setNickname(value);
  };

  return (
    <div>
      <div>
        <input value={name} onChange={onChangeName} />
        <input value={nickname} onChange={onChangeNickname} />
      </div>
      <div>
        <div>
          <b>이름:</b>
          {name}
        </div>
        <div>
          <b>닉네임:</b>
          {nickname}
        </div>
      </div>
    </div>
  );
};

export default Info;
```

### App.js

```javascript
import React, { useState } from 'react';
import Info from './Info';

const App = () => {
  const [visible, setVisible] = useState(false);
  return (
    <div>
      <button
        onClick={() => {
          setVisible(!visible);
        }}
      >
        {visible ? '숨기기' : '보이기'}
      </button>
      <hr />
      {visible && <Info />}
    </div>
  );
};

export default App;
```

컴포넌트가 나타날 때 콘솔에 effect가 보이고, 사라질 때 cleanup이 보여지게 됩니다.
그 다음엔, 한번 인풋에 이름을 적어보고 콘솔을 보면 렌더링이 될 때마다 뒷정리 함수가 계속 보여지고 있는 것을 확인할 수 있습니다. 그리고, 뒷정리 함수가 호출될 때에는 업데이트 되기 직전의 값을 보여주고 있습니다. 만약에 , 오직 언마운트 될 때만 뒷정리 함수를 호출하고 싶다면 useEffect 함수의 두번째 파라미터에 비어있는 배열을 넣으면 됩니다.

```javascript
useEffect(() => {
    console.log("effect");
    console.log(name);
    return () => {
      console.log("cleanup");
      console.log(name);
    };
},[]);
```

## useContext

이 Hook을 사용하면 함수형 컴포넌트에서 Context를 보다 더 쉽게 사용 할 수 있습니다.
ContextSample.js라는 컴포넌트를 만들어보겠습니다.

### ContextSample.js

```javascript
import React, { createContext, useContext } from 'react';

const themeContext = createContext('black');
const ContextSample = () => {

    const theme = useContext(themeContext);
    const style = {
        width: '24px',
        height: '24px',
        background: theme
    };
    return (
        <div style={style} />
    );
};

export default ContextSample;
```

### App.js

```javascript
import React from 'react';
import ContextSample from './ContextSample';

const App = () => {
  return <ContextSample />;
};

export default App;
```

## useReducer

useReducer는 useState보다 컴포넌트에서 더 다양한 상황에 따라 다양한 상태를 다른 값으로 업데이트 해주고 싶을 때 사용하는 Hook 입니다. 리듀서(reducer)라는 개념은 Redux를 배웠으면 이해가 더 쉽습니다. 

리듀서는 현재 상태와, 업데이트를 위해 필요한 정보를 담은 액션 값을 전달 받아 새로운 상태를 반환하는 함수 입니다. 리듀서 함수에서 새로운 상태를 만들 때는 꼭 불변성을 지켜주어야 합니다.

```javascript
function reducer(state, action) {
    return {...}; // 불변성을 지키면서 업데이트한 새로운 상태를 반환합니다.
}
```

액션 값은 주로 다음과 같은 형태로 이루어져있습니다.

```javascript
{
    type: 'INCREMENT',
    // 다른 값이 필요하다면, 추가적으로 들어갑니다.
}
```

Redux에서는 액션 객체에는 어떤 액션인지 알려주는 type 필드가 꼭 있어야 하지만, useReducer에서 사용하는 액션 객체는 곡 type을 지니고 있을 필요가 없습니다. 심지어, 객체가 아니라 문자열이나, 숫자여도 상관이 없습니다.


## 카운터 구현하기

먼저, 기존의 Counter 컴포넌트를 useReducer를 사용하여 다시 구현해보겠습니다.

```javascript
import React, { useReducer } from 'react';

function reducer(state, action) {
  // action.type에 따라 다른 작업 수행
  switch(action.type) {
    case 'INCREMENT':
      return { value: state.value + 1 };
    case 'DECREMENT':
      return { value: state.value - 1 };
    default:
      return state;
  }
}

const Counter = () => {
  const [state, dispatch] = useReducer(reducer, { value: 0 });
  
  return (
    <div>
      <p>
        현재 카운터 값은 <b>{state.value}</b> 입니다.
      </p>
      <button onClick={() => dispatch({ type: 'INCREMENT'})}>+1</button>
      <button onClick={() => dispatch({ type: 'DECREMENT'})}>-1</button>
    </div>
  );
};

export default Counter;
```

useReducer의 첫 번째 파라미터로는 리듀서 함수, 그리고 두 번째 파라미터는 해당 리듀서의 기본 값을 넣어줍니다. 이 Hook을 사용 했을 때에는 state 값과 dispatch 함수를 받아오게 됩니다. 여기서 state는 현재 가르키고 있는 상태이고, dispatch는 액션을 발생시키는 함수입니다. dispatch(action)와 같은 형태로, 함수 안에 파라미터로 액션 값을 넣어주면 리듀서 함수가 호출되는 구조입니다.

useReducer을 사용했을 때의 가장 큰 장점은 컴포넌트 업데이트 로직을 컴포넌트 바깥으로 빼낼 수 있다는 점입니다. 


### App.js

```javascript
import React from 'react';
import Counter from './Counter';

const App = () => {
  return <Counter />;
};

export default App;
```

## Input 상태 고나리하기

이번에는 useReducer를 사용하여 Info 컴포넌트에서 인풋 상태를 관리해보겠습니다.

기존에는 인풋이 여러개여서 useState를 여러번 사용했는데요, useReducer를 사용한다면 우리가 기존에 클래스형 컴포넌트에서 input 태그에 name 값을 할당하고, e.target.name를 참조하여 setState를 해준것과 유사한 방식으로 작업을 처리 할 수 있습니다.

### Info.js

```javascript
import React, { useReducer } from "react";


function reducer(state, action) {
  return {
    ...state,
    [action.name]: action.value
  }
}

const Info = () => {
 
  const [ state, dispatch ] = useReducer(reducer, {
    name: '',
    nickname: ''
  });

  const { name, nickname} = state;
  const onChange = (e) => {
    dispatch(e.target);
  };

  return (
    <div>
      <div>
        <input name="name" value={name} onChange={onChange} />
        <input name="nickname" value={nickname} onChange={onChange} />
      </div>
      <div>
        <div>
          <b>이름:</b>
          {name}
        </div>
        <div>
          <b>닉네임:</b>
          {nickname}
        </div>
      </div>
    </div>
  );
};

export default Info;
```

useReducer에서의 액션은 어떤 값이 되어도 됩니다. 이벤트 객체가 지니고 있는 e.target 값 자체를 액션 값으로 사용하였습니다.

이런 식으로 인풋을 관리하면, 아무리 인풋의 개수가 많아져도 코드를 짧고 깔끔하게 유지 할 수 있습니다.

### App.js

```javascript
import React from "react";
import Counter from './Counter';
import Info from './Info';

const App = () => {
  return (
    <div>
      <Counter/>
      <Info />
    </div>
  );
};

export default App;
```

## useMemo

useMemo를 사용하면 함수형 컴포넌트 내부에서 발생하는 연산을 최적화 할 수 있습니다. 먼저 리스트에 숫자들을 추가하면 해당 숫자들의 평균을 나타내는 함수형 컴포넌트를 작성해보겠습니다.

### Average.js

```javascript
import React, { useState } from 'react';

const getAverage = numbers => {
  console.log('평균 값 계산 중....');
  
  if (numbers.length === 0) return 0;
  const sum = numbers.reduce((a, b) => a + b);
  return sum / numbers.length;
}

const Average = () => {

  const [ list, setList ] = useState([]);
  const [ number, setNumber ] = useState('');

  const onChange = (e) => {
    const { value } = e.target;
    setNumber(value);
  }

  const onInsert = () => {
    const nextList = list.concat(parseInt(number));
    setList(nextList);
    setNumber('');
  }

  return (
    <div>
      <input value={number} onChange={onChange} />
      <button onClick={onInsert}>등록</button>
      <ul>
    
      </ul>
      <div>
        <b>평균 값:</b>{getAverage(list)}
      </div>
    </div>
  );
};

export default Average;
```

### App.js

```javascript
import React from "react";
import Average from './Average';

const App = () => {
  return (
    <div>
      <Average />
    </div>
  );
};

export default App;
```


reduce()는 빈 요소를 제외하고 배열 내에 존재하는 각 요소에 대해 callback 함수를 한 번씩 실행하는데, 콜백 함수는 다음의 네 인수를 받습니다. 1. accumulator, currentValue, currentIndex, array 콜백이 최초 호출 때 accumulator와 currentValue는 다음 두 가지 값 중 하나를 가질 수 있습니다. 만약 reduce() 함수 호출에서 initialValue를 제공한 경우, accumulator는 initialValue와 같고 currentValue는 첫 번째 값과 같습니다. initialValue를 제공하지 않는다면, accumulator는 첫 번째 값과 같고 currentValue는 두 번째와 같습니다.


>> 참고: initialValue를 제공하지 않으면, reduce()는 인덱스 1부터 시작해 콜백 함수를 실행하고 첫 번째 인덱스는 건너 뜁니다. initialValue를 제공하면 인덱스 0에서 시작합니다.

### 실행 결과

![image](https://user-images.githubusercontent.com/22395934/104919038-fa2d1a80-59d8-11eb-8a63-b15d09a756d4.png)

평균 값은 잘보여지고 있는데, 숫자를 등록할 때 뿐만 아니라 인풋 내용이 수정 될 때도 우리가 만든 getAverage 함수가 호출되고 있는 것을 확인 할 수 있습니다. 인풋 내용이 바뀔 땐 평균 값을 다시 계산할 필요가 없는데, 이렇게 렌더링 할 때마다 계산을 하는 것은 낭비입니다.

useMemo Hook을 사용하면 이러한 작업을 최적화 할 수 있습니다. 렌더링 하는 과정에서 특정 값이 바뀌었을 때만 연산을 실행하고 만약에 원하는 값이 바뀐 것이 아니라면 이전에 연산했던 결과를 다시 사용하는 방식입니다.

코드를 다음과 같이 수정해보겠습니다.

```javascript
import React, { useState, useMemo } from 'react';

const getAverage = numbers => {
  console.log('평균 값 계산 중....');
  
  if (numbers.length === 0) return 0;
  const sum = numbers.reduce((a, b) => a + b);
  return sum / numbers.length;
}

const Average = () => {

  const [ list, setList ] = useState([]);
  const [ number, setNumber ] = useState('');

  const onChange = (e) => {
    const { value } = e.target;
    setNumber(value);
  }

  const onInsert = () => {
    const nextList = list.concat(parseInt(number));
    setList(nextList);
    setNumber('');
  }

  const avg = useMemo(() => getAverage(list), [list]);

  return (
    <div>
      <input value={number} onChange={onChange} />
      <button onClick={onInsert}>등록</button>
      <ul>
        {
          list.map((value, index) => 
            <li key={index}>{value}</li>
          )
        }
      </ul>
      <div>
        <b>평균 값:</b>{avg}
      </div>
    </div>
  );
};

export default Average;
```

이제 list 배열의 내용이 바뀔 때에만 getAverage 함수가 호출됩니다.


## useCallback

useCallback은 useMemo와 상당히 비슷한 함수입니다. 주로 렌더링 성능을 최적화해야 하는 상황에서 사용하는데요, 이 Hook을 사용하면 이벤트 핸들러 함수를 필요할 때만 생성 할 수 있습니다.
우리가 방금 구현한 Average 컴포넌트를 보면, onChange와 onInsert라는 함수를 선언해주었습니다. 이렇게 선언을 하게 되면 컴포넌트가 리렌더링 될 때마다 이 함수들이 새로 생성됩니다. 대부분의 경우에는 이러한 방식이 문제가 되지 않지만, 컴포넌트의 렌더링이 자주 발생하거나, 렌더링 해야 할 컴포넌트의 개수가 많아진다면 이 부분을 최적화 해 줄 수 있습니다.

useCallback을 사용하는 예제를 보겠습니다.

```javascript
import React, { useState, useMemo, useCallback } from 'react';

const getAverage = numbers => {
  console.log('평균 값 계산 중....');
  
  if (numbers.length === 0) return 0;
  const sum = numbers.reduce((a, b) => a + b);
  return sum / numbers.length;
}

const Average = () => {

  const [ list, setList ] = useState([]);
  const [ number, setNumber ] = useState('');

  const onChange = useCallback(e => {
    const { value } = e.target;
    setNumber(value);
  }, []);

  const onInsert = useCallback(e => {
    console.log('onInsert function created');
    const nextList = list.concat(parseInt(number));
    setList(nextList);
    setNumber('');
  },[number, list]);

  const avg = useMemo(() => getAverage(list), [list]);

  return (
    <div>
      <input value={number} onChange={onChange} />
      <button onClick={onInsert}>등록</button>
      <ul>
        {
          list.map((value, index) => 
            <li key={index}>{value}</li>
          )
        }
      </ul>
      <div>
        <b>평균 값:</b>{avg}
      </div>
    </div>
  );
};

export default Average;
```

useCallback의 첫 번째 파라미터에는 우리가 생성해주고 싶은 함수를 넣어주고, 두 번째 파라미터에는 배열을 넣어주면 되는데 이 배열에는 어떤 값이 바뀌었을 때 함수를 새로 생성해주어야 하는지 명시해주어야 합니다.

만약애 onChange처럼 비어있는 배열을 넣게 되면 컴포넌트가 렌더링 될 때 단 한번만 함수가 생성되며, onInsert처럼 배열 안에 number와 list를 넣게 되면 인풋 내용이 바뀌거나 새로운 항목이 추가될 때마다 함수가 생성됩니다.

함수 내부에서 기존의 상태 값을 의존해야 할 때는 꼭 두 번째 파라미터 안에 포함을 시켜주어야 합니다. 예를 들어서 onChange의 경우에 기존의 값을 조회하는 일은 없고 바로 설정만 하기 때문에 배열이 비어있어도 상관이 없지만, onInsert는 기존의 number와 list를 조회해서 nextList를 생성하기 때문에 배열 안에 number와 list를 꼭 넣어주어야 합니다.

참고로 다음 두 코드는 완벽히 똑같은 코드입니다.

```javascript
useCallback(() => {
    console.log('hello world!');
}, [])

useMemo(() => {
    const fn = () => {
        console.log('hello world!')'
    };
    return fn;
}, [])
```

useCallback은 결국 useMemo에서 함수를 반환하는 상황에서 더 편하게 사용할 수 있는 Hook 입니다. 숫자, 문자열, 객체처럼 일반 값을 재사용하기 위해서는 useMemo를, 그리고 함수를 재사용하기 위해서는 useCallback을 사용하세요.


## useRef

useRef Hook은 함수형 컴포넌트에서 ref를 쉽게 사용할 수 있게 해줍니다. Average 컴포넌트에서 등록 버튼을 눌렀을 때 포커스가 인풋 쪽으로 넘어가도록 코드를 작성하였습니다.

### Average.js

```javascript
import React, { useState, useMemo, useCallback, useRef } from 'react';

const getAverage = numbers => {
  console.log('평균 값 계산 중....');
  
  if (numbers.length === 0) return 0;
  const sum = numbers.reduce((a, b) => a + b);
  return sum / numbers.length;
}

const Average = () => {

  const [ list, setList ] = useState([]);
  const [ number, setNumber ] = useState('');
  const inputEl = useRef(null);

  const onChange = useCallback(e => {
    const { value } = e.target;
    setNumber(value);
  }, []);

  const onInsert = useCallback(e => {
    const nextList = list.concat(parseInt(number));
    setList(nextList);
    setNumber('');
    inputEl.current.focus();
  },[number, list]);

  const avg = useMemo(() => getAverage(list), [list]);

  return (
    <div>
      <input value={number} onChange={onChange} ref={inputEl} />
      <button onClick={onInsert}>등록</button>
      <ul>
        {
          list.map((value, index) => 
            <li key={index}>{value}</li>
          )
        }
      </ul>
      <div>
        <b>평균 값:</b>{avg}
      </div>
    </div>
  );
};

export default Average;
```

useRef를 사용하여 ref를 설정하면, useRef를 통해 만든 객체 안의 current 값이 실제 엘리먼트를 가르키게 됩니다.

## 커스텀 Hooks 만들기

만약에 에러 컴포넌트에서 비슷한 기능을 공유하게 되는 일이 발생한다면 이를 우리만의 Hook을 작성하여 로직을 재사용 할 수 있습니다.

### useInput.js

```javascript
import React from "react";
import useInput from './useInput';

const Info = () => {
  const [ state, onChange ] = useInput({
    name: '',
    nickname: '',
    age:''
  });
  const { name, nickname } = state;

  return (
    <div>
      <div>
        <input name="name" value={name} onChange={onChange} />
        <input name="nickname" value={nickname} onChange={onChange} />
      </div>
      <div>
        <div>
          <b>이름:</b>
          {name}
        </div>
        <div>
          <b>닉네임:</b>
          {nickname}
        </div>
      </div>
    </div>
  );
};

export default Info;
```

useInput Hook을 사용하면서 아까전에 작성했던 Info 컴포넌트가 훨씬 코드가 깔끔해졌습니다.


## usePromise

함수형 컴포넌트에서 Promise를 더 쉽게 사용할 수 있는 Hook을 만들어보겠습니다.

### usePromise.js

```javascript
import { useState, useEffect } from 'react';

export default function usePromise(createPromise, deps) {

    const [resolved, setResolved] = useState(null);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);

    const process = async () => {
       setLoading(true);
       try {
         const result = await createPromise();
         setResolved(result);
       } catch (e) {
         console.log(e); // name: message
         setError(e)
       }
       setLoading(false);
    };
    
    useEffect(() => {
        process();
    }, deps);

    return [resolved, loading, error];
}
```

위 커스텀 훅에서 useState와 useEffect를 함께 사용하였습니다ㅏ. 이 함수는 프로미스를 생성하는 promiseCreator와, 언제 프로미스를 새로 만들지에 대한 조건을 위한 deps 배열을 파라미터로 받아옵니다. 이 deps 배열은 useEffect의 두 번째 파라미터로 전달되며, 기본값은 비어있는 배열입니다.

useEffect를 사용하실 때 주의하실 점이 있는데, useEffect에 파라미터로 전달해주는 함수에서 async를 사용하면 안됩니다. 예를 들어서 다음 코드는 오류를 발생하는 코드입니다.

```javascript
useEffect(asyc () => {});
```

useEffect에서는 뒷정리 함수를 반환해야 하는데, async를 사용하게 되면 함수가 아닌 프로미스를 반환하기 때문에 오류가 발생하게 됩니다.

이제 이 Hook을 사용하는 예제 컴포넌트를 작성해보겠습니다.

### UsePromiseSample.js

```javascript
import React from 'react';
import usePromise from './usePromise';

const wait = () => {
    return new Promise(resolve => {
        setTimeout(() => resolve('Hello hooks!'), 3000);
    });
}

const UsePromiseSample = () => {
    const [resolved, loading, error] = usePromise(wait, []);

    if (loading) return <div>로딩중...!</div>;
    if (error) return <div>에러 발상!</div>;
    if (!resolved) return null;

    return <div>{resolved}</div>;
};

export default usePromiseSample;
```

>> awiat 키워드가 적용되는 대상은 반드시 프로미스 객체를 반환해야 await가 의도한 대로 동작합니다. 일반적으로 await의 대상이 되는 비동기 처리 코드는 Axios 등 프로미스를 반환하는 API 호출 함수입니다.


>> 함수형 컴포넌트와 클래스 형 컴포넌트의 차이점이 무엇일까? 클래스 형 컴포넌트의 경우 state 기능 및 라이프사이클 기능을 사용할 수 있으며 임의 메서드를 정의할 수 있다는 점입니다. 반면 함수형 컴포넌트는 클래스형 컴포넌트보다 선언하기가 좀 더 편하고, 메모리 자원을 덜 사용한다는 장점입니다. 단순하게 데이터를 받아서 UI에 뿌려줄 경우 많이 사용합니다. 과거에는 클래스형에 비해서 state와 라이프사이클 API를 사용할 수 없다는 단점이 있었는데 v16.8 이후로 훅이 도입되면서 해결되었습니다. 지금 공식 문서에서도 함수형 컴포넌트 + 훅을 사용하는 것을 권고하고 있습니다.