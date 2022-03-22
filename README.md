# What I learned?

## 타입스크립트 사용을 위한 설정
```
npx create-react-app --template typescript
```
이 작업을 통해서 CRA가 타입스크립트를 이용할 때 필요한 기본적인 것들을 설치, 설정해준다.

### Cannot use JSX unless the '--jsx' flag is provided 오류
해당 오류는 vscode의 버전을 로컬과 확인하여 통일시켜주어야 한다.
vscode 우하단에 타입스크립트 버전을 클릭하여 타입스크립트 버전을 workspace 버전으로 맞춰주면 오류가 해결된다.

## FC
`React.FC`를 사용하여 컴포넌트의 타입을 지정할 수 있다.
```typescript
type GreetingsProps = {
  name: string;
};

const Greetings: React.FC<GreetingsProps> = ({ name }) => (
  <div>Hello, {name}</div>
);
```
Greetings 컴포넌트가 사용된 곳에서 name을 넘겨줄때 name의 기본 타입이 string인지 검사합니다.

하지만 `FC`를 사용하면 defaultProps가 제대로 작동하지 않는 문제가 있다.
```typescript
type GreetingsProps = {
  name: string;
  mark: string;
};

const Greetings: React.FC<GreetingsProps> = ({ name, mark }) => (
  <div>
    Hello, {name} {mark}
  </div>
);

Greetings.defaultProps = {
  mark: '!'
};

export default Greetings;
```
위 경우에서 Greetings 컴포넌트를 사용하면 `defaultProps`에 mark 값이 있음에도 불구하고 mark의 값이 없다는 에러가 발생한다.

이러한 문제는 `FC`를 사용하였을 때 나타난다.
```typescript
const Greetings = ({ name, mark }: GreetingsProps) => (
  <div>
    Hello, {name} {mark}
  </div>
);

Greetings.defaultProps = {
  mark: '!'
};
```
이 경우 컴포넌트를 선언할 때 위와 같이 코드를 작성하여 문제를 해결할 수 있다.

만약 컴포넌트에 경우에 따라 필요한 `props`가 있다면 아래와 같이 `?`문자를 사용하면 된다.
```typescript
import React from 'react';

type GreetingsProps = {
  name: string;
  mark: string;
  optional?: string;
};

function Greetings({ name, mark, optional }: GreetingsProps) {
  return (
    <div>
      Hello, {name} {mark}
      {optional && <p>{optional}</p>}
    </div>
  );
}

Greetings.defaultProps = {
  mark: '!'
};

export default Greetings;
```

## 함수의 타입 지정
컴포넌트에서 특정 함수를 `props`로 받아와 사용해야 한다면 아래와 같이 이용할 수 있다.
```typescript
import React from 'react';

type GreetingsProps = {
  name: string;
  mark: string;
  optional?: string;
  onClick: (name: string) => void; // 아무것도 리턴하지 않는다는 함수를 의미합니다.
};

function Greetings({ name, mark, optional, onClick }: GreetingsProps) {
  const handleClick = () => onClick(name);
  return (
    <div>
      Hello, {name} {mark}
      {optional && <p>{optional}</p>}
      <div>
        <button onClick={handleClick}>Click Me</button>
      </div>
    </div>
  );
}

Greetings.defaultProps = {
  mark: '!'
};

export default Greetings;
```

위 코드는 아래와 같이 실행할 수 있다.
```typescript
import React from 'react';
import Greetings from './Greetings';

const App: React.FC = () => {
  const onClick = (name: string) => {
    console.log(`${name} says hello`);
  };
  return <Greetings name="Hello" onClick={onClick} />;
};

export default App;
```
