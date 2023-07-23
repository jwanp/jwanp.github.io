---
title: "Block Chain Oracles"
date: 2023-07-23T19:12:15+09:00
draft: false
tags: ["block-chain","smart-contract","block-chain-oracle"]
categories: ["block-chain"]
featuredImage: /images/block-chain-oracle/blockchainoracleDalle.png
---

# Blockchain Oracle

## Oracle 은 무었인가?

[Chainlink Education](https://chain.link/education/blockchain-oracles)

>Blockchain oracles are entities that connect [blockchains](https://chain.link/education-hub/blockchain) to external systems, thereby enabling [smart contracts](https://chain.link/education/smart-contracts) to execute based upon inputs and outputs from the real world.

스마트 계약이 블록체인 외부의 데이터를 가져올 수 있게 연결해주는 entity 이다. 또한, 블록체인 안의 데이터 뿐만 아니라 외부의 데이터와 상호작용 할 수 있는 스마트 계약을 [hybrid smart contract](https://chain.link/education-hub/hybrid-smart-contracts) 라고 부른다. 

#### Smart Contract 의 양면성

**Smart Contract** 는 blockchain 위에서 동작하기 때문에 **탈중앙화**가 되어있고 누군가가 **임의적으로 데이터를 수정**하지 못하고 **코드에 의해서만 동작한다.** 따라서 신뢰성이 높은 반면에 스마트계약은 **블록체인 밖에 있는 정보**를 가져오지 못한다는 문제점이 있다. 하지만 smart contract 를 만들다보면 외부의 정보가 필요할때가 있다.

웹에서는 HTTP protocol 등으로 서로 상호작용 하면서 데이터를 주고 받는다. 이처럼 스마트계약에서 **블록체인 외부의 정보**와 상호작용할때 쓰이는 것이 **Oracle** 이리고 생각된다.

예를들면 이더리움 시세 예측 배팅을 위한 escrow 시스템으로 스마트계약을 만들 수 있다. 이때 사용자가 예측을 하고나서 이더리움 시세를 가져오고 싶지만 블록체인 안에는 해당 정보가 없다. 따라서 블록체인 외부 서버와 통신을 해야하는데 이때 오라클이 쓰인다.

### Chainlink

[**Chainlink**](https://chain.link/) 는 블록체인안에서 압도적으로 많은 사용자를 보유하고 있는 BlockChain Oracle network 이다. Chainlink 를 이용해서 smart contract 에서 상호작용 하고 싶다면 [Chainlink tutorial](https://docs.chain.link/getting-started/conceptual-overview) 을 참고하라.

이더리움 시세예측 배팅 Smart contract 를 만들때 썼던 **API call** 을 이용한 코드의 일부를 보면서 실제로 어떻게 쓰이는지 간단하게 설명하겠다.

1. Jobs - 해당 jobid 에게 request 를 보내면 요청한 request 를 수행한후 response 를 스마트 계약에 다시 보내준다.

   해당 코드에서 API Call 을 위한 **Job id 는 "ca98366cc7314957b8c012c72f05aeeb"** 이다.

2. Job 이 수행할 요청은 **req 변수**에 저장되어 보내지고 job 이 결과값을 response 하면 **fullfill 함수**에서 parameter 값으로 이를 받아서 처리한다.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

import "@chainlink/contracts/src/v0.8/ChainlinkClient.sol";
import "@chainlink/contracts/src/v0.8/ConfirmedOwner.sol";
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/utils/Strings.sol";

contract PredictGame is ChainlinkClient, ConfirmedOwner {
    using Chainlink for Chainlink.Request;
		ERC20 private ERCinstance;    // ERC20 토큰을 다루기 위한 인터페이스
    address public tokenAdress; // This is the token address
    address payable public host; // This is the client
    // 토큰을 보냈을때 로그
    event Transfer(address indexed _from, address indexed _to, uint256 _value); 
    event Approval(address indexed _owner, address indexed _spender, uint256 _value); 
    // getDaysEthUsdPrices 를 실행할때 request 를 보내는 url 의 로그를 찍는 이벤트
    event APIurl(string api); 

    //variables used for oracle
    address private oracle; // 오라클 주소
    bytes32 private jobId; // jobid
    uint256 private fee; // link fee
    
    event RequestVolume(bytes32 indexed requestId, uint256 volume);
    // 20230607 -> 0 (해당 날짜를 모두 ethUsdPrices 에 저장하면 6이 저장된다.)
		mapping(uint256 => uint8) private datetemp; 
		// 20230627 -> [가격1, 가격2, 가격3, 가격4]. (시작날자 -> 가격 4개 매핑)
    mapping(uint256 => uint256[4]) private ethUsdPrices; 
		constructor() ConfirmedOwner(msg.sender){
		        // 배포된 tokenAddress 를 입력
		        tokenAdress = 0x7caEd854Dac539EE7E28a3E42Cdc68F32665F7A6; 
		        ERCinstance = ERC20 (tokenAdress); // ERC20 인터페이스로 해당 token address 를 감싼다.
		        host = payable(msg.sender);
		        //default values
		        setChainlinkToken(0x779877A7B0D9E8603169DdbD7836e478b4624789);
		        setChainlinkOracle(0x6090149792dAAeE9D1D568c9f9a6F6B46AA29eFD);
		        jobId = "ca98366cc7314957b8c012c72f05aeeb"; 
		        fee = (1 * LINK_DIVISIBILITY) / 10; // 0,1 * 10**18 (Varies by network and job)
		}

		function getDaysEthUsdPrices(uint256 _timestamp) internal returns(bytes32 requestId) { 
	        Chainlink.Request memory req = buildChainlinkRequest(
	            jobId,
	            address(this),
	            this.fulfill.selector
	        );
	        req.add(
	            "get",
	            string(abi.encodePacked("https://min-api.cryptocompare.com/data/pricemultifull?fsyms=ETH&tsyms=USD&ts=", Strings.toString(_timestamp)))
	            
	        );
	        // requst를 보낸 URL 을 확인하는 로그를 찍는다.
	        emit APIurl(string(abi.encodePacked("https://min-api.cryptocompare.com/data/pricemultifull?fsyms=ETH&tsyms=USD&ts=", Strings.toString(_timestamp))));
	
	        // Set the path to find the desired data in the API response
	        req.add("path", "RAW,ETH,USD,VOLUME24HOUR");
	
	        // Multiply the result by 1000000000000000000 to remove decimals
	        int256 timesAmount = 10 ** 18;
	        req.addInt("times", timesAmount);
	
	        // Sends the request
	        return sendChainlinkRequest(req, fee);
		}

		function fulfill(bytes32 _requestId, uint256 _price) public recordChainlinkFulfillment(_requestId) {
        // Parse the requestId to get the day index (0-6) for the price
        emit RequestVolume(_requestId, _price);
        
        // datetemp[startdate] 으로 startdate 해당하는 가격을 어디까지 불러왔는지 알 수 있다.
        //ethUsdPrices[20230627] 이 [2131511,23155,0,0] 이면 datetemp[20230627] 의 값은 2 
				// 예 datetemp[20230627] 의 값이 2 이면 20230627 에 시작하는 날짜의 가격
        if(datetemp[getYearMonthDay(block.timestamp)] == 0){ 
            ethUsdPrices[getYearMonthDay(block.timestamp)][datetemp[getYearMonthDay(block.timestamp)]++] = _price; 
        } 
        // 전에 불러왔던 가격이 현재 받은 가격과 다르면 ethUsdPrices 에 저장. 
        // 두개의 가격이 같으면 같은 response 를 받은 것이다.
        else if(ethUsdPrices[getYearMonthDay(block.timestamp)][datetemp[getYearMonthDay(block.timestamp) - 1]] != _price){
            ethUsdPrices[getYearMonthDay(block.timestamp)][datetemp[getYearMonthDay(block.timestamp)]++] = _price; 
        }
        
		}

		function getYearMonthDay(uint256 timestamp) internal pure returns (uint256) {
		// 31556926 is the number of seconds in a year
        uint256 year = (timestamp / 31556926) + 1970; 
        // 2629743 is the number of seconds in a month
        uint256 month = (timestamp / 2629743) % 12; 
        uint256 day = (timestamp / 86400) % 31; // 86400 is the number of seconds in a day

        // Combine the components into a single uint256 value
        uint256 date = (year * 10000) + (month * 100) + day;
        return date;
    }
		
```

|                mapping variables                |                             설명                             |
| :---------------------------------------------: | :----------------------------------------------------------: |
|     mapping(uint256 => uint8) **datetemp**      |   해당 날짜에 해당하는 req 가 완료될때마다 1 씩 증가한다.    |
| mapping(uint256 => uint256[4]) **ethUsdPrices** | 해당 날짜에 해당하는 req 가 완료될때마다 <br />ETH/USD 시세를 저장한다. |

|                   함수 variables                    |                             설명                             |
| :-------------------------------------------------: | :----------------------------------------------------------: |
| **getYearMonthDay**(uint256 timestamp) returns date |    timestamp 를 날짜로 바꿔준다. ex) 16782917 → 20200702     |
|     **getDaysEthUsdPrices**(uint256 _timestamp)     | 해당 timestamp 에 해당하는 ETH/USD 시세에 대한 API call 을 보낸다. |
|   **fulfill**(bytes32 _requestId, uint256 _price)   |       response 를 받아서 스마트 계약 변수에 저장한다.        |

해당 smart contract 가 궁금하면 [PredictGame.sol](https://github.com/jwanp/SKKUOracleTeam3/blob/main/contracts/PredictGame.sol) 를 참고