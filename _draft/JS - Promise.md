## 비동기처리

현재 실행중인 작업과는 별개의 작업을 수행하는 것
데이터를 받아오는 등의 실행이 완료될 때 까지 기다리지 않고 다음 코드를 먼저 수행

## 콜백 함수
함수의 매개변수로 다른 함수를 받아서 실행시킨다.
```javascript
function add(a, callback){
	return a + callback();
}

function callback(){
	return 20;
}

console.log(add(10, callback));
```


## Promise
비동기 작업의 완료/실패를 나타내는 객체

비동기 처리에 사용하는 여러번의 콜백함수 사용을 콜백 지옥이라고 부름
콜백 지옥을 개선하기 위해 사용하는 Promise
