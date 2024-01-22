---
title: "Visual Studio Code --file-write: EPERM 해결"
date: 2024-01-22T10:15:17+09:00
draft: false
categories: ['Tech']
featuredImage: /images/featuredImage/VSC--file-write-Error.png
---

잘 작업해 오던 작업 폴더에서 저장이 안된다. 마지막에 branch merge 하고 작업 branch 삭제후 다시 분기했었다. 그 후부터 익숙하지만 좀 더 까다로운 에러가 뜬다.

## 비번을 입력해도 왜 계속 에러가 뜨지

먼저 저장을 하면 비번을 입력하라고 나온다. 여기까지는 익숙하다. 그리고 비번을 치면 저장이 되어야 하는데... 또 다시 에러가 뜬다.

![fileWriteError](/images/tech/VSC--file-write-Error.png )

이런적은 처음이어서 굉장히 당황스러웠다.

왜 이럴까? 일단 EPERM을 보니 권한 문제인 것 같다. 따라서 폴더의 권한을 나로 바꿔야 하고 구글링을 해서 해결하였다.

## 해결방법

1. `echo $USER` 로 자신의 이름을 파악한다. 다음줄에 `<username>` 이 뜰것이다.
2. `chown -R <username> .` 을 입력하여 권한을 바꾼다.

- `chown`: 폴더의 owner 를 `<username>`으로 바꾼다. 
- `-R` : recursive 라는 뜻으로 `.`(현재폴더) 하위 모든 폴더와 파일들에게 적용한다.