investdex.io is a platform that lists decentralised cryptocurrencies on uniswap and pancake swap (Two exchanges which trade crypto)Uniswap and pancake swap have APIs/GraphQL endpoints.

Uniswap endpoint is as follows: https://thegraph.com/explorer/subgraph/uniswap/uniswap-v2?selected=playground

Example query: 
```
{
  pairs (orderBy:totalSupply, orderDirection:desc) {
    id
    token0{
      id
      symbol
      totalSupply
      txCount
    }
    token1{
      id
      symbol
      totalSupply
      txCount
    }
  }
}
```

The graphQL endpoint can be queried on express/node.js to access the data. 

Create an Express.js backend system with the following: 

1. Query all Tokens available on uniswap using the “Token” schema, save it to mongoDB every 30 minutes. The existing tokens must be updated.

2. Create an endpoint to access the tokens sorted by “tradeVolumeUSD”, “totalLiquidity” and “untrackedVolumeUSD”. The sorting filters need to be included in the query string. 

For example: 
```
/tokens?sortBy=tradeVolumeUSD&limit=100&page=2 
```


3. Query swaps in the last 1 hour on uniswap using the “Swap” schema.

Base Query: 
```
 {
  swaps(orderBy:timestamp) {
    id
    timestamp
    amount0In
    amount1In
    amount0Out
    amount1Out
    amountUSD
    pair{
      id
      token0{
        symbol
        decimals
      }
      token1{
        symbol
        decimals
      }
    }
  }
}

```
Update the above query to filter it by timestamp and amountUSD such that the response returned is: 
-  sorted by timestamp
- includes results in the last 4 hours
- amountUSD > 10000


4. Create an API to query "Bundle" with the latest ethPrice. Store the price to Redis using redis ```setex```  ,  cache for 30 seconds. On the next API call, check if redis is empty, if so query "Bundle" again, else return data stored in redis. 
