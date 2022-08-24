---
description: A standard allowing Wallets to enforce security policies for dApps.
---

# ENSIP-14: Security Policy Records

| **Author**    | Bernard Wagner(@bernard-wagner) |
| ------------- | ----------------------- |
| **Status**    | Draft                   |
| **Submitted** | TBC                     |

### Abstract

This ENSIP defines a protocol for Wallet providers to query DNS-in-ENS records for a security policy that can be applied to validate the properties of transaction signing requests. The objective is to mitigate the impact of front-end hacks such as DNS takeovers or supply-chain compromise.

### Motivation

Hackers often target dApp front-ends to coerce users into signing transactions that allow the hacker to transfer the victim's funds. 

### Specification

#### DNS-in-ENS Text Records

Introduce a well-known global TXT recods that allows Wallet providers to discover the security policy for a specific dApp using on-chain data. 

```
ensip-14: uri=https://mywebapp.xyz/.well-known/ensip-14.json; hash=hex-string; 
```

URI can either use `https`, `ipfs` or `data` protocol handler.

#### Policy Standard

```
{
  "report": "https://mywebapp.xyz/report", // URL to notify incase transactions are blocked
  "rules": [{
      "signature": "0x.....", // 4-byte signature
      "abi": "function approve(address, uint256)", // include payable keyword if transaction should permit sending ETH
      "inputs": [{
        "index": 0,
        "operator": "eq", // "lt", "lte", "gt", "gte"
        "value": "0x000000",
       }],
      "targets": ["0x00000000"], // valid "to" property of transaction, if unspecified defaults to any
  }]
}
```

#### Wallet logic

1. Obtain dApp hostname, either using HTTP Origin header or based on browser context.
2. Query `ensip-14` TXT record for hostname.
3. Fetch policy document from `uri` property.
4. Validate integrity of policy document using `digest` property.
5. Parse unsigned transaction by iterating over policy and evaluating based on 4byte signature.
6. If no rule match, send raw unsigned transaction to reporting url. `https://mywebapp.xyz/report?tx=raw`

### Rationale

### Backwards Compatibility

Not applicable.

### Security Considerations

None.

### Copyright

Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
