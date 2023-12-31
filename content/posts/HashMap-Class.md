---
title: "[Java] HashMap Class"
date: 2023-11-19T02:57:06+09:00
draft: false
tags: ["Java","HashMap","코딩테스트"]
categories: ["Java"]
featuredImage: /images/Java/Java.png
---


---
Java HashMap Class 를 알아보자.

먼저 HashMap 은 탐색에 매우 효율적이다. 

- Key-value pair 로서 값을 저장한다.
- key 값과 value 값을 hashfunction 을 이용해 매우 빠르게 찾는다.
- 탐색시 O(1) 의 성능을 보인다.

해당 사이트들을 참고하여 작성하였다.

https://docs.oracle.com/javase/8/docs/api/java/util/HashMap.html

https://coding-factory.tistory.com/556 

---
---
## import

HashMap<K,V>

- K - key 값의 type
- V - value 값의 type

```java
import java.util.HashMap;
```
---
---
## Constructors

default load factor - 0.75



### HashMap()

```java
HashMap<String, String> map = new HashMap<String, String>();
HashMap<String, String> map = new HashMap<>(); 
```

### HashMap(int initialCapacity)

선언시에 미리 사이즈를 정해두면 메모리 공간에 상당히 도움된다.<br>
크기를 알 수 있다면 initialCapacity 를 설정 해 두도록 하자.

```java
HashMap<String, String> map = new HashMap<>(10);
```

### **HashMap(init initialCapacity, float loadFactor)**

```java
HashMap<String, String> map = new HashMap<>(10,0.7f);
```

### **HashMap(Map<? extennds K, extends V> m)**

```java
HashMap<String, String> map = new HashMap<String,String>(){{
	put("key","value");
}};
```
---
---
## Methods

HashMap 클래스의 기본적인 함수들이다.<br>
사용 법은 `hm.함수명;` 이런식으로 쓰면된다.

### clear, clone

- **clear()** -> void

  모든 매핑을 삭제한다.

- **clone()** -> Object

  객채에 대한 얕은 복사(Shallow copy)를 한다. (주소값만 복사)

```java
map.clear(); //모든 값 제거
```

### contains

- **containsKey(Object key)** -> boolean

  key 값이 존재하면 true 반환

- **containsValue(Object value)** -> boolean value 

  value 값 존재하면 true 반환

### Set

Key 와 Value 값을 순환할때는 **entrySet()** 을 하는 것이 더 효율적이다.<br> 
KeySet() 을 사용하면 map.get() 함수를 써야해서 성능이 하락된다.

- **entrySet()** -> Set<Map.Entry<K,V>>

  mapping 에 대한 집합 view 를 반환

- **keySet()** -> Set<K>

  키값들에 대한 집합 view 를 반환

```java
//entrySet() 
for (Entry<Integer, String> entry : map.entrySet()) {
    System.out.printlnentry.getKey() + " : " + entry.getValue());
}

//KeySet() 
for(Integer i : map.keySet()){ //저장된 key값 확인
    System.out.println(i + " : " + map.get(i));
}

// Entry.setValue() 이용 모든 값에 10 더하기
for (Map.Entry<String,Integer> item : map.entrySet()){
    item.setValue(item.getValue() + 10);
}
```

### get

- **get(Object key)** -> V

- **getOrDefault(Object key, V defaultValue)** -> V

  키에 대한 값이 존재: 값 반환

  키에 대한 값이 존재 X: defaultValue 매핑

```java
// Minsoo 키값이 존재: 기존 value + 1
// Minsoo 키값이 존재 X: 0 + 1
map.put("Minsoo", map.getOrDefault("Minsoo", 0) + 1)
```

- **isEmpty()** -> boolean

### put

- **put(K key, V value)** -> V

- **putAll(Map<? extends K, ? extends V> m)** -> void

  모든 mapping 값을 복사한다.

- **putIfAbsent(K key, V value)** -> V

  key값이 mapping 되어있지 않다면 해당 value 로 mapping 한다.

```java
map.put(1,"사과");
// 기존 Minsoo 키값에 대한 value 값 -1 업데이트
map.put("Minsoo", map.get("Minsoo") - 1);
```

### remove

- **remove(Object key)** -> V

- **remove(Object key, Object value)** -> boolean

  key 값이 해당 value 와 mapping 되어 있을 때만 삭제

```java
map.remove(1); //key값 1 제거
```

### replace

- **replace(K key, V value)** -> V

- **replace(K key, V oldValue, V newValue)** -> boolean

- **replaceAll(BiFunction<? super K,? super V,? extends V> function)** -> void

```java
map.replaceAll((key, value) -> {
  if(key.contains("Script")) {
    return value * 10;
  }
  return value;
});
```

### size, values

- size() -> int

- values() -> Collection<V>

```java
// values() 값들 ArrayList 로 변환
List<Integer> length_list = new ArrayList<>(hm.values());
```
---
---

## HashMap Sorting

```java
import java.util.Map;
import java.util.HashMap;
import java.util.Collections;
import java.util.List;
import java.util.Comparator;
import java.util.ArrayList;
```

### key ArrayList 선언

```java
List<Integer> keyList = new ArrayList<>(map.keySet());
```

### value 값 기준 오름차순 정렬

```java
Collections.sort(keySetList, (o1, o2) -> (map.get(o1).compareTo(map.get(o2))));
```

### value 값 기준 내림차순 정렬

```java
Collections.sort(keySetList, (o1, o2) -> (map.get(o2).compareTo(map.get(o1))));
```

### key 값 기준 오름차순 정렬

```java
Collections.sort(keySetList);
```

