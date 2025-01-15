---
title: '[Javascript] Capturing Rainwater'
date: 2025-01-16T00:16:43+09:00
draft: false
tags: ['javascript', 'coding-test']
categories: ['Tech']
featuredImage: /images/tech/javascript-coding-test/capturing-rainwater.png
---

_Made by 박주환_

## 함수 및 개념 정리

### two pointer

two pointer란 배열에서 양쪽의 두개의 포인터가 서로를 향해 이동하면서 문제를 푸는 방법입니다.

-   배열 문제에서 주로 쓰입니다.
-   배열의 복사본이 필요하지 않고 한번의 loop(배열의 길이 만큼 == O(n))으로 문제를 풀 수 있습니다.

### Math.max(요소1, 요소2, ...): 가장 큰것을 반환합니다.

```js
console.log(Math.max(1, 3, 2));
// Expected output: 3

console.log(Math.max(-1, -3, -2));
// Expected output: -1

const array1 = [1, 3, 2];

console.log(Math.max(...array1));
// Expected output: 3
```

-   [출처](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/max)

### while(조건){ 실행문 } : 문장안을 실행하기 전에 조건문을 확인합니다.

```js
var n = 0;
var x = 0;

while (n < 3) {
    /*(1.조건문)*/ // (2.실행문)
    n++;
    x += n;
}
```

1. 조건문 `(0 < 3 == true)`
2. 실행문 `(0 + 1 == 1), (0 + 1 == 1)`

3. 조건문 `(1 < 3 == true)`
4. 실행문 `(1 + 1 == 2), (1 + 2 == 3)`

5. 조건문 `(2 < 3 == true)`
6. 실행문 `(2 + 1 == 3), (x += n == 1)`

7. 조건문 `(3 < 3 == false)`
8. **break;**

-   [출처](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/while)

## Capturing Rainwater 문제 정의

해당 문제는 높이들의 array가 있을때 그 사이에 차는 물의 높이를 구하는 것입니다.

-   input: 높이들 `[4, 2, 1, 3, 0, 1, 2]`
-   output: totalWater: 6

![title](/images/tech/javascript-coding-test/capturing-rainwater.png)

다음과 같이 각 index 사이에 차는 물의 총량을 구하여라

## 알고리즘

각 height에서 물이 어디까지 차는지 구할 수 있다면 각 요소에서의 물의양을 모두 더하면 됩니다. 따라서 각 요소에서의 물의양을 구해보겠습니다.

> height `[4, 2, 1, 3, 0, 1, 2]` 가 주어졌을때 먼저 **index 2**입장에서 생각해보겠습니다.

어디까지 물이 찰까요?

-   왼쪽의 가장 큰 곳까지 물이 찬다.
-   오른쪽의 가장 큰 곳까지 물이찬다.

얼마나 물이 찰까요?

-   물이 차는 양: 왼쪽과 오른쪽 벽중 낮은것의 높이 - 나의 높이

**이 알고리즘이 다른 인덱스에도 적용되는지 확인해 보겠습니다.**

> **index 0**

-   왼쪽 가장 큰 곳: 나 자신(4)
-   오른쪽 가장 큰 곳: 나 자신(4)
-   따라서 4 - 4 = 0

-   **왼쪽과 오른쪽의 가장 높은 곳을 구할때는 나자신도 포함되어야 합니다.**
    > **index 1**
-   왼쪽 가장 큰 곳: 4
-   오른쪽 가장 큰 곳: 3
-   물이 차는 양: 3 - 2 = 1

> **index 3**

-   왼쪽 가장 큰곳: 4
-   오른쪽 가장 큰 곳: 나 자신(3)
-   물이 차는 양: 3 - 3 = 0

모든 index에 적용이 됨으로 해당 알고리즘으로 two pointer를 구현합니다.

## two pointer으로 구현하기

왼쪽의 가장 큰 벽을 `leftBound`, 오른쪽의 가장 큰 벽을 `rightBound` 으로 정의하고
왼쪽의 pointer를 `leftPointer`, 오른쪽의 pointer를 `rightPointer` 으로 정의하겠습니다.

원래는 pointer마다 leftBound와 rightBound를 따로 지정해 주어야합니다.
하지만 우리는 각 포인터에서 순차적으로 진행하기 때문에 반대 방향인 **leftPointer의 rightBound를 알수 없고, rightPointer의 leftBound를 알수 없습니다.**

이 문제를 어떻게 해결 해야 할까요?

각 포인터에서 물의 높이를 구하려면 **leftBound와 rightBound 중에서 더 작은 것만 필요합니다.** 따라서 다음을 이용하면 두개의 포인터중에서 하나는 더 작은 bound를 확정 할 수 있습니다.

1. leftPointer의 leftBound와 rightPointer의 rightPointer를 비교 한다.
2. 더작은 bound를 가지고있는 pointer는 해당 bound 반대방향에 _적어도 하나는 자신의 bound보다 크다._
3. 따라서 물의 양 = 자신의 높이 - 자신의 bound

이제 two pointer를 기준으로 순차적으로 차는 물의 양을 구하면서 포인터를 옮겨 보겠습니다.

> **left pointer**

index: 0

-   leftBound는 자기 자신이다.
-   rightBound는 자기 자신이다.
-   차는 물의 양은 0

index: 1

-   leftBound는 4 이다.
-   rightBound는 알 수 없다.

> **right pointer**

index: 6

-   leftBound는 자기 자신이다.
-   rightBound는 자기 자신이다.
-   차는 물의 양은 0

index: 5

-   leftBound는 알 수 없다.
-   rightBound는 2 이다.

여기에서 1차로 막힙니다. 왜냐하면 각각의 포인터에서 하나의 bound를 모르기 때문입니다.
하지만 위에서 각 포인터의 bound를 구하는 과정을 이용하면 해결 할 수 있습니다.

**4(leftPointer leftBound) > 2 (rightPointer rightBound)** 이므로 rightPointer의 물이 차는 양은 **2(rightPointer rightBound) - 1(rightPointer height) == 1** 으로 차는 물의 양을 구할 수 있습니다.

## 전체 코드

1. leftBound와 rightBound 비교
2. 더작은 pointer 진행
3. bound와 나의 높이를 비교하여 더 큰것으로 bound를 update 한다.
4. 차는 물의 양을 구하여 totalWater에 더한다.
5. 해당 pointer 이동

```js
function capturingRainWater(heights) {
    let totalWater = 0;
    let leftPointer = 0;
    let leftBound = heights[leftPointer];
    let rightPointer = heights.length - 1;
    let rightBound = heights[rightPointer];
    let currentHeight = 0;

    // 둘이 만나면 멈춘다.
    while (leftPointer < rightPointer) {
        // 고려 X: 만약 둘이 같다면 둘다 확정 지을 수 있다.
        if (leftBound <= rightBound) {
            // 1. leftBound와 rightBound 비교
            currentHeight = heights[leftPointer]; // 더 작은 pointer 진행
            leftBound = Math.max(leftBound, currentHeight);
            totalWater += leftBound - currentHeight;
            leftPointer += 1;
        } else {
            currentHeight = heights[rightPointer];
            rightBound = Math.max(rightBound, currentHeight);
            totalWater += rightBound - currentHeight;
            rightPointer -= 1;
        }
    }
    return totalWater;
}
```
