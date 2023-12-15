# 리엑트 최적화 활용 및 Ref

ShouldComponentUpdate 함수는 컴포넌트의 상태 값과 부모 컴포넌트로부터 받은 props 객체의 값에 따라서 리렌더링 할지 안할지 결정해주는 함수 입니다. 만약 PhoneInfo 컴포넌트 중에서 굳이 update를 할 필요가 없는데 다른 PhoneInfo 컴포넌트의 수정이 발생할 때마다 모든 PhoneInfo 컴포넌트가 리렌더링 된다면 업데이트 성능이 많이 떨어질 수 밖에 없습니다. 


```javascript
shouldComponentUpdate(nextProps, nextState) {
    return true;
}  
```
기본적으로 shouldComponentUpdate 함수는 별도로 구현하지 않으면 true를 리턴합니다.


```javascript
shouldComponentUpdate(nextProps, nextState) {
    if (this.state !== nextState) {
        return true;
    }
    return this.props.info !== nextProps.info;  
}    
```

위에 shouldComponentUpdate 함수를 구현해서 PhoneInfo 컴포넌트 업데이트를 하기 위한 조건 로직을 작성하였습니다. setState 함수가 호출되면 무조건 컴포넌트를 업데이트를 해주고, 부모 컴포넌트로 부터 받은 props 객체가 다르면 마찬가지로 업데이트를 해줍니다. 

만약 불변성을 보장하지 않는다면 컴포넌트를 업데이트 할 때 객체가 가지고 있는 벨류들을 비교해야 되고 그러면 더 복잡해지기 때문에 state나 props는 불변성을 가져야 합니다.


## 전화번호부 검색 

이번에는 이름으로 전화번호를 찾는 예제를 작성해보았습니다.

먼저, App 컴포넌트에서 state 객체에 keyword를 추가하고 render() 함수에서 검색이라는 값을 가진 input 엘리먼트를 생성하였습니다.

```javascript
import React, { Component } from 'react';
import PhoneForm from './components/PhoneForm';
import PhoneInfoList from './components/PhoneInfoList';


class App extends Component {

  id = 3;

  state = {
    information: [
      {
        id: 0,
        name: '임준영',
        phone: '010-7900-7714',
      },
      {
        id: 1,
        name: '김광용',
        phone: '010-8954-7045',
      },
      {
        id: 2,
        name: '김진환',
        phone: '010-8700-7514',
      }
    ],
    keyword: '',
  }

  handleChange = (e) => {
    this.setState({
      keyword: e.target.value
    });
  }

  handleCreate = (data) => {
    const { information } = this.state;
    this.setState({
      information: information.concat({
        ...data,
        id: this.id++,
      }) // concat은 인자로 주어진 배열이나 값들을 기존의 배열에 합쳐서 새 배열을 반환힘.
    });
  }

  handleRemove = (id) => {
    const { information } = this.state;
    this.setState({
      information: information.filter(info => info.id !== id)
    });
  }

  handleUpdate = (id, data) => {
    const { information } = this.state;
    this.setState({
      information: information.map(
        info => {
        if (info.id === id) {
          return {
            id,
            ...data,
          };
        } 
        return info;
      })
    });

  } 

  render() {
    return (
      <div>
       <PhoneForm onCreate={this.handleCreate} />
       <input
        value={this.state.keyword}
        onChange={this.handleChange}
        placeholder="검색..."
       />
       <PhoneInfoList 
        data={this.state.information.filter(
          info => info.name.indexOf(this.state.keyword) > -1
        )} 
        onRemove={this.handleRemove}
        onUpdate={this.handleUpdate}
        />
      </div>
    ); 
  }
}

export default App;
```

마찬가지로 변경 감지 이벤트 함수를 만들어서 검색 창에 값을 입력할 때 setState() 함수로 keyword를 셋팅해줍니다. 그러면 App 컴포넌트는 렌더링이 됩니다.


```javascript
<PhoneInfoList 
    data={this.state.information.filter(
    info => info.name.indexOf(this.state.keyword) > -1
)} 
```

그리고 PhoneInfoList 컴포넌트에 배열 객체를 props로 전달할 때 내장 메서드인 filter 함수를 사용하여 배열 객체 요소에 name 프로퍼티 값에 keyword 값이 포함되어 있는 리엑트 컴포넌트만 생성하면 검색 창에 이름을 입력했을 때 해당 PhoneInfo 컴포넌트만 렌더링 됩니다.


## Ref

전화번호를 등록하면 focus가 전화번호 input 엘리먼트로 되어 있는데, 만약 이름 input 엘리먼트로 focus를 두고 싶다면 ref를 사용하면 됩니다.

```javascript
import React, { Component } from 'react';

class PhoneForm extends Component {
    input = null
    ``
    state = {
        name: '',
        phone: '',
    }

    handleChange = (e) => {
        this.setState({
            [e.target.name] : e.target.value 
        });
    }

    handleSubmit = (e) => {
        e.preventDefault();
        this.props.onCreate(this.state);
        this.setState({
            name: '',
            phone: '',
        });
        this.input.focus();
    }

    render() {
        return (
            <form onSubmit={this.handleSubmit}>
                <input 
                    name="name"
                    placeholder="이름" 
                    onChange={this.handleChange} 
                    value={this.state.name} 
                    ref={ref => this.input=ref}
                />
                <input 
                    name="phone"
                    placeholder="전화번호" 
                    onChange={this.handleChange}
                    value={this.state.phone} 
                />
                <button type="submit">등록</button>
            </form>
        );
    }
}

export default PhoneForm;
```

PhoneForm 컴포넌트의 render() 함수에서 input 엘리먼트에 포커스를 두고 싶은 엘리먼트에 ref 속성을 추가하고 화살표 함수로 PhoneForm 컴포넌트의 클래스 필드인 input 값을 input 엘리먼트의 참조 값으로 초기화 시켜줍니다.

```javascript
 handleSubmit = (e) => {
    e.preventDefault();
    this.props.onCreate(this.state);
    this.setState({
        name: '',
        phone: '',
    });
    this.input.focus();
}
```

그리고 등록 버튼을 눌렀을 때 `this.input.focus()`를 호출하면 실제 input이 참조하고 있는 DOM 객체에 focus를 두게 됩니다.

리엑트 16.3v에서는 이렇게도 사용이 가능합니다.

```javascript
import React, { Component } from 'react';

class PhoneForm extends Component {
    input = React.createRef();

    state = {
        name: '',
        phone: '',
    }

    handleChange = (e) => {
        this.setState({
            [e.target.name] : e.target.value 
        });
    }

    handleSubmit = (e) => {
        e.preventDefault();
        this.props.onCreate(this.state);
        this.setState({
            name: '',
            phone: '',
        });
        this.input.current.focus();
    }

    render() {
        return (
            <form onSubmit={this.handleSubmit}>
                <input 
                    name="name"
                    placeholder="이름" 
                    onChange={this.handleChange} 
                    value={this.state.name} 
                    ref={this.input}
                />
                <input 
                    name="phone"
                    placeholder="전화번호" 
                    onChange={this.handleChange}
                    value={this.state.phone} 
                />
                <button type="submit">등록</button>
            </form>
        );
    }
}

export default PhoneForm;
```

PhoneInfo 컴포넌트의 클래스 필드 `input = React.createRef()`로 초기화 시켜준 후에 foucs를 두기 위한 input 엘리먼트의 ref 속성 값을 this.input으로 참조합니다. 그리고 등록 버튼을 눌렀을 때 handleSubmit() 함수에서 마지막에 this.input.current.focus()를 호출하면 됩니다.

current라는 값을 통해서 해당 DOM에 접근이 가능하게 됩니다.

