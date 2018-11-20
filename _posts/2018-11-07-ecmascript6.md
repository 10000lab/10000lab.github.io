---
title: ECMAScript 6
key: 20181107
categories: [javascript]
tags: [ES6, ECMAScript 6, ECMAScript 2015]
---

## ES6 소개
자바스크립트는 2009년에 이크마스크립트 2009(ECMAScript 2009)가 발표된 후 2015년에 새로운 표준을 발표하게 됩니다. 새로 발표된 이크마스크립트 6(ECMAScript 6, ECMAScript 2015, ES6)는 프로그래밍을 좀 더 유연하고 편하게 할 수 있는 다수의 기능을 포함하고 있습니다. 현재 주요 자바스크립트 엔진에서는 대부분의 기능을 구현한 상태입니다. 각 브라우저별 구현 상태는 [ES6 호환 테이블](http://kangax.github.io/es5-compat-table/es6/)에서 확인 할 수 있습니다.
최근 상당한 인기를 끌고 있는 [Node.js](https://nodejs.org)를 배우기 위해서는 ES6에 새로 추가된 기능을 알아두는 것이 여러모로 유용합니다. 특히나 이미 자바스크립트로 프로그래밍을 하는데 익숙하신 분들은 ES6에 추가된 기능을 한번 쭉 훌터보는 것만으로 도움이 됩니다.
![ECMAScript6](/assets/images/ecmascript6.png)
이번 글에서는 ES6에 추가된 기능을 뽑아서 간략하게 설명을 하려고 합니다. 목록과 내용은 [Luke Hoban의 글](https://github.com/lukehoban/es6features)에서 가져왔고 새로 작성한 내용과 번역으로 옮겨 놓은 내용이 섞여 있습니다.
전체 ES6의 내용을 보시려면 [ES6 표준](http://www.ecma-international.org/ecma-262/6.0/)을 참고하세요.

계속해서 살펴 보게될 ES6의 기능은 아래와 같습니다.
- [화살표 함수](#화살표-함수)
- [클라스](#클라스)
- [확장 오브젝트 리터럴](#확장-오브젝트-리터럴)
- [템프릿 스트링](#템프릿-스트링)
- [디스트럭처링](#디스트럭처링)
- [기본 함수 파라미터](#기본-함수-파라미터)
- [함수 나머지 파라미터](#함수-나머지-파라미터)
- [전개 문법](#전개-문법)
- [let + const](#let--const)
- [for..of](#forof)
- [이터레이션 프로토콜](이터레이션-프로토콜)
- [제너레이터](#제너레이터)
- [유니코드](#유니코드)
- [모듈](#모듈)
- [모듈 로더](#모듈-로더)
- [Map, Set(WeakMap, WeakSet)](#map-setweakmap-weakset)
- [프록시](#프록시)
- [심볼](#심볼)
- [새로운 라이브러리 추가 기능](#새로운-라이브러리-추가-기능)
- [이진수, 팔진수](#이진수-팔진수)
- [프로미스](#프로미스)
- [리플렉트 API](#리플렉트-api)
- [테일 콜 옵티마이제이션](#테일-콜-옵티마이제이션)

## ES 6 새로운 기능
ES 6의 새로운 기능을 하나씩 살펴 보도록 하겠습니다.
### 화살표 함수
화살표 함수는 ES 6에 추가된 기능중에 단연 가장 많이 사용되는 기능입니다. 화살표 함수(Arrow function)를 사용하면 콜백 함수를 만들 때 간략하면서도 유용하게 사용할 수 있습니다. 예를 보겠습니다.
{% highlight javascript %}
const prices = [100, 200, 300];
// function callback
const changed = prices.map(function (p) {
  return p + 200;
});
{% endhighlight %}
배열에 있는 값을 받아서 200을 더한 후 새로운 배열을 생성하는 코드입니다. prices.map에 일반 함수를 인자로 넣었습니다.
간단히 보면 위처럼 `function (p)`부분을 `(p) =>` 변경하면 화살표 함수가 됩니다.
{% highlight javascript %}
const prices = [100, 200, 300];
const changed = prices.map((p) => {
  return p + 200;
});
{% endhighlight %}
단순하게 보면 function이라는 키워드를 지우고 파리미터 목록 다음에 화살표(`=>`)를 넣는다고 생각해도 되고요.

함수의 내용이 하나의 익스프레션으로 표현이 되는 경우는 중괄호를 없애고 좀 더 간략하게 사용할 수 있습니다.
{% highlight javascript %}
onst prices = [100, 200, 300];
let changed = prices.map(p => p + 200);
console.log(changed);
changed = prices.map((p, index) => p + 200 + index);
console.log(changed);
{% endhighlight %}
이때 익스프레션을 이벨류에이션한 값이 화살표 함수의 리턴 값이 됩니다.

일반 함수와 화살표 함수의 차이이면서 꼭 기억해야할 중요한 점은 `this`를 다루는 방식입니다.
함수를 콜백으로 전달할 때 this는 콜백 함수 자신을 의미합니다. 그래서 보통은 아래와 같이 self라고 하는 변수를 하나 생성해서 사용합니다.
{% highlight javascript %}
function HelloEs6() {
  this.name = "ES 6";
}
// const self = this 로 해서 콜백에서는 self를 사용
HelloEs6.prototype.sayHello = function () {
  const self = this;
  setTimeout( function () {
	console.log('Hello ' + self.name);
  }, 500);
};
const es6 = new HelloEs6();
es6.sayHello();
// Hello ES 6
{% endhighlight %}
`화살표 함수`  에서 `this`는 자신을 둘러싸고 있는 컨텍스트의 this를 의미합니다. 그래서 별도로 self라는 변수를 만들지 않고 바로 this를 사용할 수 있습니다. 아래 예에서 setTimeout의 파라이터인 화살표 함수에서 사용한 this는 `ES 6 Arrow`가 됩니다.
{% highlight javascript %}
function HelloEs6Arrow() {
  this.name = "ES 6 Arrow";
}
// this.name 을 사용
HelloEs6Arrow.prototype.sayHello = function () {
  setTimeout( ()  => {
    console.log('Hello ' + this.name);
  }, 500);
};
const es6Arrow = new HelloEs6Arrow();
es6Arrow.sayHello();
// Hello ES 6 Arrow
{% endhighlight %}

참고: [MDN Arrow Functions](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

### 클라스
ES6에서는 `class` 키워드를 사용해서 클라스를 생성하는 문법이 추가되었습니다. 이 방법은 전통적인 오브젝트 오리엔티드 언어들이 클라스를 정의하는 방식과 유사합니다. 아무래도 많은 개발자들이 좀 더 편하게 느낄 수 있는 방식이기도 합니다. 기억해야하는 점은 `class` 키워드를 사용하는 문법은 코드를 작성하는 문법적인 편리성만 제공한다는 것입니다. 내부 인터프리터는 전과 동일하게 포로토타입에 기반한 오브젝트 오리엔티드 방식을 유지하고 있습니다. 
상속이 가능하고 `super`를 사용해서 부모의 메써드 호출이 가능합니다. 객체화, 스태틱 메써드, 생성자 등을 지원합니다. 

ES5에서 프로토타입 문법을 사용해서 클라스를 생성하려면 function 키워드를 사용해서 함수를 생성하고 prototype에 프라퍼티를 추가하는 방식으로 클라스를 구현할 수 있습니다.

{% highlight javascript %}
function Product(productId, product, numReview) {
  this.productId = productId;
  this.product = product;
  this.numReview = numReview;
}

Product.prototype.getProduct = function () {
  return this.productId + ': ' + this.product;
};

Product.moreReview = function (product1, product2) {
  return (product1.numReview >= product2.numReview) ? product1: product2;
};

const product1 = new Product(1, 'iPhone', 50);
const product2 = new Product(2, 'Android', 53);
const moreReview = Product.moreReview(product1, product2);

console.log(moreReview.getProduct());
{% endhighlight %}

위의 코드와 동일한 코드를 `class` 키워드를 사용해서 작성면 아래와 같습니다.
{% highlight javascript %}
class Product {
  constructor (productId, product, numReview) {
    this.productId = productId;
    this.product = product;
    this.numReview = numReview;
  }

  getProduct () {
    return this.productId + ': ' + this.product;
  };

  static moreReview (product1, product2) {
    return (product1.numReview >= product2.numReview) ? product1: product2;
  };
}

const product1 = new Product(1, 'iPhone', 50);
const product2 = new Product(2, 'Android', 53);
const moreReview = Product.moreReview(product1, product2);

console.log(moreReview.getProduct());
{% endhighlight %}
Java나 C++등 다른 언어를 사용하시던 분이시라면 좀 더 친숙한 느낌을 받을 수 있습니다. 게다가 매번 prototype에 프라퍼티를 추가하는 형식으로 코딩을 하는 것 보다 훨씬 간결해 보여서 좋습니다. class를 상속받는 서브 클라스는 `extends` 키워드를 사용합니다.
{% highlight javascript %}
class ProductWithOS extends Product {
  constructor (productId, product, os, numReview) {
    super(productId, product, numReview);
    this.os = os;
  }
  getProduct () {
    return this.productId + ': ' + this.product + '(' + this.os + ')';
  };
}

const product1 = new ProductWithOS(1, 'iPhone 7', 'iOS', 50);
const product2 = new ProductWithOS(2, 'Galaxy S7', 'Android', 53);
const moreReview = ProductWithOS.moreReview(product1, product2);

console.log(moreReview.getProduct());
{% endhighlight %}
서브클라스는 constructor에서 `super()`를 통해서 부모 클라스의 생성자를 호출합니다. getProduct는 함수를 다시 정의하면서 오버라이드 되었습니다. `class`와 `extends`를 사용한 클라스 정의와 상속은 직관적이고 구조적인 오브젝트 오리엔티드 프로그래밍을 하려는 많은 개발자들에게 희소식이 아닐 수 없네요.

참고: [MDN Classes](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)

### 확장 오브젝트 리터럴
오브젝트 리터럴은 중괄호({})를 사용해서 오브젝트를 생성하는 문법입니다. 멤버의 이름인 키(key)와 값을 쌍으로 이루진 오브젝트 멤버를 저장할 수 있습니다. ES6에 추가된 확장 오브젝트 리터럴은 변수나 함수를 오브젝트 멤버로 저장하는 간편한 방법을 제공합니다. 멤버의 키를 바로 지정하는 대신에 계산된 값으로 지정할 수 있습니다. 세터와 게터를 사용해서 오브젝트 프라퍼티를 읽거나 저장할 수 있습니다.

아래 예에서와 같이 newProduct 오브젝트를 생성시 `const newProduct = {product, numReview}`로 지정할 수 있습니다. 일반적인 오브젝트 문법으로 변경을 하면 `const newProduct = {product: product, numReview: numReview}`와 같습니다. ES5에서라면 오브젝트 프라퍼티 이름을 지정해 주고 `:`를 찍고 값을 넣어줘야합니다. ES6에서는 오브젝트 리터럴인 중괄호에 변수를 넣으면 변수의 이름으로 키를 갖는 프라퍼티를 생성해 줍니다.
{% highlight javascript %}
const product = 'iPhone';
const numReview = 5;
const newProduct = {product, numReview};
console.log(newProduct);
{% endhighlight %}

프라퍼티와 마찬가지로 확장 오브젝트 리터럴로 함수도 추가할 수 있습니다. 역시 함수 이름과 동일한 키로 멤버를 생성해 줍니다.
{% highlight javascript %}
const name = 'iPhone';
const price = 100;

const newProduct = {
  getProductName() {
    return name;
  },
  getPrice() {
    return price;
  }
};

//ES5로 작성한 newProduct 오브젝트
const newProductES5 = {
  getProductName: function () {
    return name;
  },
  getPrice: function() {
    return price;
  }
};
{% endhighlight %}

ES5에서는 오브젝트의 프라퍼티 이름으로 계산된 값을 넣기 위해서는 오브젝트를 생성한 후 괄호([])를 사용해서 이름을 지정할 수 있었습니다. ES6의 오브젝트 리터럴에서는 오브젝트 생성시 바로 계산된 값으로 이름을 지정하는 것이 가능해졌습니다.
{% highlight javascript %}
const name = 'iPhone';
const product = {
  [name + '_color']: 'blue',
  [name + '_screen']: '4.7 inch'
};
console.log(product.iPhone_color);
console.log(product.iPhone_screen);
{% endhighlight %}
게터(getter)와 세터(setter)는 오브젝트의 멤버함수를 프라퍼티처럼 사용할 수 있도록 해 줍니다. 오브젝트로부터 가져와야하는 데이터가 연산을 거친 후에 받을 수 있는 것이면 멤버 함수로 구현을 하게 됩니다. 오브젝트를 사용하는 쪽에서는 이 함수로부터 리턴 받은 값을 변수에 저장된 값과 비료를 하려면 `obj.productFullName() === 'Android:Galaxy S7'`처럼 코드를 작성해야합니다. 게다가 값을 넣을 때는 다른 함수를 사용해서 `obj.setProductFullName('iOS:iPhone 7')` 이렇게 해야합니다. 함수 이름을 정하는 규칙을 정해서 할 수는 있지만 직관적이지 않고 번거롭기만 합니다. 아래는 ES6를 사용한 예입니다.
{% highlight javascript %}
const product = {
  os : 'iOS',
  name: 'iPhone 7',
  get productFullName () {
    return this.os + ':' + this.name;
  },
  set productFullName (fullName) {
    let names = fullName.split(':');
    this.os = names[0];
    this.name = names[1];
  }
};

console.log(product.productFullName);
product.productFullName = 'Android:Galaxy S7';
console.log(product.os);
console.log(product.name);
{% endhighlight %}

참고: [MDN Grammar and types: Object literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Grammar_and_types#Object_literals)

### 템프릿 스트링
ES6에는 편리하면서도 강력한 기능을 제공하는 스트링 리터럴이 추가 되었습니다. 이 문법은 기존의 작은 따옴표(')나 큰 따옴표(") 대신에 백틱(\`)을 사용합니다. 기존 스트링 리터럴과 탈리 템프릿 스트링 리터럴은 변수나 익스프레션을 스트링 안에 넣을 수 있습니다. `$(expression)`처럼 사용하면 이 부분을 실제 변수의 값이나 익스프레션의 결과로 치환을 해 줍니다. 마치 탬플릿 언어를 사용하는 것처럼 말이죠. 또한 여러 라인에 걸쳐서 스트링을 만들 수도 있습니다. 
{% highlight javascript %}
const product = {
  os : 'iOS',
  name: 'iPhone 7',
  get productFullName () {
    // ES6 Template String
    return `${this.os}: ${this.name}`;
  },
  set productFullName (fullName) {
    let names = fullName.split(':');
    this.os = names[0];
    this.name = names[1];
  }
};

console.log(product.productFullName);
product.productFullName = 'Android:Galaxy S7';
// Multi lines
const result =`   *** Product ***
   * OS : ${product.os}
   * Name : ${product.name}
   * FullName: ${product.os + ':' + product.name}`;
console.log(result);
{% endhighlight %}
템플릿 스트링 관련해서 추가로 알고 넘어갈 내용으로는 태그드 템플릿이 있습니다. 템플릿 스트링의 내용을 인자로 받아서 필요한 연산을 한 후 스트링이나 다른 오브젝트를 리턴하는 함수를 구현합니다. 그리고 이 함수를 템프릿 스트링 앞에 배치합니다. 함수의 인자로는 `$(expression)` 앞 뒤로 나뉘는 스트링을 배열로 갖는 파라미터와 각 익스프레션이 순대대로 들어갑니다.
{% highlight javascript %}
const product = {
  os : 'iOS',
  name: 'iPhone 7'
};

function addCompany(strings, nameExp, osExp) {
  const str0 = strings[0]; // "The "
  const str1 = strings[1]; // " OS is "
  let company;
  if (osExp === 'iOS')
    company = 'Apple';
  else
    company = 'Samsung';

  return `${str0}${nameExp}${str1}${osExp} and made by ${company}.`;
}
//${product.name}과 ${product.os}로 나뉜 2개의 string
const taggedString = addCompany`The ${product.name} OS is ${product.os}`;
console.log(taggedString);
{% endhighlight %}

참고: [MDN Template Strings](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/template_strings)

### 디스트럭처링
디스트럭처링은 여러 값을 패킹(pack)에서 가지고 있는 변수에서 언패킹(unpack)을 해서 다른 변수에 값을 할당하는 문법입니다. 정확히는 디스트럭처링 어사인먼트라고 해야합니다. 배열에서는 값을 오브젝트에서는 프라퍼티를 언패킹할 수 있습니다. 먼저 배열 디스트럭처링을 살펴 보겠습니다. 
변수를 선언하면서 바로 할당하거나 이미 선언된 변수에 디스트럭처링을 할 수 있습니다. 배열의 갯수가 할당 받을 변수의 갯수보다 적은 경우에 기본 값을 지정할 수 있습니다. 배열을 언패킹할때 앞에 일부는 각각 변수에 할당하고 나머지는 하나의 변수에 배열로 저장할 수 있습니다. 이는 나머지 패턴(rest pattern)을 통해서 지정할 수 있습니다.
{% highlight javascript %}
const phones = ['iPhone', 'Android', 'featurePhone', 'Others'];
// 변수를 선언하면서 바로 할당
let [phone1, phone2, phone3, phone4] = phones;
console.log(phone1); // iPhone
console.log(phone2); // Android
console.log(phone3); // featurePhone
console.log(phone4); // Others

// 이미 선언된 변수에 할당
let first, second, others;
[first, second] = phones; // 2개 이후 나머지는 무시됨.
console.log(first); // iPhone
console.log(second); // Android

[phone1, phone2, phone3='Unknown'] = [second, first];
console.log(phone1); // Android
console.log(phone2); // iPhone
console.log(phone3); // Unknown

// 나머지 패턴
[first, second, ...others] = phones;
console.log(phone1); // iPhone
console.log(phone2); // Android
console.log(others); // ['featurePhone', 'Others']
{% endhighlight %}

오브젝트 디스트럭처링도 변수를 선언하면서 혹은 이미 선언된 변수에도 가능합니다. 특별히 변수 선언 후에 디스트럭처링 저장을 하는 경우에는 꼭 압뒤로 (....)처럼 괄호를 넣어 주어야합니다. 라인의 시작에 있는 중괄호({)는 블럭의 시작으로 해석이 되기때문입니다. 
{% highlight javascript %}
const iphone = {
  name: 'iPhone 7',
  os: 'iOS'
};
const android = {
  name: 'Galaxy S7',
  os: 'Android'
};

let {name, os} = iphone;
console.log(name);  // iPhone 7
console.log(os);    // iOS

// 괄호를 꼭 해 줘야합니다.
({name, os} = android);
console.log(name);   // Galaxy S7
console.log(os);     // Android
{% endhighlight %}
디스트럭처링을 하면서 바로 다른 변수에 저장을 할 수도 있습니다. 매칭되는 키가 없는 경우에 사용할 기본 값을 지정할 수도 있습니다.
{% highlight javascript %}
const iphone = {
  name: 'iPhone 7',
  os: 'iOS'
};

// 다른 이름으로 저장
let {name: phoneName, os: phoneOS} = iphone;
console.log(phoneName);  // iPhone 7
console.log(phoneOS);    // iOS

// 기본값 지정
let {name, os, color='Blue'} = iphone;
console.log(name);   // iPhone 7
console.log(os);     // iOS
console.log(color);  // Blue
{% endhighlight %}

참고: [MDN Destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

### 기본 함수 파라미터
함수를 정의할 때 파라미터에 기본 값을 지정할 수 있습니다. 함수가 호출될 때 파라미터를 넣지 않았거나, `undefined`로 값이 들어오면 기본 값으로 파라미터가 세팅됩니다. ES5를 사용할 때는 파라미터에 기본 값을 넣기 위해서는 파라미터를 확인하고 기본 값을 넣는 코드를 추가해야했습니다. 파라미터를 입력하지 않으면 파라미터가 `undefined`가 되기때문에 `param === undefined`처럼 비교하여 기본 값을 지정하는 코드를 사용했습니다. 간단하게는 `||`를 사용해서 기본 값을 지정하기도 했죠.
{% highlight javascript %}
function totalPrice(price, itemCount) {
  // ES5 : itemCount 가 undefined 이면 1
  itemCount = itemCount || 1;
  return price * itemCount;
}
console.log(totalPrice(100, 2)); // 100 * 2
console.log(totalPrice(100));    // 100 * 1
{% endhighlight %}
기본 함수 파라미터를 사용하면 함수의 파라미터를 정할 때 기본 값을 지정할 수 있습니다.
{% highlight javascript %}
function totalPrice(price, itemCount=1) {
  return price * itemCount;
}

console.log(totalPrice(100, 2)); // 100 * 2
console.log(totalPrice(100));    // 100 * 1
{% endhighlight %}
기본 파라미터로는 숫자뿐 아니라 스트링, 함수, 디스트럭처링 된 변수 등이 들어갈 수 있습니다.

{% highlight javascript %}
//함수로 지정
function totalPrice(price, itemCount=defaultCount()) {
  return price * itemCount;
}
function defaultCount() {
  return 1;
}
console.log(totalPrice(100));    // 100 * 1

//오브젝트 디스트럭처링, 배열 디스트럭처링
function setPhone({name, os} = {name: 'iPhone 7', os: 'iOS'}, [price, count] = [200, 2]) {
  //오브젝트 확장 리터럴로 세팅
  return {
    name, os, price, count
  };
}
console.log(setPhone());
{% endhighlight %}
참고: [Default parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)

### 함수 나머지 파라미터
함수 파라미터의 이름 앞에 `...`를 붙이면 파라미터의 개수에 관계없이 지정된 이름에 배열로 담아 줍니다. 나머지 파라미터 앞에 있는 파라미터를 제외한 나머지 파라미터만 배열로 주기때문에 이름도 나머지 파라미터입니다.
{% highlight javascript %}
function createCart(id, name='Unknown', ...products) {
  const count = products.length;
  return {
    id, name, count,
    products
  }
}
let cart = createCart(1, 'Good Customer', 'iPhone 7', 'Galaxy S7', 'Charger');
console.log(cart);
//{"id":1,"name":"Good Customer","count":3,"products":["iPhone 7","Galaxy S7","Charger"]}

//파라미터가 주어지지 않아도 나머지 파라미터는 빈 배열로 생성됩니다.
cart = createCart(2);
console.log(cart);
//{"id":2,"name":"Unknown","count":0,"products":[]}

//나머지 파라미터를 디스트럭처링하는 예제입니다.
function createCartOnly2(id, name='Unknown', ...[product1, product2]) {
  const count = 2;
  return {
    id, name, count,
    products2: [product1, product2]
  }
}
//디스트럭처링 갯수를 넘는 파라미터는 무시됩니다.
cart = createCartOnly2(3, 'New Customer', 'iPhone 7', 'Galaxy S7', 'Charger');
console.log(cart);
//{"id":3,"name":"New Customer","count":2,"products2":["iPhone 7","Galaxy S7"]}
{% endhighlight %}
나머지 파라미터를 보면 `arguments` 오브젝트와 기능이 유사한 것처럼 보입니다. 하지만 중요한 차이점이 있습니다.
- 나머지 파라미터는 파라미터 이름이 지정되지 않은 나머지 파라미터만 포함하고 있습니다. 반면 arguments는 모든 파라미터를 포함하고 있습니다.
- 나머지 파라미터는 자바스크립트 `배열(Array)`입니다. 따라서 배열의 여러가지 메써드를 사용할 수 있습니다. `sort`, `map`, `forEach` 등을 말입니다. 하지만 arguments는 배열이 아니기때문에 배열이 가진 기능을 사용할 수 없습니다.
아래 예제는 `arguments`를 사용해서 createCart()를 구현한 예입니다.
{% highlight javascript %}
function createCart(id, name='Unknown') {
  const count = arguments.length - 2;
  const args = Array.from(arguments).splice(2);
  return {
    id, name, count,
    products: args
  }
}

let cart = createCart(1, 'Good Customer', 'iPhone 7', 'Galaxy S7', 'Charger');
console.log(cart);
//{"id":1,"name":"Good Customer","count":3,"products":["iPhone 7","Galaxy S7","Charger"]}
{% endhighlight %}
참고: [Rest parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)

### 전개 문법
전개 문법(spread syntax)은 배열이나 스트링처럼 이터레이션이 가능한 데이터를 개별 데이터로 펼쳐서(전개, spread) 전달해 주는 기능입니다. 이터러블한 변수 이름 앞에 `...`를 붙여 사용합니다. 함수 인자의 경우 `values`라는 배열을 함수 인자로 `someFunction(...values)`처럼 넣을 수 있습니다. 또한 배열이나 오브젝트를 생성할 때도 사용할 수 있습니다.
{% highlight javascript %}
// 1. 함수 전체 인자로 사용한 예
function createCart(id, name, count) {
  return {
    id, name, count
  }
}
const product = [1, 'iPhone 7', 3];
// Spread Syntax
let cart = createCart(...product);
console.log(cart);
//{"id":1,"name":"iPhone 7","count":3}

// 2. 함수 일부 인자로 사용한 예
const selection = ['Galaxy S7', 5];
cart = createCart(2, ...selection);
console.log(cart);
//{"id":2,"name":"Galaxy S7","count":5}
{% endhighlight %}
배열 리터럴에서도 전개 문법을 사용할 수 있습니다. 배열의 복사나 여래개의 배열을 하나로 합치는 데도 이용할 수 있습니다.
{% highlight javascript %}
const product1 = ['iPhone 7', 'iOS'];
const product2 = ['Galaxy S7', 'Android'];

//배열 리터럴에서 사용
const products = ['iPhone X', 'iOS', ...product1, 'Galaxy 9', 'Android'];
console.log(products);
//["iPhone X","iOS","iPhone 7","iOS","Galaxy 9","Android"]

//배열 복사
const copied = [...products];
console.log(copied);
//["iPhone X","iOS","iPhone 7","iOS","Galaxy 9","Android"]

//2개의 배열을 연결할 때
const two_products = [...product1, ...product2];
console.log(two_products);
//["iPhone 7","iOS","Galaxy S7","Android"]
{% endhighlight %}
오브젝트도 전개 문법을 사용할 수 있습니다. 배역의 예와 마찬가지고 생성, 복사, 합치는 것이 가능합니다. 주의할 점은 오브젝트의 키가 동일한 경우 앞에 있는 오브젝트의 키의 내용은 뒤에 있는 오브젝트의 것으로 덮어 써집니다. 아래 예제에서 결과를 보실 수 있습니다.
{% highlight javascript %}
const product1 = {name: 'iPhone 7', os: 'iOS'};
const product2 = {name: 'Galaxy S7', type: 'Smart Phone'};

//오브젝트 리터럴에서 사용
const products = {id: 3, ...product1};
console.log(products);
//{"id":3,"name":"iPhone 7","os":"iOS"}

//오브젝트 복사
const copied = {...product2};
console.log(copied);
//{"name":"Galaxy S7","type":"Smart Phone"}

//2개의 오브젝트를 합칠 때
const two_products = {...product1, ...product2};
console.log(two_products);
//{"name":"Galaxy S7","os":"iOS","type":"Smart Phone"}
{% endhighlight %}
참고: [Spread Operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)

### let + const
`let`은 블록 스코프를 갖는 변수를 정의할 수 있습니다. 글로벌 스코프이거나 함수 단위 스코프를 갖는 `var`와는 스코프에서 차이가 있습니다. var는 같은 글로벌 혹은 함수 내부에서도 중괄호({ })로 구분되는 블록을 벗어나도 변수가 유지됩니다. 하지만 let은 블록 안에서만 유효합니다.
{% highlight javascript %}
function priceCheck() {
  let price1 = 200;
  var price2 = 200;

  if (true) {
    let price1 = 300;
    var price2 = 300;

    console.log(price1);  //300
    console.log(price2);  //300
  }

  //if 블록 안의 price1과 밖의 price1은 서로 다른 변수 입니다.
  console.log(price1);  //200
  console.log(price2);  //300
}
priceCheck();
{% endhighlight %}
`const`는 스코프는 let과 동일한 범위를 갖는 상수를 정의할 수 있습니다. 여기서 상수는 const로 정의된 변수에 다른 것을 다시 어사인할 수 없다는 것입니다. 하지만 이 const 변수가 레퍼런스하고 있는 오브젝트의 내용은 얼마든지 변경이 가능합니다.
{% highlight javascript %}

const PI = 3.14;
//아래 코드는 Uncaught TypeError: Assignment to constant variable 를 발생합니다.
//PI = 4;

const product = {
  name: 'Galaxy S7',
  os : 'Android'
};
// product가 레퍼런스를 하고 있는 오브젝트 내용을 변경하는 것은 가능합니다.
product.name = 'Galaxy 9';
{% endhighlight %}

참고: [let statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let), [const statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)

### for...of 
`for...of` 스테이트먼트를 사용하여 이터러블한 오브젝트를 루프를 돌 수 있습니다. String, Array와 Array처럼 동작하는 arguments나 NodeList, TypedArray, Map, Set 등의 오브젝트에 사용할 수 있습니다. 추가로 사용자 정의 이터러블 오브젝트에서 사용 가능합니다.(사용자 정의 이터러블 프로토콜은 다음에 설명합니다.)

{% highlight javascript %}
let iterable = [10, 20, 30];
for (let vaule of interable) {
  value += 1;
  colsole.log(value);
}
// 11
// 21
// 31
{% endhighlight javascript %}
참고 : [MDN for...of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)

### 이터레이션 프로토콜
자바스크립트 `이터레이션 프로토콜`을 사용하여 오브젝트를 이터레이션이 가능하게 할 수 있습니다. 이터레이션이 가능한 오브젝트가 되면 for...of 스테이트먼트에서 사용 가능하게 됩니다. 
이터러블한 오브젝트가 되기 위해서는 이터레이터 함수를 리턴하는 메써드를 구현해야합니다. `@@interator`를 키로 하는 메써드를 구현하면 됩니다. 키는 `@@interator`를 직접 사용하는 대신 `Symbol.interator`로 사용하면 됩니다.
이터레이터는 `next()` 메써드를 구현합니다. `next()` 메써드는 `done`과 `value` 프라퍼티를 갖는 오브젝트를 리턴합니다.
{% highlight javascript %}
let fibonacci = {
  [Symbol.iterator]() {
    let pre = 0, cur = 1;
    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        return { done: false, value: cur }
      }
    }
  }
}

for (var n of fibonacci) {
  // truncate the sequence at 1000
  if (n > 1000)
    break;
  console.log(n);
}
{% endhighlight %}

참조: [MDN for...of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)

### 제너레이터
간단히 말해서 제너레이터는 `function*`과 `yield`를 사용해서 이터레이터를 구현한 것과 같습니다. 할수를 정의할 때 function*를 사용하면 제너레이터가 리턴됩니다. 이터레이터의 `next`외에 추가로 `return`와 `throw` 메써드를 호출할 수 있습니다. next는 yield 익스프레션 값을 리턴합니다.return은 파라미터로 주어진 값을 리턴하면서 제너레이터를 종료합니다. throw는 제너레이터에 에러를 전달합니다.

{% highlight javascript %}
var fibonacci = {
  [Symbol.iterator]: function*() {
    var pre = 0, cur = 1;
    for (;;) {
      var temp = pre;
      pre = cur;
      cur += temp;
      yield cur;
    }
  }
}

for (var n of fibonacci) {
  // truncate the sequence at 1000
  if (n > 1000)
    break;
  console.log(n);
}
{% endhighlight %}

참조: [MDN Generator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)

### 유니코드
전체 유니코드를 지원합니다. 스트링 안에서 유니코드를 표현할 수 있는 새로운 방식이 추가되었습니다. RegExp에는 코드 포인트를 처리하기 위해 `u` 모드가 추가되었습니다. 추가로 21bit 코드 포인트 레벨에서 스트링을 처리하는 API가 추가되었습니다.
{% highlight javascript %}
// same as ES5.1
"𠮷".length == 2

// new RegExp behaviour, opt-in ‘u’
"𠮷".match(/./u)[0].length == 2

// new form
"\u{20BB7}"=="𠮷"=="\uD842\uDFB7"

// new String ops
"𠮷".codePointAt(0) == 0x20BB7

// for-of iterates code points
for(var c of "𠮷") {
  console.log(c);
}
{% endhighlight %}

참조: [MDN RegExp.prototype.unicode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/unicode)

### 모듈
콤포넌트를 정의할 수 있도록 모듈을 지원합니다. 

{% highlight javascript %}
// lib/math.js
export function sum(x, y) {
  return x + y;
}
export var pi = 3.141593;
{% endhighlight %}

{% highlight javascript %}
// app.js
import * as math from "lib/math";
alert("2π = " + math.sum(math.pi, math.pi));
{% endhighlight %}

{% highlight javascript %}
// otherApp.js
import {sum, pi} from "lib/math";
alert("2π = " + sum(pi, pi));
{% endhighlight %}

추가적으로 `export default`와 `import *`:

{% highlight javascript %}
// lib/mathplusplus.js
import * from "lib/math";
export var e = 2.71828182846;
export default function(x) {
    return Math.log(x);
}
{% endhighlight %}
{% highlight javascript %}
// app.js
import ln, {pi, e} from "lib/mathplusplus";
alert("2π = " + ln(e)*pi*2);
{% endhighlight %}

참고: [import statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import), [export statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)

### 모듈 로더
모듈 로더는 아래 기능을 지원합니다.:
- Dynamic loading
- State isolation
- Global namespace isolation
- Compilation hooks
- Nested virtualization

기본 모듈 로더는 설정이 가능합니다. 또한 별도로 구분되어 있거나 정해진 컨텍스트 내에서 새 로더를 구성하는 것도 가능합니다.

{% highlight javascript %}
// Dynamic loading – ‘System’ is default loader
System.import('lib/math').then(function(m) {
  alert("2π = " + m.sum(m.pi, m.pi));
});

// Create execution sandboxes – new Loaders
var loader = new Loader({
  global: fixup(window) // replace ‘console.log’
});
loader.eval("console.log('hello world!');");

// Directly manipulate module cache
System.get('jquery');
System.set('jquery', Module({$: $})); // WARNING: not yet finalized
{% endhighlight %}

### Map, Set(WeakMap, WeakSet)
일반적인 알고리즘에서 많이 사용되는 데이터 구조가 추가되었습니다. WeakMap과 WeakSet은 weak reference를 지원해서 메모리 릭을 방지할 수 있습니다.

{% highlight javascript %}
// Sets
var s = new Set();
s.add("hello").add("goodbye").add("hello");
s.size === 2;
s.has("hello") === true;

// Maps
var m = new Map();
m.set("hello", 42);
m.set(s, 34);
m.get(s) == 34;

// Weak Maps
var wm = new WeakMap();
wm.set(s, { extra: 42 });
wm.size === undefined

// Weak Sets
var ws = new WeakSet();
ws.add({ data: 42 });
// Because the added object has no other references, it will not be held in the set
{% endhighlight %}

참조: [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map), [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set), [WeakMap](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap), [WeakSet](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakSet)

### 프록시
오브젝트에 새로 정의된 기능(함수)를 수행할 수 있도록 합니다. 원래 오브젝트의 동작을 중간에 가로채거나, 오브젝트를 가상화, 로깅을 하는데 사용할 수 있습니다.

{% highlight javascript %}
// Proxying a normal object
var target = {};
var handler = {
  get: function (receiver, name) {
    return `Hello, ${name}!`;
  }
};

var p = new Proxy(target, handler);
p.world === 'Hello, world!';
{% endhighlight %}

{% highlight javascript %}
// Proxying a function object
var target = function () { return 'I am the target'; };
var handler = {
  apply: function (receiver, ...args) {
    return 'I am the proxy';
  }
};

var p = new Proxy(target, handler);
p() === 'I am the proxy';
{% endhighlight %}

프록시에서 수행 가능한 모든 목록은 아래와 같습니다.:

{% highlight javascript %}
var handler =
{
  get:...,
  set:...,
  has:...,
  deleteProperty:...,
  apply:...,
  construct:...,
  getOwnPropertyDescriptor:...,
  defineProperty:...,
  getPrototypeOf:...,
  setPrototypeOf:...,
  enumerate:...,
  ownKeys:...,
  preventExtensions:...,
  isExtensible:...
}
{% endhighlight %}

참고: [MDN Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

### 심볼
오브젝트의 키로 사용될 수 있는 기본 타입입니다. 오브젝트의 키로 `string`을 사용할 수도 있지만 `symbol`도 사용할 수 있게 추가되었습니다. 파라미터로 `description`을 추가할 수 있지만 필수는 아닙니다. 


{% highlight javascript %}
var MyClass = (function() {

  // module scoped symbol
  var key = Symbol("key");

  function MyClass(privateData) {
    this[key] = privateData;
  }

  MyClass.prototype = {
    doStuff: function() {
      ... this[key] ...
    }
  };

  return MyClass;
})();

var c = new MyClass("hello")
c["key"] === undefined
{% endhighlight %}

참고: [MDN Symbol](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)


### 새로운 라이브러리 추가 기능
Math + Number + String + Array + Object APIs
라이브러리에 새로운 기능이 다수 추가되었습니다.

{% highlight javascript %}
Number.EPSILON
Number.isInteger(Infinity) // false
Number.isNaN("NaN") // false

Math.acosh(3) // 1.762747174039086
Math.hypot(3, 4) // 5
Math.imul(Math.pow(2, 32) - 1, Math.pow(2, 32) - 2) // 2

"abcde".includes("cd") // true
"abc".repeat(3) // "abcabcabc"

Array.from(document.querySelectorAll('*')) // Returns a real Array
Array.of(1, 2, 3) // Similar to new Array(...), but without special one-arg behavior
[0, 0, 0].fill(7, 1) // [0,7,7]
[1, 2, 3].find(x => x == 3) // 3
[1, 2, 3].findIndex(x => x == 2) // 1
[1, 2, 3, 4, 5].copyWithin(3, 0) // [1, 2, 3, 1, 2]
["a", "b", "c"].entries() // iterator [0, "a"], [1,"b"], [2,"c"]
["a", "b", "c"].keys() // iterator 0, 1, 2
["a", "b", "c"].values() // iterator "a", "b", "c"

Object.assign(Point, { origin: new Point(0,0) })
{% endhighlight %}

참고: [Number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number), [Math](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math), [Array.from](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from), [Array.of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/of), [Array.prototype.copyWithin](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/copyWithin), [Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)

### 이진수, 팔진수
이진수(`b`)와 팔진수(`o`)가 뉴머릭 리터럴에 추가되었습니다.

{% highlight javascript %}
0b111110111 === 503 // true
0o767 === 503 // true
{% endhighlight %}

### 프로미스
어싱크 프로그래밍에 사용할 수 있는 라이브러리입니다. 
{% highlight javascript %}
function timeout(duration = 0) {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, duration);
    })
}

var p = timeout(1000).then(() => {
    return timeout(2000);
}).then(() => {
    throw new Error("hmm");
}).catch(err => {
    return Promise.all([timeout(100), timeout(200)]);
})
{% endhighlight %}

More info: [MDN Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

### 리플렉트 API
런타임 레벨에서 오브젝트에 리플렉션 API를 제공합니다. 프록시의 트랩 목록과 동일한 API를 제공합니다. 프록시를 구현할 때 유용하게 사용할 수 있습니다.

{% highlight javascript %}
// No sample yet
{% endhighlight %}

참고: [MDN Reflect](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect)

### 테일 콜 옵티마이제이션
자바스크립트 인터프리터 구현과 관련된 사항입니다. 함수의 리턴하기 전 마지막 해야할 일이 함수를 이벨류에이션하는 것이라면 ES6 인터프리터는 테일 콜 옵티마이제이션을 할 수 있습니다. 마지막 호출되는 함수는 로컬 변수를 따로 가지고 있지 않아야 합니다. 이 경우 별도의 스택 프레임을 만들지 않기때문에 메모리 사용량이 줄어들게 됩니다. 특히나 리컬시브 함수인 경우에는 스택 오버플로우 에러를 방지할 수 있습니다.

{% highlight javascript %}
function factorial(n, acc = 1) {
    'use strict';
    if (n <= 1) return acc;
    return factorial(n - 1, n * acc);
}

// Stack overflow in most implementations today,
// but safe on arbitrary inputs in ES6
factorial(100000)
{% endhighlight %}

참고: [ES6 tail calls](http://benignbemine.github.io/2015/07/19/es6-tail-calls/)

---


