---
title: 'React로 빌드하고 github pages 로 호스팅 해보았다. (feat. 404에러)'
date: 2024-01-20T14:07:47+09:00
draft: false
tags: ['react']
categories: ['Tech']
featuredImage: /images/featuredImage/React로-빌드하고-github-pages로-호스팅.jpg
---
사진: [Unsplash](https://unsplash.com/ko/%EC%82%AC%EC%A7%84/%ED%9D%91%EC%9D%B8%EA%B3%BC-%EB%B0%B1%EC%9D%B8-%ED%8E%AD%EA%B7%84-%EC%9E%A5%EB%82%9C%EA%B0%90-wX2L8L-fGeA?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)의 [Roman Synkevych](https://unsplash.com/ko/@synkevych?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)
  
## Github Pages

> **Github Pages**는 **static site hosting 서비스**로 레파지토리에 HTML, CSS, Javascript 파일을 올리면 웹사이트를 배포해 주는 기능이다.

보통은 개인 블로그를 github.io 도메인을 사용하여 배포한다. 내 블로그도 **HUGO** 라는 Static Site Genegrator 와 **Github Pages** 를 이용하여 배포하였다. Github Pages를 이용하려면 **github.io 도메인**을 이용하기 때문에 `<username>.github.io` 레파지토리는 무조건 있어야 한다. 만드는 법은 [여기](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site) 참고.


Github Pages가 좋은점은 **모든 public 레파지토리를** 이 도메인을 사용하여 배포할 수 있다는 점이다. `<username>.github.io` 가 아닌 repository를 **<span style="background-color: #FFFF00">React로 빌드 하여 호스팅 할 때 주의해야 할 점을 공유하고자 한다.</span>**

## React 로 만든 웹사이트를 Github Pages 를 이용해여 배포해보자

### build 폴더 생성

React 프로젝트 의 루트폴더에서 `npm run build`를 하면 build 폴더가 생긴다.

### Github 설정

1. repository 이름 아무거나 만들고 build 폴더의 내용물들을 해당 repository에 넣어준다.
2. Settings => Pages에서

![githubPages](/images/tech/React로-빌드하고-github-pages로-호스팅/githubPages.png)

Branch를 main으로 설정하고 Save를 누르면 끝이다. 너무 쉽다!
이제 `http(s)://<username>.github.io/<repository>` 으로 접속하면 웹사이트가 나와야 하는데... 😇

### 404 에러

에러가 뜨면 기쁘다. 해결할 때 생각을 할 수 있어서 좋다. 해결하면 더 기쁘다. 다만 결과에 집착하면 안 된다.

먼저 에러의 원인을 찾아야 한다. 마지막에서 2번째에 단서가 있다.
![404Error](/images/tech/React로-빌드하고-github-pages로-호스팅/404Error.png)

`http(s)://jwanp.github.io/manifest` 이부분이 수상하다. 위에서 `http(s)://<username>.github.io/<repository>` 으로 접속해야 한다고 했다.

그렇다면 `http(s)://jwanp.github.io/<respository>/manifest` 이 되어야한다.

### 해결

React 프로젝트 루트 폴더의 package.json 파일 마지막에 `"homepage": "http://<username>.github.io/<repository>"` 를 추가해 준다.

```json
// (package.json)
{
    // (생략)
    "browserslist": {
        // (생략)
    },
    "homepage": "http://jwanp.github.io/<repsitory>/"
}
```

## Github과 오픈소스 커뮤니티

웹사이트 호스팅 서비스가 무료라는 것이 세삼 감사하게 느껴진다. 이러한 무료 서비스외에도 개발 문화가 발전한 것은 **오픈소스 커뮤니티와 문화** 라는 것을 느낀다. 물론 상업적인 목적도 있을 것이다. 그럼에도 나는 github 중심으로 돌아가고 있는 **오픈소스 문화가 특별하다고 생각한다.** 사람들은 자신이 알고 있는 좋은 것들을 공유하길 바란다. 앞으로도 이 문화가 더 발전했으면 좋겠고 나도 기여하고 싶다는 생각을 하였다.

---

**Ref**: 
- https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages
- https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site