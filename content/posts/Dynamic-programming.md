---
title: '[Javascript] Dynamic Programming'
date: 2025-03-29T18:05:00+09:00
draft: false
tags: ['javascript', 'coding-test']
categories: ['Tech']
featuredImage: /images/tech/javascript-coding-test/fibo.png
---

_Made by 박주환_

{{< line_break >}}

## 함수 및 개념 정리

Dynamic Programming은 recursive call을 쓸때 memoization을 통해서 실행 시간을 단축시키는 방법을 말합니다.

### recursive

recursive란 반복되는 계산을 함수에서 자기 자신을 호출함으로 구현하는 방법입니다.
이를 구현하려면 두가지가 필요합니다.

-   base case: 자기 자신을 호출하면서 가장 깊숙히 들어갔을때 나올 수 있는 탈출구
-   자기 호출이 포함된 실행문

## fibonacci

fibonacci란 앞에 있는 두수의 값을 더해서 나오는 수를 의미합니다.
ex) `fibo = [0, 1, 1, 2, 3, 5, 8, 13]`

```js
// 2 = 1 + 1
fibo[3] = fibo[1] + fibo[2];
// 3 = 1 + 2
fibo[4] = fibo[2] + fibo[3];
```

### fibonacci recursive

limit 이 주어졌을때 피보나치 수열을 구하고 싶다면 recursive를 통해서 구할 수 있습니다.

-   base case: 0 또는 1일때 자기자신 return
-   normal case: `fibo[n] = fibo[n-1] + fibo[n-2]`

```js
function getFibo(n){
	// edge case
	if(n < 0){
		return 0;
	}
	// base case
	if(n == 0 or n == 1){
		return n;
	}

	// normal case
	return getFibo(n-1) + getFibo(n-2);
}
```

{{< line_break >}}

제대로 작동하는지 알기 위해서 n = 4 를 기준으로 순서대로 호출해 보겠습니다.

-   getFibo(4):
    -   getFibo(3) + getFibo(2):
        -   n == 3 => getFibo(1) + getFibo(2):
            -   n == 2 => getFibo(1) + getFibo(0)
        -   n == 2 => getFibo(1) + getFibo(0)

base case까지 따라가면 다음과 같이 순차적으로 함수를 호출합니다. 여기에서 유의할점은 getFibo(1) 3번, getFibo(0) 2 번, getFibo(2) 2번으로 중복 호출이 많습니다.

{{< line_break >}}

n 이 커진다면 중복 호출은 기하급수적으로 늘어납니다. 따라서 이를 방지하기 위해서 이미 구한 n을 Javascript Object에 저장한다면 가장 밑의 호출까지 타고 내려가지 않아도 됩니다.
이를 **Memoization** 이라고 합니다.

### fibonacci memoization

-   Object가 key값을 찾는 속도는 O(1)으로 매우 빠릅니다다.
-   따라서 Object를 사용하면, 저장공간을 사용하는 대신에 효율을 높일 수 있습니다.

```js
// 피보나치를 저장할 Object
memo = {
    // base cases
    0: 0,
    1: 1,
};
function getFiboMemo(n) {
    // edge case
    if (n < 0) {
        return 0;
    }
    // 이미 계산된 n의 피보나치 수 가져온다.
    if (n in memo) {
        return memo[n];
    }
    // recursive call 으로 피보나치 수를구한다.
    let res = getFiboMemo(n - 1) + getFiboMemo(n - 2);
    // 결과를 memo에 저장한다.
    memo[n] = res;
    return res;
}
```

이것도 제대로 동작하는지 알기 위해 n = 4 일를 기준으로 순차적으로 호출해 보겠습니다.

```js
- getFibo(4) // memo = {0: 0 , 1: 1}
	- getFibo(3) + getFibo(2) // {0: 0 , 1: 1}
		- n == 3 => getFibo(2)+ memo[1] // {0: 0 , 1: 1}
			- n == 2 => memo[1] +memo[0]
			- {0: 0, 1: 1, 2: 1}
		- n == 2 => memo[2]
		- {0: 0, 1: 1, 2: 1, 3: 2}
```

이처럼 가장 마지막의 n == 2 에서 이미 memo에 저장되어 있으므로 더이상 getFibo(1) + getFibo(0) 을 호출 하지 않습니다. 따라서 n 이 커져도 각 n마다 하위 호출은 딱 한번만 하게 되어 있으므로 시간을 매우 단축 시킬 수 있습니다.

## knapsack

### 문제 정의

knapsack 문제는 값비싼 물건을 최대한 많이 훔치면 되는 문제입니다.

-   내가 가져갈 수 있는 무개: weightCap
-   물건들의 무개: weights
-   물건들의 가격: prices

이렇게 세개가 주어 졌을때 훔칠 수 있는 최대한의 가격을 구하면 됩니다.

예를 들어서 다음과 같이 주어졌을때 훔칠 수 있는 최대한의 가격은 몇일까요?

-   weightCap : 5
-   weights: [1, 3, 5]
-   prices: [250, 300, 500]

각 경우에서 weightCap: 5 보다 크면 x 으로 표시하겠습니다.

1. 모두 훔칠때 1 + 3 + 5 > 5 (x)
2. 첫번째와 두번째 1 + 3 < 5 (o), 250 + 300 == 550
3. 첫번째와 세번째 1 + 5 > 5 (x)
4. 두번째와 세번째 3 + 5 > 5 (x)
5. 세번째 5 == 5 (o), 500

따라서 훔칠 수 있는 최대한의 가격은 550 입니다.

이를 위에서 처럼 recursive solution과 memoization을 통해서 풀어보겠습니다.

### recursive knapSack

-   모든 경우의 수를 구하면서 풀어 보면 다음과 같습니다.

저희는 여러 물건들 중에서 훔치는 물건들을 선택해야 합니다.

따라서 배열을 순회하면서 해당 물건이 포함될 경우와 포함되지 않은 두 경우를 재귀호출을 한뒤에,
둘중 더 큰 가격을 선택 해서 반환하겠습니다. 재귀호출이 더 깊게 들어갈 수록, index값이 더 높은 물건에 도달하게 됩니다.

-   weightCap : 5
-   weights: [1, 3, 5]
-   prices: [250, 300, 500]

현재 상태는 `방금 넣은 물건의 가격 | 남아 있는 용량` 으로 표시하겠습니다.

{{< line_break >}}

1. 첫번째 물건을 가방에: `250 | 4`
    1. 두번째 물건을 가방에: `300 | 1`
        1. 세번째 물건은 못넣어(5 > 1): `0 | 1`
            1. 물건이 없어: `0 | 1`
                - return 0
            - return 0
        - return 300
    2. 두번째 물건 버려: `0 | 4`
        1. 세번째 물건은 못넣어(5 > 4): `0 | 1`
            1. 물건이 없어: `0 | 1`
                - return 0
            - return 0
        - return 0
    3. 둘중에 더 비싼것은? (300 > 0)
    - return 300 + 250
2. 첫번째 물건 버려: `0 | 5`
    1. 두번째 물건을 가방에: `300 | 2`
        1. 세번째 물건은 못넣어: (5 > 2): `0 | 2`
            1. 물건이 없어: `0 | 2`
                - return 0
            - return 0
        - return 300
    2. 두번째 물건 버려: `0 | 5`
        1. 세번째 물건을 가방에: `500 | 0`
            1. 남은 용량이 없어: `0 | 0`
                - return 0
            - return 500
        - return 500
    3. 둘중에 더 비싼것은? (300 < 500)
    - return 500
3. 둘중에 더 비싼것은? (550 > 500)

-   return 550

{{< line_break >}}

이를 코드로 짜면 다음과 같습니다.

```js
const knapSack = (weightCap, weights, prices, i) => {
    // 남은 용량이 없어 || 물건이 없어
    if (weightCap == 0 || i == weights.length) {
        return 0;

        // i번째 물건은 못넣어
    } else if (weights[i] > weightCap) {
        return knapSack(weightCap, weights, prices, i + 1);
    } else {
        // i 번째 물건을 가방에
        const includeRes = prices[i] + knapSack(weightCap - weights[i], weights, prices, i + 1);
        // i 번째 물건 버려
        const excludeRes = knapSack(weightCap, weights, prices, i + 1);
        // 둘중에 뭐가더 크지?
        return Math.max(includeRes, excludeRes);
    }
};
```

### memoization knapsack

위의 해결책은 recursive call 덕분에 각 호출마다 맨 끝까지 들렀다 옵니다.
따라서 저희는 중복된 호출을 피하기 위해서 memoization을 활용해 보겠습니다.

먼저 memoization을 활용하기 위해서는 knapSack 함수의 의미를 제대로 파악 해야합니다.

knapSack함수의 결과 값은 <u>내 가방에 넣을 수 있는 최대한의 가격</u> 입니다.
파라미터는 다음과 같습니다.

-   남은 가방의 용량
-   물건 용량 리스트
-   물건 가격 리스트

recursive knapSack 에서 3개의 파라미터를 다음과 같이 정의 했었습니다.

-   weightCap : 5
-   weights: [1, 3, 5]
-   prices: [250, 300, 500]

여기에서 각 호출마다 계속 변화하는 값 두개가 있습니다.

-   남은 용량(weightCap)
-   물건의 index

따라서 이를 활용하면 중복되는 호출을 피할 수 있지 않을까요?

겹치는 호출을 찾아보면,
남은 용량이 1일때 세번째 물건은 항상 0을 반환합니다.

왜냐하면 세번째 물건의 용량은 5이기 때문입니다. 마찬가지로 남은 용량이 2, 3, 4 일때도 세번째 물건은 항상 0를 반환 합니다.

또한 남은 용량이 0일때 모든 물건은 항상 0을 반환합니다.

따라서 저희는 배열 (남은 용량, 물건의 index)위치에 내 가방에 넣을 수 있는 최대한의 가격을 저장 하고, 각 호출에서 남은 용량과 index별로 최대한의 값을 채우면 문제를 해결 할 수 있습니다.

결국 정답은 남은 용량이 5일때, 물건의 index가 1인 값이 될 것입니다.
왜냐하면 해당 파라미터일때가 가장 상위의 호출이기 때문입니다.

전체 진행 과정을 간단히 설명 해보겠습니다.
각 호출마다 가장 깊은 호출의 인덱스 부터 배열의 값이 채워질 것입니다. 여기에서 가장 깊은 호출은 index 값이 크고, weightCap 값이 낮은 순일 것입니다. 위의 호출 예시를 보면 이해가 더 쉽습니다.

{{< line_break >}}

1. 첫번째 물건을 가방에: `250 | 4`
    1. 두번째 물건을 가방에: `300 | 1`
        1. 세번째 물건은 못넣어(5 > 1): `0 | 1`
            1. 물건이 없어: `0 | 1`
                - return 0
            - return 0
        - return 300
    2. 두번째 물건 버려: `0 | 4`
        1. 세번째 물건은 못넣어(5 > 4): `0 | 1`
            1. 물건이 없어: `0 | 1`
                - return 0
            - return 0
        - return 0
    3. 둘중에 뭐가 더 비싸지? (300 > 0)
    - return 300 + 250

{{< line_break >}}

위의 recursive call의 일부를 가져왔습니다. 여기에서 값이 채워지는 순서는 어떻게 될까요?

-   `matrix[남은 용량, 물건의 index]`
-   (가장 깊은 호출인) 남은 용량이 1일때 세번째물건: `matrix[1, 2] = 0`
-   남은 용량이 4일때 두번째 물건: `matrix[4,1] = Math.max(300, 0)`
-   남은 용량이 5일때 첫번째 물건: `matrix[5,0] = Math.max(550, ??)`

해당 순서로 호출을 하게되고 이렇게 배열에 값을 저장한다면 다른 호출인 `??`을 진행할때 동일한 호출에 대한 matrix 값이 존재한다면 시간을 단축 할 수 있습니다.

```js
const dynamicKnapsack = function (weightCap, weights, values) {
    const numItem = weights.length;
    const matrix = new Array(numItem);
    // row: 지금까지 본 아이템의 개수
    // column: 수용가능한 weight
    for (let index = 0; index <= numItem; index++) {
        matrix[index] = new Array(weightCap + 1);
        for (let weight = 0; weight <= weightCap; weight++) {
            if (weight <= 0 || index <= 0) {
                matrix[index][weight] = 0;
            } else if (weights[index - 1] <= weight) {
                const leftWeightCap = weight - weights[index - 1];
                const included = weights[index] + matrix[index - 1][leftWeightCap];
                const excluded = matrix[index - 1][leftWeightCap];
                matrix[index][weight] = Math.max(included, excluded);
            } else {
                // 무조건 excluded
                const leftWeightCap = weight - weights[index - 1];
                matrix[index][weight] = matrix[index - 1][leftWeightCap];
            }
        }
    }
    return matrix[numItem][weightCap];
};

// 테스트 하기
const weightCap = 50;
const weights = [31, 10, 20, 19, 4, 3, 6];
const values = [70, 20, 39, 37, 7, 5, 10];
console.log(dynamicKnapsack(weightCap, weights, values));
```
