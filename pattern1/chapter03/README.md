JavaScript Pattern
======================

# 3. 리터널과 생성자
javascript내에서 제공하는 Object()나 Array()등의 내장 생성자 함수보다는 리터널 표기법을 사용해서 개발하는 것이 더 좋다. 왜 리터널이 좋은지? 리터널이 무엇인지?에 대해서 알아보자
## 3.1 객체 리터널
### 3.1.1 객체 리터널 문법

1. 객체를 중괄호('{' 와 '}'로 감싼다.)
2. 객체안의 property들을 comma(',')로 분리한다.
3. property key와 propery value는 colon(':')으로 분리한다.
4. 객체를 변수에 할당할때 닫는 중괄호 뒤에 세미콜론을 빼먹지 않도록 한다.

### 3.1.2 객체 리터널 사용
객체 리터널은 위에 설명된 문법을 사용해서 생성할 수 있다. 반드시 빈객체({})에서 시작할 필요가 없다. 선언 시점에서 property를 넣을수 있다. 참고로 {}는 실제 비어있는게 아니다. undefined를 제외한 나머지는 모두 값이 있는것이며 {} 객체조차도 Object.prototype에서 상속받은 property들과 method들이 있다.
    
    let user = {}
	user.name = 'hang';
	user.getName = () => {
		return user.name;
	}
	console.log(user.getName());

### 3.1.3 생성자 함수 사용
javascript는 객체를 생성할 수 있는 Object를 제공한다. 비슷한것으로 Array, Number등이 있다.

    // 빈객체 생성
	let obj1 = new Object();
	console.log(obj1.constructor === Object);	// true

	// 숫자객체 생성
	let obj2 = new Object(1);
	console.log(obj2.constructor === Number);	// true
	console.log(obj2.toFixed(2));	// true

Object를 이용해서 객체를 생성할 수 있는데 왜 쓰지 말라는걸까? 왜 리터널을 쓰지? 라고 생각 할 수 있다. 그것은 다음과 같다.

1. 생성자 함수보다 리터널이 코드량이 작다.
2. 생성자 함수는 현재 역역에서 동일이름의 누군가가 만든 생성자가 있을 수 있기 때문에 Object()를 호출 한 위치부터 전역 Object까지 탐색한다.
3. Object는 인자를 받는데, 인자에 따라서 생각과 다른 객체가 반환된다.

이런 장점과 생성자 함수의 단점들이 있으니 객체 리터널을 쓰는게 좋다.
## 3.2 사용자 정의 생성자 함수
아래와 같이 객체 리터널이나 생성자 함수를 사용하지 않고 직접 생성자 함수를 만들어서 객체를 생성이 가능하다.

    var User = function(name) {
		this.name = name;
		this.getName = function() {
			return this.name;
		};
	};

	var homi = new User('home');
	console.log(homi.getName());

homi를 생성할대처럼 new와 함깨 생성자 함수를 호출하면 실제적으로 아래와 같은 작업을 진행한다.

1. this라는 빈객체를 생성하고 해당함수의 prototype을 상속 받는다. (var this = Object.create(User.prototype);)
2. this 객체에 property, method를 추가한다. (this.name = name; this.getName = function() { return this.name; })
3. 명시적으로 다른 객체를 return하는 문이 없으면 this객체를 리턴한다. (return this;)

차후 자세히 정리하겠지만 new를 통해서 객체를 생성하는 실제 저 객체 안에는 property나 method가 생성이된다. 그리고 각각 메모리 공간을 갖게 되는데, getName같이 객체만을 위한 것이 아닌 모든객체가 공통적으로 쓰는것들은 prototype으로 빼주는게 좋다. 그러면 실제 객체를 생성할때마다 getName의 별도 공간이 필요없이 prototype체인으로 순환하면서 constructor안에서 선언된 getName을 모든 객체들이 공유해쓰게 된다. 이러면 객채별 메모리낭비없이 사용할 수 가 있다.

    User.prototype.zEngine = function(name) {
		return 'Start zEngine';
	}
	console.log(homi.zEngine());

### 3.2.1 new를 통한 생성자의 반환 값
생성자 함수에서 별도 return문이 없으면 this객체를 리턴한다 했었다. return 문을 만들면 그 return에 대한 객체 값이 넘어간다. 그런데 함수는 꼭 return으로 객체를 넘겨주어야 하며 문자열이나, true/false값을 넘기면 에러는 없지만 this객체가 반환된다.

    let Car = function(name='s991') {
		this.name = name;

		let that = {};
		that.name = 'G181';

		return that;
	}

	let zKey = new Car('C991');
	console.log(zKey.name);	// G181

위에서 이야기 한데로 return을 that이 아닌 문자열이나 불린값을 넘겨보자 그럼 this객체에 선언된 C991이 출력이 된다.

    let Car = function(name='s991') {
		this.name = name;

		let that = {};
		that.name = 'G181';

		return 'tt';
	}

	let zKey = new Car('C991');
	console.log(zKey.name);	// C991
