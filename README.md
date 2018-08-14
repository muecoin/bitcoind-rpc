bitcoind-rpc-mue.js
===============

[![NPM Package](https://img.shields.io/npm/v/bitcoind-rpc-mue.svg?style=flat-square)](https://www.npmjs.org/package/bitcoind-rpc-mue)
[![Build Status](https://img.shields.io/travis/muecoin/bitcoind-rpc-mue.svg?branch=master&style=flat-square)](https://travis-ci.org/muecoin/bitcoind-rpc-mue)
[![Coverage Status](https://img.shields.io/coveralls/muecoin/bitcoind-rpc-mue.svg?style=flat-square)](https://coveralls.io/r/muecoin/bitcoind-rpc-mue?branch=master)

A client library to connect to MonetaryUnit Core RPC in JavaScript.

## Get Started

bitcoind-rpc-mue.js runs on [node](http://nodejs.org/), and can be installed via [npm](https://npmjs.org/):

```bash
npm install bitcoind-rpc-mue
```

## Examples 

```javascript
var run = function() {
  var bitcore = require('bitcore-mue');
  var RpcClient = require('bitcoind-rpc-mue');

  var config = {
    protocol: 'http',
    user: 'user',
    pass: 'pass',
    host: '127.0.0.1',
    port: '29948',
  };

  var rpc = new RpcClient(config);

  var txids = [];

  function showNewTransactions() {
    rpc.getRawMemPool(function (err, ret) {
      if (err) {
        console.error(err);
        return setTimeout(showNewTransactions, 10000);
      }

      function batchCall() {
        ret.result.forEach(function (txid) {
          if (txids.indexOf(txid) === -1) {
            rpc.getRawTransaction(txid);
          }
        });
      }

      rpc.batch(batchCall, function(err, rawtxs) {
        if (err) {
          console.error(err);
          return setTimeout(showNewTransactions, 10000);
        }

        rawtxs.map(function (rawtx) {
          var tx = new bitcore.Transaction(rawtx.result);
          console.log('\n\n\n' + tx.id + ':', tx.toObject());
        });

        txids = ret.result;
        setTimeout(showNewTransactions, 2500);
      });
    });
  }

  showNewTransactions();
};
```

## License

**Code released under [the MIT license](https://github.com/bitpay/bitcore/blob/master/LICENSE).**

Copyright 2013-2014 BitPay, Inc.
