---
title: "Web3.js"
date: 2023-08-06T22:33:26+09:00
draft: false
tags: ["block-chain"]
categories: ["Tech"]
featuredImage: /images/web3.js/web3_js.png
---

지금까지는 스마트 컨트렉트를 개발하는 것에 초점이 맞춰져 있었다. 그렇다면 frontend 랑 스마트 컨트렉트와 연결은 어떻게 할까? 

web 개발은 크게 front-end 와 back-end 로 나누어서 볼 수 있다. API 서버를 만들어서 필요한 정보들을 HTTP 프로토콜로 가져오든지 해서 보기 좋게 front-end 에서 뿌려준다. 

Web2 과 유사하게 back-end 는 smart-contract 로 보고 front-end 는 동일하다. 그렇다면 소통 방식도 같을까? 그렇지 않다.  Blockchain node 의 provider 는 JSON-RPC 라는 통신 규약을 사용하고 우리는 smart-contract 에서 정보들을 가져올때 이러한 규약들을 이용해서 소통해야한다. 

이 과정을 우리가 일일히 다 코딩하면 머리 아프고 귀찮다. 따라서 Web3.js 나 ethers.js 와 같은 라이브러리가 있는 것이다.  이번 글에서는 그중에서도 ethereum foundation 에서 관리하고 있는 Web3.js 를 살펴볼것이다.

## Web3.js 란?

[web3.org](https://docs.web3js.org/)

> Web3.js is a collection of **libraries that allow you to interact with a local or remote ethereum node** using HTTP, IPC or WebSocket.

Web3.js 란 이더리움 노드와 상호작용하기 위해서 필요한 **javascript** 모듈들을 모아둔 라이브러리 이다. 

다른말로 하면, ethereum node 와 상호작용 하기위해 이미작성된 **javascript** 코드의 모음 이라고 보면 될것 같다.

예를 들어서 DApp (Decentralized application) 을 만든다고 해보자. 그렇다면 여기에서 solidity 언어로 smart contract 를 작성한뒤 블록체인 상에 배포하고 frontend 에서 해당 smart contract 와 상호작용 해야한다. 이때 필요한것이 web3.js 이다.

blockchain 노드상에 있는 스마트 컨트렉트 정보들은 blockchain 안에서만 접근할 수 있다. 예를 들어서 인터넷에 있는 정보는 우리가 인터넷에 접속해야 볼 수 있듯이 블록체인 위에 배포된 스마트 컨트렉트 에 있는 정보들을 보려면 블록체인에 접근 해야한다. 

그렇다면 smart contract 정보에 접근하기 위해서는 blockchain 에 접속해야 하는데 어떻게 접속을 한단 말인가? 

## Provider			 			 			 			 		

우리는 Provider 를 통해서 blockchain node 에 접근 할 수 있다. 지금은 Provider 을 이해하기 쉽게 blockchain 노드에 있는 API server 라고 생각하면 될 것 같다.

[https://docs.web3js.org/guides/web3_providers_guide/](https://docs.web3js.org/guides/web3_providers_guide/)

> web3.js **providers** are objects responsible for enabling connectivity with the Ethereum network in various ways.

- Provider 란 ethereum network 안에서 JSON-RPC 형식에 맞춰서 데이터를 주고 받는 ethereum node 이다.
- **JSONRPC** vs HTTP protocol: 정보를 주고 받기 위한 **통신 규약**

일반적인 web 에서는 frontend 가 API server 와 통신을 주고 받을때 HTTP protocol 로 통신한다. 하지만 provider 는 JSON-RPC 라는 통신 규약 에 의해서 소통한다. 

![provider 구조도](/images/web3.js/Wed3js구조도.jpeg) 

[마이크로소프즈웨어 393호 발췌 (web3.js 구조도)](http://wiki.hash.kr/index.php/%ED%8C%8C%EC%9D%BC:Wed3js%EA%B5%AC%EC%A1%B0%EB%8F%84.jpg)

Provider 는 다음과 같은 것들이 있다. 한번씩 들어봤던가 써봤던 것들이다. 

1. Remote Node Provider - alchemy ..
2. Injected Provider - MetaMask ..

고맙게도 Alchemy, Infura, Metamask 같은 provider 들이 이미 구현 되어있어서 우리는 이것들과 정보를 주고 받으면서 쓰기 만 하면 된다. 또 이러한 provider 와 상호작용 하는 코드도 Web3.js 라이브러리에 다 구현이 되어 있다.. 너무 편한 세상이다. 이런것들을 오픈소스로 제공해 주는 사람들이 아직 많은 것을 보니 우리의 미래는밝다.

## Tutorial

[공식 홈페이지](https://docs.web3js.org/) 의 Getting Started 를 따라하면 Web3.js 를 어떻게 사용하는지 알 수 있다. 그중에서 주의 해야할 점과 인상 깊게 봤던 부분들을 공유하겠다.

### constructor 에서 provider 자동 할당.

Web3 객체 에서 Provider 를 할당 할때 HttpProvider, WebsocketProvider 등 으로 따로 할당을 해야하는데 constructor 에서 url 을 보고 자동으로 할당 시켜 준다고 한다.

원칙적으론 다음과 같이 provider 를 할당해줘야한다.

```javascript
const web3 = 
     new Web3(new Web3.providers.HttpProvider('http://loc')
const web3 = 
		new Web3(new Web3.providers.WebsocketProvider('ws://loc') 
```

하지만 복잡하게 위처럼 하지 않아도' ws:' 혹은 'http:' url 을 넣어주면 Web3.js 라이브러리에서 알아서 할당해 준다.

```javascript
const { Web3 } = require('web3'); 
const web3 = new Web3(/* PROVIDER*/); 
```

### MetaMask

metamask 에서는 window.ethereum 이라는 객체가 브라우저에서 생성한다. 따라서 window.ethereum 객체가 없다면 metamask 가 안 깔린 것이다.

다음과 같이 Web3 객체를 할당할 수 있다.

```javascript
web3 = new Web3(window.ethereum);
```

### deploy SmartContract

smartcontract 를 compile 하면 abi 와 bytecode 가 주어진다. 어떻게 compile 하냐고? solc 라이브러리나 hardhat 라이브러리를 이용하면 된다.

abi 와 bytecode 로 다음과 같이 스마트 컨트렉트를 배포하면 된다.

```javascript
const MyContract = new web3.eth.Contract(abi);
const myContract = MyContract.deploy({ data: '0x' + bytecode, arguments: [1], });
const gas = await myContract.estimateGas({ from: account, }); const tx = await myContract.send({ from: account, gas, gasPrice 10000000000, });
```

여기에서 주의 해야할 점은 MyContract.deploy() 한다고 끝이 아니다. <br>스마트 컨트렉트를 배포하는것은 블록체인 노드의 변화를 야기 하므로 가스비가 든다.  .send() 함수 까지 해줘야 한다.

### .send() 함수와 .call() 함수

SmartContract 를 가져올때는 abi 와 블록체인 상에서 가져올 contract address 가 필요하다.

```javascript
const MyContract = new web3.eth.Contract(abi, deployedAddress);
```

다음과 같이 SmartContract 를 가져오고 난뒤에 .send() 와 .call() 함수를 이용해서 함수를 호출 하면 되는데, 상황에 따라서 두개가 나뉘어서 쓰인다. <br>.send() 는 gas 가 필요할때, .call() 은 gas 가 필요 없을때 쓰인다.

**smart contract** 함수 호출

-  **.send()**: smart contract 의 상태를 변경하는 함수를 호출: estimatedgas, gasPrice 필요

- **.call()**: smart contract 의 상태를 변경하지 않는 함수 호출: pure, view

## Web3.js vs Ethers.js

해당 포스트에서 ethers.js 는 다루지 않았지만 둘중 어느 라이브러리를 사용해야 한단 말인가? <br>나는 Ethers.js 의 손을 들어 주고 싶다. 하지만 먼저 두개를 비교하면 다음과 같다.

|                     | web3.js                                                      |                          ethers.js                           |
| :-----------------: | ------------------------------------------------------------ | :----------------------------------------------------------: |
|       발표일        | Feb 2015                                                     |                           Jul 2016                           |
|    github stars     | [**17.8k** stars](https://github.com/web3/web3.js/stargazers) | [**6.9k** stars](https://github.com/ethers-io/ethers.js/stargazers) |
| github contributors | 16**                                                         |                              1                               |
|        size         | 590kb                                                        |                            116kb                             |

해당 지표를 보면 Web3.js 가 더 많은 인기를 누리고 있다. 

두개의 가장 큰 차이점은 

Web3.js 는 블록체인과 상호작용하는 메서드를 단일한 **web3** 객체로 제공한다.<br> 하지만  **ethers.js**는 API를 두 가지 개별 역할로 분리한다.

1. **Provider**: 개인키 없이 이더리움 네트워크 연결
2. **Signer**: 개인 키에 접근하여 트랜잭션에 서명할 수 있다.

이는 개발자들이 더 유연하게 코드를 작성할 수 있도록 도와준다. 그리고 직접 코드를 짜보면 개인적으로 코드가  ethers.js 가 더 깔끔하다는 인상을 준다.

두개의 장점만을 나열하면 다음과 같다.

- **web3.js** : popularity, ethereum foundation, documentation
- **ethers.js** : performance, usability, hardhat-tutorial, documentation(getting-started, playground)

### 결론

둘다 상황에 맞춰서 적절히 사용하면 되지만 평소에는 ethers.js 를 더 많이 사용 할것 같다. 왜냐하면 hardhat tutorial 이 ethers.js 로 되어 있는데 이것이 매우 편리 하기 때문이다.





