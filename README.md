# JavaScript

## 목차
1. [기본문법](#1.-기본문법)
2. [ES6+ 문법](#2.-ES6+-문법)
3. [비동기처리](#3.-비동기처리)
4. [HTTP통신](#4.-HTTP통신)

## 1. 기본문법
### [hoisting](https://developer.mozilla.org/ko/docs/Glossary/Hoisting) (MDN 링크)
```javascript
console.log(add(1)); // 2

console.log(num1); // undefined
var num1 = 1;

console.log(num2); // reference error
let num2 = 2;

console.log(num3); // reference error
const num3 = 3;

function add(input){
    return input + 1;
}
```
### [this](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/this) (MDN 링크)
- this는 자신이 속한 객체 혹은 자신이 생성하는 인스턴스 가르키는 자기참조 변수 입니다.
```javascript
const dog = {
    name:'누렁이',
    say: function(){ console.log(this.name + "는 왈왈") }, // 익명함수
};
dog.say(); // 누렁이는 왈왈

// 생성자 함수 선언
function Student(name){
    this.name = name;
    this.say = function(){
        console.log("Hello my name is " + this.name);
    }
}

// 인스턴스 생성
const minsu = new Student('민수');
minsu.say(); // Hello my name is 민수

// this는 함수가 실행되는 시점에 결정됩니다.
const john = {
    name: '존'
}

john.say = minsu.say;

john.say(); // Hello my name is 존

// 전역 및 일반함수에서 this는 window 전역 객체입니다.
console.log(this); // window object

function nomalFunction(){
    console.log(this); // window object
}

nomalFunction(); 

// PS. 추가적으로 bind, call, apply 함수를 통해 this를 지정해줄 수 있습니다.
```
### [scope](https://developer.mozilla.org/ko/docs/Glossary/Scope) (MDN 링크)
```javascript
var item = '손전등';

function room(){
    var state = '정전';
    find();
    
    function find(){
        console.log(item + '어디있지?')
    }
}

room(); // 손전등 어디있지?
// scope chain을 통해 상위 scope의 식별자에서 참조할 값을 찾습니다.
```
### execution context
```javascript
var num = 1;

function a(){
    return num;
}

function b(){
    var num = 3;
    return a();
}

console.log(b()); // 1

/** 
 함수의 상위 scope는 함수가 정의될 때 결정됩니다.
 함수 a의 상위스코프는 전역스코프 입니다.
*/

// [주의] this 결정시점과 혼동될 수 있습니다. 함수의 this는 함수가 실행될 때 결정됩니다.

// PS. execution context의 자세한 내용은 처음에 이해하기 어려울 수 있습니다. js에 익숙해진 이후에 학습을 추천합니다.
```
### [closer](https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures) (MDN 링크)
- 상위 scope의 식별자를 참조하는 하위 scope 입니다.
- 상위 scope의 생존주기가 끝나더라도, 반환된 하위 scope에서 상위 scope의 식별자를 참조할 수 있습니다.
- 이 경우, garbage collector는 상위 scope를 메모리에서 해제하지 않습니다.
- [자바스크립트 메모리관리](https://developer.mozilla.org/ko/docs/Web/JavaScript/Memory_Management)
```javascript
function highScope(){ // 상위 scope
    var num = 10;
    return lowScope;
    
    function lowScope(){ // 하위 scope
        console.log(num);
    }
}

const closer = highScope();

closer(); // 10
```
## 2. ES6+ 문법
 - const, let
```javascript
// 프로젝트 내에서는 let과 const를 주로 사용합니다.
var num1 = 1; // 함수레벨 스코프
let num2 = 2; // 블록레벨 스코프
const num3 = 3; // 블록레벨 스코프

var num1 = 1; // 변수 중복선언 가능
let num2 = 2; // syntax error
const num3 = 3; // syntax error
```
 - arrow function
 ```javascript
function add1(input){
     return input + 1;
 };
 const add2 = (input) => { return input + 1 };
 const add3 = (input) => input + 1;
 const add4 = input => input + 1;
 ```
 - template literals
```javascript
const name = '홍길동'
const str1 = name + ' 입니다.' // 홍길동 입니다.
const str2 = `${name} 입니다.` // 홍길동 입니다.
```
 - default parameters
```javascript
function sayHello(name="홍길동"){
    return `안녕하세요 ${name} 입니다.`
}
console.log(sayHello()) // 안녕하세요 홍길동 입니다.
console.log(sayHello('수달')) // 안녕하세요 수달 입니다.
```
 - array and object destructing
```javascript
// literalObject
const cat = {
    name: 'nero',
    age:5,
    type:'cute'
}
const {name, age, type} = cat;
console.log(`${name} is ${age} years old cat and ${type}`); 
// nero is 5 years old cat and cute  

const names = ['사과','토마토','오렌지'];
const [apple,tomato,orange] = names;
console.log(apple,tomato,orange);
// 사과 토마토 오렌지
```
 - import and export
```javascript
// src/index.js
import mainFunction from './sample.js'
console.log(mainFunction()); // something

import otherName, { subFunction } from './sample.js'
console.log(otherName()); // something
console.log(subFunction(1)); // 2

// src/sample.js
function mainFunction(){
    return 'something';
}
// export는 import 시 같은 이름을 사용해야합니다.
export subFunction(input){
    return input + 1;
}
// export default는 import 시 이름이 달라도 됩니다.
export default mainFunction;
```
 - [pormise](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise) (MDN 링크)
```javascript
const p = new Promise((resolve,reject)=>{
	console.log("promise object created!");
  setTimeout(()=>{
  	resolve('something');
  },1000);
});

p.then((value)=>{
	console.log(value);
  return new Promise((resolve,reject)=>{
  	  setTimeout(()=>{
        resolve('something again');
      },1000);
  });
}).then((value)=>{
	console.log(value);
  return new Promise((resolve,reject)=>{
  	  setTimeout(()=>{
        resolve('something again');
      },1000);
  });
}).then((value)=>{
	console.log(value);
  // throw "throw error"
  // return new Promise((resolve,reject)=>{reject("throw error")});
}).catch((message)=>{
	// 위 주석 해제 시 해당 catch 영역이 실행됩니다.
	console.error(message);
}).finally(()=>{
	console.log("promise finish");
});
```
 - rest parameter and spread operator( 비구조화 할당 )
```javascript
const Sam = {
    name:'샘',
    age:26,
    hobby: 'piano'
}

// rest parameter
const {name, ...rest} = Sam;
console.log(rest); // {age:26, hobby:'piano'}

const array = [1,2,3,4,5];
const [one,two,...others] = array;
console.log(others); // [3,4,5]

// spread operator
console.log(...others); // 3,4,5

function convertArgumentsToArray(...spreadedArgument){
    return spreadedArgument;
}

const newArray = convertArgumentsToArray(1,2,3,4,5);

console.log(newArray); // [1,2,3,4,5]
```
 - [class](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes) (MDN 링크)

## 비동기처리
### callback
- 특정 시점에 실행이 필요한 로직(함수)을 함수의 인자로 넘겨주는 방식입니다.
- setTimeout, setInterval, http response 처리 등...
- 무분별한 사용 시 [콜백지옥](https://librewiki.net/wiki/%EC%BD%9C%EB%B0%B1_%EC%A7%80%EC%98%A5)을 맛볼 수 있습니다.
```javascript
const callback = () => console.log("run callback function!");
setTimeout(callback,1000);
```
### [promise](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise) ( MDN 링크 )
- es6 에서 처음 등장한 문법입니다.
- 비동기 작업이 완료된 이후의 처리를 작성할 수 있습니다.
- ~~콜백지옥에서 벗어나게해준 은인~~
```javascript
const callback = (resolve,reject) => {
    console.log('프로미스 객체 생성 시 해당 내용이 실행됩니다.');
    setTimeout(() => {
        resolve('비동기요청 성공');
    },1000);
};
const promise = new Promise(callback);

promise.then((value) => {
    // promise 객체 생성 시 전달된 callback내 resolve() 실행시점에서 해당 내용이 실행됩니다.
    console.log(value); // 비동기요청 성공
}).catch((msg)=>{
    // reject()가 실행되는 시점에서 해당 내용이 실행됩니다.
    // 현재 reject()를 작성하지 않았기때문에 실행하지 않습니다.
}).finally(()=>{
    // 어떠한 경우에서라도 실행이 필요한 로직이 있다면 이곳에 작성합니다.( log 등 ... )
});
```
### [async await](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/async_function) ( MDN 링크 )
- Promise 객체를 사용하는 다른 방법입니다.
- 비동기 처리를 동기적인 문법으로 작성할 수 있습니다.
- `async` 예약어를 함수앞에 붙여 사용합니다. (이하 `async 함수`)
```javascript
async function add(){
    // ... something
}
```
- `await` 예약어는 `async 함수`에서만 사용 가능합니다.
```javascript
function helloAfter2seconds(){
    return new Promise((resolve)=>{ // new 예약어를 잊지맙시다.
        setTimeout(()=>{
            resolve("안녕하세요!");
        },2000);
    });
}
async function sampleAsyncFunction(){
    console.log("start async function");
    
    const result = await helloAfter2seconds(); 
    console.log(result); // 안녕하세요!
    
    console.log("end async function");
}
sampleAsyncFunction();

/**
    [async 함수 실행 중 await 도달 시 처리 순서]
    
    1. sampleAsyncFunction() 실행을 중단합니다.
    2. helloAfter2seconds 함수가 반환하는 Promise의 callback을 실행합니다.
    3. resolve() 혹은 reject() 실행 순간을 기다립니다.
    4. resolve 혹은 reject로 전달된 인자를 리턴합니다.
*/
```
### event loop
- [유튜브 설명영상](https://www.youtube.com/watch?v=8aGhZQkoFbQ) (한글자막 있어요)
- JS runtime은 single thread 입니다.
- 비동기 작업이 필요한경우, Web API에 위임하여 작업 완료 후 task queue에 callback을 전달합니다.
- call stack이 비어있는경우, event loop가 작동하며 task queue를 확인합니다.
- FIFO방식으로 task queue에 전달된 callback을 call stack에 올려 실행합니다.  
- event loop가 충분히 이해가 되셨다면, 실행컨텍스트(execution context) 학습을 추천합니다:)

### 

## 3. HTTP 통신
### http method
###  
[promise]: <>

