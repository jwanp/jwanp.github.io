---
title: "Minting Nft"
date: 2023-07-16T19:59:12+09:00
draft: false
tags: ["block-chain"]
categories: ["Tech"]
featuredImage: /images/minting-nft/opensea.png
---

# 나만의 NFT 만들기

remix, pinata 를이용해서 Sepolia test net 에 NFT 를 배포하는 법을 알아보자



## NFT란 무었인가?

NFT: Non-Fungible Tokens 

한글로 번역하면 뭔가를 다른 무엇인가로 바꿀수 없는 토큰이다.

[NFT]: https://ethereum.org/en/nft/

> *NFTs are* ***tokens*** *that we can use* ***to represent ownership of unique items****. They let us tokenise things like* ***art, collectibles, even real estate**.* *They can only have one official owner at a time and they're secured by the Ethereum blockchain – no one can modify the record of ownership or copy/paste a new NFT into existence.*

NFT 는 토큰이지만 보통 토큰과는 다르다. 그림, 소장품, 부동산 등 사물을 토큰화하여 그것에대한 소유를 나타내 주는 토큰이다. 예를 들어서 그림을 NFT 로 만들 수 있다. 블록체인 상에서 어떤 그림을 토큰화 하여서 그것을 토큰처럼 거래할 수 있고 소유할 수 있다.



해당 그림을  블록체인 위해서 토큰화하여 소유하거나 다른사람에게 보낼 수 있게 되는 것이다.

지금부터 NFT 를 생성하는 방법을 알아 보겠다.

### 사전에 필요한것

1. [Metamask Wallet]( https://metamask.io/)

2. [Remix](https://remix.ethereum.org)

3. 개인 그림: 아무 그림이나 상관 없다. 자신이 그린 그림, 사진 등등. 딱히 할만한게 없다면 그림 AI 생성기를 이용해서 만들어보자 [DALLE]( https://openai.com/dall-e-2)	

4. [Sepolia faucet](https://sepoliafaucet.com/) 으로 ETH 를 받는다.

5. [Pinata](https://www.pinata.cloud/) 계정



**NFT** 를 배포하는 과정을 간략하게 설명하면 다음과 같다. 스마트 컨트렉트에 **ERC-721** token contract 코드를 작성한뒤 배포하면 된다.

해당 스마트 컨트렉트에서 가장 중요한건 token 의 <u>**metadata** 가 저장되어 있는 URL</u> 이다. 해당 URL 은 어떻게 만들어 지며 어떠한 정보가 저장되어 있을까?

## NFT metadata

metadata 에는 NFT 에대한 정보가 저장되어 있다. 예를들어 NFT 의 이름, 사진, 설명, 특성 등이 있을 수 있다.

가장 보편적으로는 JSON 파일 형태로 저장된다. 예를 들면 다음과 같이 작성할 수 있다.

```json
{
  "description": "Winnie the Pooh created by DALL E",  
  "image": "ipfs:///QmU2Wf4z4w6VSwPUMgWCq3UwCgRAG5EQtkFHqXkz8sRtz9", 
  "name": "WINNIE_SECOND"
}
```



해당 metadata 가 저장되어 있는 URL 을 만들려면 어떻게 해야할까?? 

1. blockchain 위에 metadata 를 저장한다.
2. **IPFS 를 사용한다.**
3. JSON file 을 반환하는 API 를 만든다.

첫번째랑 두번째 방법이 가장 많이 쓰인다. 첫번째 방법은 해당 metadata 를 저장하고 호출하는데에 ETH 가 쓰일 수 있어서 비싸다. 따라서 **pinata 를 이용한 두번째 방법으로 해보도록 하겠다.** 

세번째 방법은 <u>peer-to-peer network</u>가 아니다. 이는 블록체인이 쓰이는 의미 퇴색시키므로 잘 쓰이지 않는다. 

### IPFS 란 무었인가?

[IPFS](https://docs.ipfs.tech/concepts/what-is-ipfs/#defining-ipfs) - InterPlanetary File System

> **IPFS** is a modular suite of <u>protocols</u> for organizing and transferring data, designed from the ground up with the principles of <u>content addressing</u> and **peer-to-peer networking**.

IPFS 는 분산형 파일 시스템에 데이터를 저장하고 인터넷으로 공유하기 위한 protocol 이다. 이는 탈중앙화 웹으로 하나의 서버가 다운되거나 파괴되더라도 파일을 안전하게 보관 할 수 있다.



그럼 본격적으로 pinata 에서 해당 metadata 를 저장해보자.

먼저 pinata 에 그림파일을 업로드 한후에 해당 **CID** number 를 복사한다.
<br>
![pinata](/images/minting-nft/pinata.png)  
<br>
metadata.json 파일에 해당 NFT 의 이름, image url, description 등을 작성 한뒤에 pinata 에 업로드 한다. 여기에서 image  에는 **ipfs://** **뒤에** 복사한 **CID** number 를 넣어준다.
<br>
```json
{
  "description": "Winnie the Pooh created by DALL E",  
  "image": "ipfs:///QmU2Wf4z4w6VSwPUMgWCq3UwCgRAG5EQtkFHqXkz8sRtz9", 
  "name": "WINNIE_SECOND"
}
```
<br><br>


## ERC-721 token contract

[remix](https://remix.ethereum.org/) 에서 **ERC-721** contract 를 작성 해 보자. [ERC-721](https://ethereum.org/ko/developers/docs/standards/tokens/erc-721/) 이 궁금하다면 하이퍼 링크를 참고하라. 

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >= 0.7.0 < 0.9.0;


import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/utils/Counters.sol";

contract MyToken is ERC721 {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIdCounter;

    constructor() ERC721("TOKEN_NAME", "TOKEN_SYMBOL") {}

    function _baseURI() internal pure override returns (string memory) {
        return "YOUR_URL";
    }

    function mintNFT(address to)
        public returns (uint256)
    {
        require(_tokenIdCounter.current() < 2); 
        _tokenIdCounter.increment();
        _safeMint(to, _tokenIdCounter.current());

        return _tokenIdCounter.current();
    }
}
```
<br>
해당 코드는 solidity 로 작성한 smart contract 이다. constructor() 에 자신이 원하는 token 이름과 심볼을 넣어준다. _baseURI() 함수 에서 YOUR_URL 에 아까 작성했던 **pinata** metadata.json 의 URL 을 넣어준다.
<br>
![pinata](/images/minting-nft/metamaskdeploy.png)  
<br>
[Remix](https://remix.ethereum.org/) 에서 해당 contract 를 **metamask** 지갑과 연결 한뒤에 **sepolia testnet** 에 **deploy** 해주고 **mintNFT 함수**에 자신의 지갑 주소를 넣고 실행 시켜 주면 끝!

![pinata](/images/minting-nft/mintingnft.png)  
<br>
### NFT 확인하기

1. **metamask wallet**: 자신의 metamask 에서 NFT 섹션에 NFT 가져오기 버튼을 눌러서 deploy 된 contract 의 주소와 아이디를 넣어주고 가져와 주면 metamask 지갑에서 볼 수 있다. 아이디는 1 을 입력 하면되는데 이는 위에 코드에서 **_tokenIdCounter** 이다.

![pinata](/images/minting-nft/nftin.png)  

해당 NFT 를 가져오게 되면 metamask 지갑에서 확인 할 수 있다.
<br>

![pinata](/images/minting-nft/nftreceive.png)

2. **opensea**: [opensea testnet](https://testnets.opensea.io/) 에서 배포된 contract 주소를 치면 NFT를 볼 수 있다. Opensea 에서 NFT 를 거래하는 것 도 가능하다.

![pinata](/images/minting-nft/opensea.png)  