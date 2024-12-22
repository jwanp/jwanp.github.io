---
title: '[Javascript] Sieve of Eratosthenes'
date: 2024-12-21T17:06:52+09:00
draft: false
tags: ['javascript', 'coding-test']
categories: ['Tech']
featuredImage: /images/tech/javascript-coding-test/소수테이블.png
---

_Made by 박주환_

{{< line_break >}}

## 함수 및 개념 정리

문제를 보기전에 필요한 함수들을 살펴 보겠습니다. 기초적인 내용도 포함되어 있지만 다시한번 개념을 명확하게 하고 모르던 함수가 있다면 참고 하시면 좋습니다.

지금 당장 눈에 들어오지 않는다면 문제를보다가 궁금하면 다시 위로 올라와서 공부하는 것이 더 도움이 될 수도 있습니다.

### **for loop** 진행 순서: **항상 조건문을 들렸다가 실행문을 실행합니다.**

```js
for(1.초기화; 2.조건문; 4.증감식){
	3.실행문
}
```

1. 초기화
2. **조건문: true**
3. 실행문
4. 증감식
5. **조건문: true**
6. 실행문
7. 증감식
8. **조건문: false -> break**

{{< line_break >}}

```js
// 예시
for (let i = 2; i <= limit; i++) {
    실행문;
}
```

{{< line_break >}}

### **배열 생성 및 초기화하는 방법** : Array().fill()

```js
변수 = new Array(배열 크기).fill(초기값)

// 예시)
const output = new Array(limit +1).fill(true)
```

{{< line_break >}}

### **제곱함수**: Math.pow()

```js
// base: 밑, exponent: 지수, power: 제곱
Math.pow(base, exponent);

// 에시)
Math.pow(2, 4); // 2 x 2 x 2 x 2 = 16

Math.pow(16, 0.5); // (2 x 2) , (2 x 2) = 4
```

{{< line_break >}}

### **reduce**

-   배열.reduce(`콜백함수`, `초기값`)
-   배열의 각 element에 대해서 콜백 함수를 실행하여 **accumulator**를 반환 합니다.

{{< line_break >}}

```js
array.reduce(
    // 함수 본문에 {}를 안쓰면 해당 값이 바로 반환된다.
    (accumulator, cur, index) => accumulator + cur,
    초기값
);
```

-   `초기값`은 **accumulator**의 초기값입니다.
-   `콜백함수`: callback function

-   총 4개의 arguments 가 있습니다.
-   **accumulator**: 함수의 모든 리턴값을 축적합니다.
    -   **`콜백함수`의 return 값**은 **다음 accumulator 값**으로 할당됩니다.
-   cur: 진행되는 현재 값
-   index: 현재 진행되는 index
-   arr: 원래의 array

{{< line_break >}}

```js
const array1 = [1, 2, 3, 4];

// 0 + 1 + 2 + 3 + 4
const initialValue = 0;
const sumWithInitial = array1.reduce((accumulator, currentValue) => accumulator + currentValue, initialValue);

console.log(sumWithInitial);
// Expected output: 10
```

출처: [mozila-reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

{{< line_break >}}

## Sieve of Eratosthenes 문제 정의

-   **input**: limit
-   **output**: prime numbers

**limit 까지의 모든 소수들을 반환하는 문제 입니다.**

**ex)**

-   input:`10` **(10까지의 모든 소수들을 반환하여라)**
-   output:`[2,3,5,7]` **(10까지의 모든 소수들의 리스트)**

{{< line_break >}}

## 알고리즘

### 초기화

-   limit크기를 가진 배열을 하나 만들고 소수인 경우에는 true, 소수가 아니면 false 으로 표시해야 합니다.
-   해당 배열은 처음에는 모두 true입니다.
-   처음 우리가 알수 있는 소수는 2입니다.

```js
// 처음 변수로 limit 이 주어졌다고 가정한다.

// edge 케이스: 0 혹은 1이 주어지면 소수는 없다.
if(limit <= 1){
	return []
}

// 0도 포함할 것임으로 배열의 크기는 limit + 1 이다.
const output new Array(limit + 1).fill(true) // 모든 값을 true로 초기화
```

_const 으로 선언한 이유는 **배열이나 object**는 **const로 선언해도 안의 값을 개별적으로 변경** 할 수 있기 때문입니다._

### 반복문

각 수(첫번째 for loop)의 배수들(두번째 for loop)을 들리기 위해서 **이중 for loop**를 사용합니다.

outer 반복문의 첫번째 실행
{{< line_break >}}

4부터 limit 까지, 2의 배수들을 모두 false으로 표시합니다.

-   2 x 2 = 4 : false
-   2 x 3 = 6 : false
-   2 x 4 = 8 : false

-   until: 2 x number >= limit

{{< line_break >}}

#### **쉬운 방법**

소수가 아닌 값의 배수는 그것의 소수들이 이미 들렸을 것이므로 건너 뛰겠습니다.

```js
// 모든 수를 들린다.
for (let i = 2; i <= limit; i++) {
    if (output[j] == true) {
        // i 의 모든 배수들을 들린다.
        // i 의 첫 배수는 i * 2
        for (let j = i * 2; j <= limit; j = j + i) {
            output[j] = false;
        }
    }
}
```

#### **최적화**

위의 진행상황에서 효율을 더 높이려면 어떻게 해야 할까요?

limit이 25 라고 가정하고 i를 5까지 진행

```
i: 2 (2의 모든 배수를 false으로 표시한다.)
	j: 2 x 2 => output[4] = false
	j: 2 x 3 => output[6] = false
	j: 2 x 4 => output[8] = false
	j: 2 x 5 => output[10] = false
	...
	j: 2 x 10 => output[20] = false
	...
	until ... j: 2 x 12 => output[24] = false

i: 3 (3의 모든 배수를 false으로 표시한다.)
	j: 3 x 2 => output[6] = false
	j: 3 x 3 => output[9] = false
	j: 3 x 4 => output[12] = false
	j: 3 x 5 => output[15] = false
	until ... j: 3 x 8 => output[24] = false

i: 4 (2에서 false으로 할당되어서 건너 뛴다.)

i: 5 (5의 모든 배수를 false으로 표시한다.)
	j: 5 x 2 => output[10] = false
	j: 5 x 3 => output[15] = false
	j: 5 x 4 => output[20] = false
	j: 5 x 5 => output[25] = false
```

여기에서 겹치는 부분을 찾아보겠습니다.

```
output[6]
output[10]
output[15]
output[20]
```

3 x 2 는 i: 2 에서 2 x 3 으로
{{< line_break >}}
5 x 2 는 i: 2 에서 2 x 5 으로
{{< line_break >}}
5 x 3 는 i: 3 에서 3 x 5 으로
{{< line_break >}}
5 x 4 는 i: 2 에서 2 x 10 으로
{{< line_break >}}
이미 실행 되었습니다.

{{< line_break >}}

즉, inner loop 에서 i 보다 작은 값들은 이미 전단계들에서 실행 된것입니다.

따라서 inner loop 에서 시작 단계는 `i x i` 에서 시작하면됩니다.

```js
// 모든 수를 들린다.
for (let i = 2; i <= limit; i++) {
    if (output[i] == true) {
        // i 의 모든 배수들을 들린다.
        // j 의 시작 단계를 i x i 으로 바꾼다.
        for (let j = Math.pow(i, 2); j <= limit; j = j + i) {
            output[j] = false;
        }
    }
}
```

이렇게 코드를 변경했을때는 항상 edge case들을 검토해보는 것이 좋습니다.

저희가 방금 한것은 *inner loop의 초기값*을 변경해주었습니다.
{{< line_break >}}
_inner loop의 초기값_ 이 영향을 미칠 수 있는 edge case를 살펴보면,

-   inner loop의 종료 시점은 가장 마지막에 실행됨으로 동일합니다.
-   outer loop의 시작 시점은 항상 2로 고정되어 있습니다.

따라서 inner loop에게 영향을 받을 수 있는 **outer loop의 종료시점**을 한번 살펴보겠습니다.

-   inner loop 의 시작 시점은 outer loop 에서 할당된 i의 제곱입니다.
-   만약, inner loop에서 i x i 가 limit 보다 크다면 inner loop는 실행 되지 않을 것입니다.
-   따라서 outer loop의 종료시점은 limit의 square root으로 앞당길 수 있습니다.

```js
// 모든 수를 들린다.
for (let i = 2; i <= Math.pow(i, 0.5); i++) {
    if (output[i] == true) {
        // i 의 모든 배수들을 들린다.
        // i 의 첫 배수는 i * 2
        for (let j = Math.pow(i, 2); j <= limit; j = j + i) {
            output[j] = false;
        }
    }
}
```

{{< line_break >}}

마지막으로 **reduce**으로 true인 값들만 추출 해주면 답을 구할 수 있습니다.

```js
const result = output.reduce((accumulator, curr, index) => {
    if (curr) {
        accumulator.push(index);
    }
    return accumulator;
}, []);
```

## 전체 코드

```js
function sieveOfEratosthenes(limit) {
    // edge cases
    if (limit <= 1) {
        return [];
    }

    // 배열값 초기화
    const output = new Array(limit + 1).fill(true);

    // 전체 수
    for (let i = 2; i <= Math.pow(limit, 0.5); i++) {
        if (output[i] == true) {
            // 각 수의 배수들
            for (let j = Math.pow(i, 2); j <= limit; j = j + i) {
                output[j] = false;
            }
        }
    }

    return output.reduce((accumulator, curr, index) => {
        if (curr) {
            accumulator.push(index);
        }
        return accumulator;
    }, []);
}
```
