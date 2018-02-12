# TypeScript

---

### Интерфейс (interface)

--

```ts
interface Simple {
  a: number;
  b: string;
}

interface SomeOptions {
  isActive: boolean;
  callback(...args: any[]): void;
  optionalCallback?: (...args: any[]) => void;
  list: string[];
  matrix: number[][];
}
```

--

Пример

```ts
const a: Simple = {
  a: 42,
  b: "HAI!",
};

const b: SomeOptions = {
  isActive: false,
  callback() {},
  list: ["one", "two"],
  matrix: [[1, 2, 3], [4, 5, 6], [7, 8, 9]],
}
```

--

```ts
interface CPFBaseOptions {
  attrs?: { [key: string]: string };
  cssClss?: { [key: string]: string };
  cssSels?: { [key: string]: string };
}

interface CPFInstance<O extends CPFBaseOptions> {
  off(name: string): void;
  on(name: string, f: (...args: any[]) => void): void;
  once(name: string, f: (...args: any[]) => void): void;
  options: O;
}

```

--

Интерфейсы могут быть расширены

```ts
interface Foo {
  a: number;
}

interface Foo {
  b: string;
}

interface Foo {
  c: boolean;
}

const x: Foo = {
  a: 1,
  b: "two",
  c: true
};
```

--

### Псевдонимы типа (type alias)

```ts
type Alias = number;
type CustomCallback = (a: number, b: number) => number;
type CustomUnion = Alias | CustomCallback;
type CustomIntersection = Simple & SomeOptions;
type UnionOfStrings = "one" | "two" | "three"

interface IThird extends Simple, SomeOptions {
  foo: string;
}

type KeysOfThird = keyof IThird;
```

--

Иногда они пытаются прикинуться интерфейсами
```ts
type WannaBeInterface = {
  isActive: boolean;
  callback(...args: any[]): void;
  optionalCallback?: (...args: any[]) => void;
  list: string[];
  matrix: number[][];
}
```

--

Типы могут быть выведены из выражения

```ts
const x = {
  a: 1,
  b: 2,
  c: 3,
};

type XType = typeof x;

```

--

Создание index-типов на основе существующих

```ts
const x = {
  a: 1,
  b: 2,
  c: 3,
};

type SampleValue = string | number | boolean;

type SampleKV = {
  [P in keyof typeof x]: SampleValue;
}
```

--


Документация

```
https://www.typescriptlang.org/docs/handbook/interfaces.html
https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html
```

---

### Обобщённые типы (generics)

--


```ts
interface InterfaceWithGenerics<T, P> {
  value: T;
  product(v: T): P;
}

type FizzBuzz<T> = (x: T) => T;

function foo<T, S>(arg: T): S {
  // ...
}

class CustomComponent extends React.Component<Props, State> {
  // ...
}
```

--

Примеры из реальной жизни

```ts
function request<T>(url: string) {
  return fetch(url, {
    credentials: "include",
    mode: "cors",
  })
  .then((response) => response.json())
  .then((json) => json as T);
}

interface MyCustonResponse {
  a: string;
  b: number;
  status: "ok" | "error";
}

request<MyCustonResponse>("https://mail.ru/api/v1/data")
  .then((data) => {
    console.log(`a = ${data.a}, b = ${data.b}`);
  });
```

--

Пример сложнее:

```ts
export interface WidgetProps<T = string> {
  id: ID;
  isDebug?: boolean;
  isActive?: boolean;
  slots: Map<T, React.ReactNode>;
}

// ...

export type LayoutSlots = "header" | "content" | "footer" | "sidebar";
export interface LayoutProps extends Platform.WidgetProps<LayoutSlots> {
  sidebarPosition?: "left" | "right";
}

class Layout extends React.PureComponent<LayoutProps> {
  public render() {
    const { slots } = this.props;

    const content = slots.get("content"); // ok
    const sidebar = slots.get("sidebar"); // ok
    const custom = slots.get("custom"); // error
    // ...
  }
}
```

--

Документация:

```
https://www.typescriptlang.org/docs/handbook/generics.html
https://www.typescriptlang.org/docs/handbook/advanced-types.html
https://stash.mail.ru/projects/MF/repos/mpf-utils/browse/src/fp/common.ts#1-69
```

---

### TypeScript helpers

--

Partial

```ts
interface HelperA {
  foo: string;
  bar: number;
  fizz: boolean;
  buzz: "one" | "two" | "three";
}

type HelperAP = Partial<HelperA>;
/*
type HelperAP = {
  foo?: string;
  bar?: number;
  fizz?: boolean;
  buzz?: "one" | "two" | "three";
}
*/
```

--

Readonly

```ts
interface HelperA {
  foo: string;
  bar: number;
  fizz: boolean;
  buzz: "one" | "two" | "three";
}

type HelperARO = Readonly<HelperA>;
/*
type HelperARO = {
  readonly foo: string;
  readonly bar: number;
  readonly fizz: boolean;
  readonly buzz: "one" | "two" | "three";
}
*/
```

--

Pick

```ts
interface HelperA {
  foo: string;
  bar: number;
  fizz: boolean;
  buzz: "one" | "two" | "three";
}

type HelperAPick = Pick<HelperA, "fizz" | "buzz">;
/*
type HelperAPick = {
  fizz: boolean;
  buzz: "one" | "two" | "three";
}
*/
```

--

Record

```ts
interface HelperA {
  foo: string;
  bar: number;
  fizz: boolean;
  buzz: "one" | "two" | "three";
}

interface HelperB {
  a: number;
  b: string;
  c: "a" | "b" | "c";
  d: 1 | 2 | 3;
}

type SomeRecord = Record<"a" | "b" | "c", number>;
/*
type SomeRecord = {
  a: number;
  b: number;
  c: number;
}
*/

type SomeRecordFromAAndB = Record<keyof HelperA, Readonly<HelperB>>;
/*
type SomeRecordFromAAndB = {
  foo: {
    readonly a: number;
    readonly b: string;
    readonly c: "a" | "b" | "c";
    readonly d: 1 | 2 | 3;
  };
  bar: {
    readonly a: number;
    readonly b: string;
    readonly c: "a" | "b" | "c";
    readonly d: 1 | 2 | 3;
  };
  fizz: {
    readonly a: number;
    readonly b: string;
    readonly c: "a" | "b" | "c";
    readonly d: 1 | 2 | 3;
  };
  buzz: {
    readonly a: number;
    readonly b: string;
    readonly c: "a" | "b" | "c";
    readonly d: 1 | 2 | 3;
  };
}
*/
```

--

```ts
type HelperBPickRO = Readonly<Pick<IHelperB, "a" | "b">>

type SomeRecordRO = Readonly<Record<"a" | "b" | "c", number>>;
```

--

Документация и примеры
```
https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html
```

---

## any и null

---

## tsconfig.json (strict mode)

--

```json
{
  "compilerOptions": {
    "target": "es5",                          /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', or 'ESNEXT'. */
    "module": "commonjs",                     /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', or 'ESNext'. */
    // "lib": [],                             /* Specify library files to be included in the compilation:  */
    // "allowJs": true,                       /* Allow javascript files to be compiled. */
    // "checkJs": true,                       /* Report errors in .js files. */
    // "jsx": "preserve",                     /* Specify JSX code generation: 'preserve', 'react-native', or 'react'. */
    // "declaration": true,                   /* Generates corresponding '.d.ts' file. */
    // "sourceMap": true,                     /* Generates corresponding '.map' file. */
    // "outFile": "./",                       /* Concatenate and emit output to single file. */
    // "outDir": "./",                        /* Redirect output structure to the directory. */
    // "rootDir": "./",                       /* Specify the root directory of input files. Use to control the output directory structure with --outDir. */
    // "removeComments": true,                /* Do not emit comments to output. */
    // "noEmit": true,                        /* Do not emit outputs. */
    // "importHelpers": true,                 /* Import emit helpers from 'tslib'. */
    // "downlevelIteration": true,            /* Provide full support for iterables in 'for-of', spread, and destructuring when targeting 'ES5' or 'ES3'. */
    // "isolatedModules": true,               /* Transpile each file as a separate module (similar to 'ts.transpileModule'). */

    /* Strict Type-Checking Options */
    "strict": true                            /* Enable all strict type-checking options. */
    // "noImplicitAny": true,                 /* Raise error on expressions and declarations with an implied 'any' type. */
    // "strictNullChecks": true,              /* Enable strict null checks. */
    // "strictFunctionTypes": true,           /* Enable strict checking of function types. */
    // "noImplicitThis": true,                /* Raise error on 'this' expressions with an implied 'any' type. */
    // "alwaysStrict": true,                  /* Parse in strict mode and emit "use strict" for each source file. */

    /* Additional Checks */
    // "noUnusedLocals": true,                /* Report errors on unused locals. */
    // "noUnusedParameters": true,            /* Report errors on unused parameters. */
    // "noImplicitReturns": true,             /* Report error when not all code paths in function return a value. */
    // "noFallthroughCasesInSwitch": true,    /* Report errors for fallthrough cases in switch statement. */

    /* Module Resolution Options */
    // "moduleResolution": "node",            /* Specify module resolution strategy: 'node' (Node.js) or 'classic' (TypeScript pre-1.6). */
    // "baseUrl": "./",                       /* Base directory to resolve non-absolute module names. */
    // "paths": {},                           /* A series of entries which re-map imports to lookup locations relative to the 'baseUrl'. */
    // "rootDirs": [],                        /* List of root folders whose combined content represents the structure of the project at runtime. */
    // "typeRoots": [],                       /* List of folders to include type definitions from. */
    // "types": [],                           /* Type declaration files to be included in compilation. */
    // "allowSyntheticDefaultImports": true,  /* Allow default imports from modules with no default export. This does not affect code emit, just typechecking. */
    // "preserveSymlinks": true,              /* Do not resolve the real path of symlinks. */

    /* Source Map Options */
    // "sourceRoot": "./",                    /* Specify the location where debugger should locate TypeScript files instead of source locations. */
    // "mapRoot": "./",                       /* Specify the location where debugger should locate map files instead of generated locations. */
    // "inlineSourceMap": true,               /* Emit a single file with source maps instead of having a separate file. */
    // "inlineSources": true,                 /* Emit the source alongside the sourcemaps within a single file; requires '--inlineSourceMap' or '--sourceMap' to be set. */

    /* Experimental Options */
    // "experimentalDecorators": true,        /* Enables experimental support for ES7 decorators. */
    // "emitDecoratorMetadata": true,         /* Enables experimental support for emitting type metadata for decorators. */
  }
}
```

--

```json
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "lib": [
      "esnext",
      "dom"
    ],
    "jsx": "react",
    "declaration": true,
    "strict": true,
    "noUnusedLocals": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "allowSyntheticDefaultImports": true,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "traceResolution": true,
    "pretty": true
  }
}
```

--

Документация:

```
https://www.typescriptlang.org/docs/handbook/tsconfig-json.html
```

---

## tslint

--

```
$ ./node_modules/.bin/tslint --init
```

--

```json
{
    "defaultSeverity": "error",
    "extends": [
        "tslint:recommended"
    ],
    "jsRules": {},
    "rules": {
        "no-empty-interface": false,
        "max-line-length": false,
        "interface-over-type-literal": false,
        "interface-name": false
    },
    "rulesDirectory": []
}
```

---

## @types

--

```json
{
  "dependencies": {
    "@types/classnames": "^2.2.3",
    "@types/node": "^9.3.0",
    "@types/react": "^16.0.26",
    "@types/react-dom": "^16.0.3",

    "react": "^16.2.0",
    "react-dom": "^16.2.0",
  }
}
```

--

http://definitelytyped.org

---

# React

---

### Component, PureComponent, Props, State, events, css

--

Описываем какие данные получает наш компонент и какими данными он оперирует

```ts
export interface PersonProps {
  firstName: string;
  lastName: string;
  gender: "male" | "female";
  onToggleOnline: (fname: string, lname: string, isOnline: boolean) => void;
  age?: number;
  email?: string;
  isOnline?: boolean;
  defaultMessage?: string;
}

export interface PersonState {
  isOnline: boolean;
  statusMessage: string;
}
```

--

```ts
import * as React from "react";

class PersonComponent extends React.Component<PersonProps, PersonState> {
  static defaultProps: Partial<PersonProps> = {
    email: "anonymous@corp.mail.ru",
  }
}

export default Person;
```

--

Необходимо задать данные по-умолчанию

```ts
{
  constructor(props: PersonProps) {
    super(props);
    this.state = {
      isOnline: props.isOnline || false,
      statusMessage: props.defaultMessage || "Hello, World!",
    };
  }
}
```

--

Напишем немного css

```css
/* Person.css */
.root {
  /* ... */
}

.isActive {
  /* ... */
}

.gender_male {
  /* ... */
}

.gender_female {
  /* ... */
}
```

--

```ts
import * as cx from "classnames";

import * as styles from "./Person.css";
```

--

```ts
{
  public render() {
    const { age, email, firstName, gender, lastName } = this.props;
    const { isActive, statusMessage } = this.state;
  
    const classSet = cx(styles.root, {
      [styles.isActive]: isActive,
      [styles.gender_male]: gender === "male",
      [styles.gender_female]: gender === "female",
    });
  
    return (
      <div className={classSet}>
        <h3>{firstName} {lastName}</h3>
        {age
          ? (<div>{age}</div>)
          : null}
        {gender
          ? (<div>{gender}</div>)
          : null}
        <div>
          <input
            value={statusMessage}
            onChange={this.handleStatusChange} />
        </div>
        <div>
          <button onClick={this.handleButtonClick}>
            {isActive ? "deactivate" : "activate"}
          </button>
        </div>
      </div>
    );
  }
}
```

--

Но TypeScript не умеет импортировать CSS

--

Вариант 1: написать всё самому

typed-css-modules  
https://github.com/Quramy/typed-css-modules

--

```js
const glob = require('glob');
const DtsCreator = require('typed-css-modules');

const creator = new DtsCreator();

process.stdout.write('\x1Bc');

glob('src/**/*.css', {}, (err, files) => {
  for (const file of files) {
    creator.create(file).then(content => {
      const { inputPath } = content;
      content.writeFile();
    });
  }
});
```

--

Вариант 2: loader для webpack:

https://github.com/Jimdo/typings-for-css-modules-loader

--

Person.css.d.ts

```ts
export const root: string;
export const isActive: string;
export const gender_male: string;
export const gender_female: string;
```

--

```ts
import * as styles from "./Person.css";
```

--

Событие нажатия на кнопку

```ts
{
  private handleButtonClick = (event: React.SyntheticEvent<HTMLButtonElement>) => {
    const { onToggleOnline, firstName, lastName } =  this.props;
    // 1. 99% кейсов
    this.setState({
      isOnline: !this.state.isOnline,
    });

    // 2. Если необходимо исходить из предыдущего state
    this.setState((prevState/*: PersonState */, props/*: PersonProps */) => {
      return {
        ...state,
        isOnline: !this.state.isOnline,
      };
    });

    // 3.
    this.setState({
      isOnline: !this.state.isOnline,
    }, () => {
      alert("🙈🙉🙊");
      onToggleOnline(firstName, lastName, this.state.isOnline);
    });
  }
}
```

--

Событие изменения input'а

```ts
{
  private handleStatusChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    const { value } = event.target;
    this.setState({
      statusMessage: value,
    });
  }
}
```

--

Собираем всё в кучу

```ts
import * as React from "react";
import * as ReactDOM from "react-dom";

import Person from "./Person";

function flashUser(fname: string, lname: string, isActive: boolean) {
  alert(`${fname} ${lname} ${isActive ? "активирован" : "деактивирован"}`);
}

ReactDOM.render(
  <PersonComponent
    firstName="Леонид"
    lastName="Якубович"
    gender="male"
    onToggleActive={flashUser} />
, document.getElementById("root"));
```

--

Документация
```
https://reactjs.org/docs/hello-world.html
```

Код примера
```
https://gist.github.com/ChillyBwoy/3f8db9190a20b1e6a9891102df582bca
```

---

## Redux

--

```ts
export interface PersonFromProps {
  email: string;
  onToggleOnline: (fname: string, lname: string, isOnline: boolean) => void;
}

export interface PersonProps {
  firstName: string;
  lastName: string;
  gender: "male" | "female";
  onToggleOnline: PersonFromProps["onToggleOnline"];
  age?: number;
  email?: string;
  isOnline?: boolean;
  defaultMessage?: string;
}

export interface PersonState {
  isOnline: boolean;
  statusMessage: string;
}
```

--

```ts
// types.ts

export interface Person {
  firstName: string;
  lastName: string;
  age: number;
  gender: "male" | "female";
  email: string;
  isOnline: boolean;
  statusMessage: string; // теперь храним его в общем сторе
}

export interface StoreState {
  persons: {
    [key: string]: Person
  }
}

export interface ActionUpdatePerson {
  type: "@app/UPDATE_PERSON";
  statusMessage: string;
  isOnline: boolean;
}

export interface ActionNotifyPerson {
  type: "@app/NOTIFY_PERSON";
  email: string;
}

export type Action
  = ActionUpdatePerson
  | ActionNotifyPerson;

```

--

```ts
// actionCreators
import {
  ActionUpdatePerson,
  ActionNotifyPerson,
  Person,
} from "../types";

export function update(email: string, payload: Pick<Person, "isOnline" | "statusMessage">): ActionUpdatePerson {
  return {
    type: "@app/UPDATE_PERSON",
    email,
    isOnline: payload.isOnline,
    statusMessage: payload.statusMessage,
  };
}

export function notify(email: string): ActionNotifyPerson {
  return {
    type: "@app/NOTIFY_PERSON",
    email,
  };
}
```

--

```ts
// reducer.ts

import { Action, State } from "./types";

const initialState: State = {
  persons: {}
};

export default (state: State = initialState, action: Action): State => {
  switch (action.type) {
    case "@app/NOTIFY_PERSON": {
      const person = state.persons[action.email];
      // alert(`OH HAI, ${person.firstName}!`);
      return state;
    }

    case "@app/UPDATE_PERSON": {
      const person = state.persons[action.email];

      return {
        ...state,
        [email]: {
          ...person,
          isOnline: action.isOnline,
          statusMessage: action.statusMessage,
        },
      };
    }

    default: {
      return state;
    }
  }
};

```

--

```ts
import * as React from "react";
import * as ReactDOM from "react-dom";
import * as Redux from "redux";
import { Provider } from "react-redux";
import { fetchAll } from "./store/actionCreators";
import reducer from "./store/reducer";
import Person from "./Person";

const store = Redux.createStore(reducer);

function flashUser(fname: string, lname: string, isOnline: boolean) {
  alert(`${fname} ${lname} ${isOnline ? "активирован" : "деактивирован"}`);
}

ReactDOM.render(
  <Provider store={store}>
    <PersonComponent
      email="yakubovich@corp.mail.ru"
      onToggleActive={flashUser} />
  </Provider>
, document.getElementById("root"));

```

--

```ts
import { connect } from "react-redux";
import * as Redux from "redux";

const mapStateToProps = (state: StoreState, props: PersonFromProps): PersonProps => {
  const person = state.persons[props.email];

  return {
    firstName: person.firtName,
    lastName: person.lastName,
    gender: person.gender;
    onToggleOnline: props.onToggleOnline,
    age: person.age,
    statusMessage: person.statusMessage,
    isOnline: person.isOnline,
    email: props.email,
  };
};

```

--

```ts
import { notify, update } from "./store/actionCreators";

export interface PersonDispatchProps {
  onUpdate(payload: Pick<Person, "isOnline" | "statusMessage">): void;
  onNotify(): void;
}

const mapDispatchToProps = (dispatch: Redux.Dispatch<StoreState>, props: PersonFromProps): PersonDispatchProps => {
  return {
    onUpdate(payload) {
      dispatch(update(props.email, payload));
    },
    onNotify() {
      dispatch(notify(props.email));
    }
  };
};

```

--

```ts
class PersonComponent extends React.Component<PersonProps & PersonDispatchProps, PersonState> {
  // ...
  private handleUpdate = (event: React.SynthticEvent<???>) => {
    const { onUpdate } = this.props;

    onUpdate({
      isOnline: this.state.isOnline,
      statusMessage: this.state.statusMessage,
    });
  }
}
```

--

```ts
export default connect(mapStateToProps, mapDispatchToProps)(PersonComponent);
```

--

https://redux.js.org

