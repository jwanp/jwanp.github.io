---
title: "[ESLint, React] 모든 Import 에 Casing 오류가 뜬다"
date: 2024-01-13T14:03:59+09:00
draft: false
tags: ["2024-collection","React","ESLint"]
categories: ["Tech"]
featuredImage: /images/Tech/202040113-ESLint.webp
---

#
ESLint 에서 모든 import 에 대해서 Casing 오류가 떴다.
> Casing of {file name} does not match the underlying filesystem.

[stackOverflow](https://stackoverflow.com/questions/63254383/casing-of-types-does-not-match-the-underlying-filesystem) 도 보고 [ESLint issues](https://github.com/microsoft/vscode-eslint/issues/1130) 도 봤지만 힌트를 찾지 못했다.

## 그래서 **Chat GPT** 에 물어봤다.

답은 다음과 같이 왔다.
1. **Case Sensitivity**
2. **Check the File System**
3. **File Extension**
4. **IDE/File Explorer Case Sensitivity**

먼저 **Case Sensitivity** 는 대소문자 오류이다. ESLint 는 **대소문자** 구분에 엄격하지만 윈도우 나 WSL 을 사용하면 이것이 구분이 안되는 경우도 있다고 한다.

그러나 나는 Mac 을 사용중이고 아무리 생각해도 대소문자 오류는 아닌 듯 했다. finder 로 봐도 파일 이름에는 문제가 없었다.

여기에서 답을 찾지는 못했지만 단서는 **파일 이름**이나 **파일 시스템** 에 있었다. 

## 결국 한글이 문제 였던 것이다.

![Casing Error](/images/Tech/20240113-1.png "{width: 100%}")  

여기에서 `/프로젝트/` 이부분이 문제이다. `Project` 로 바꾸니 모든 import 문제가 해결되었다.
