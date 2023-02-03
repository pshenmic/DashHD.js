# hdkey

[![NPM Package](https://img.shields.io/npm/v/hdkey.svg?style=flat-square)](https://www.npmjs.org/package/hdkey)
[![build status](https://secure.travis-ci.org/cryptocoinjs/hdkey.svg)](http://travis-ci.org/cryptocoinjs/hdkey)
[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg)](http://standardjs.com/)

A JavaScript component for
[BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki)(hierarchical
deterministic keys).

## Installation

```bash
npm i --save hdkey
```

## Usage

**example:**

```js
var HDKey = require("hdkey");
var seed =
  "a0c42a9c3ac6abf2ba6a9946ae83af18f51bf1c9fa7dacc4c92513cc4dd015834341c775dcd4c0fac73547c5662d81a9e9361a0aac604a73a321bd9103bce8af";
var hdkey = await HDKey.fromMasterSeed(Buffer.from(seed, "hex"));
console.log(hdkey.privateExtendedKey);
// => 'xprv9s21ZrQH143K2SKJK9EYRW3Vsg8tWVHRS54hAJasj1eGsQXeWDHLeuu5hpLHRbeKedDJM4Wj9wHHMmuhPF8dQ3bzyup6R7qmMQ1i1FtzNEW'

var xpub = await hdkey.getPublicExtendedKey();
console.log(xpub);
// => 'xpub661MyMwAqRbcEvPmRAmYndzERhyNux1GoHzHxgzVHMBFkCro3kbbCiDZZ5XabZDyXPj5mH3hktvkjhhUdCQxie5e1g4t2GuAWNbPmsSfDp2'
```

### `await HDKey.fromMasterSeed(seedBuffer[, versions])`

Creates an `hdkey` object from a master seed buffer. Accepts an optional
`versions` object.

```js
var seed =
  "a0c42a9c3ac6abf2ba6a9946ae83af18f51bf1c9fa7dacc4c92513cc4dd015834341c775dcd4c0fac73547c5662d81a9e9361a0aac604a73a321bd9103bce8af";
var hdkey = await HDKey.fromMasterSeed(Buffer.from(seed, "hex"));
```

### `await HDKey.fromExtendedKey(extendedKey[, versions, skipVerification])`

Creates an `hdkey` object from a `xprv` or `xpub` extended key string. Accepts
an optional `versions` object & an optional `skipVerification` boolean. If
`skipVerification` is set to true, then the provided public key's x (and y if
uncompressed) coordinate will not will be verified to be on the curve.

```js
var key =
  "xprvA2nrNbFZABcdryreWet9Ea4LvTJcGsqrMzxHx98MMrotbir7yrKCEXw7nadnHM8Dq38EGfSh6dqA9QWTyefMLEcBYJUuekgW4BYPJcr9E7j";
var hdkey = await HDKey.fromExtendedKey(key);
```

**or**

```js
var key =
  "xpub6FnCn6nSzZAw5Tw7cgR9bi15UV96gLZhjDstkXXxvCLsUXBGXPdSnLFbdpq8p9HmGsApME5hQTZ3emM2rnY5agb9rXpVGyy3bdW6EEgAtqt";
var hdkey = HDKey.fromExtendedKey(key);
```

---

### `await hdkey.derive(path)`

Derives the `hdkey` at `path` from the current `hdkey`.

```js
var seed =
  "fffcf9f6f3f0edeae7e4e1dedbd8d5d2cfccc9c6c3c0bdbab7b4b1aeaba8a5a29f9c999693908d8a8784817e7b7875726f6c696663605d5a5754514e4b484542";
var hdkey = await HDKey.fromMasterSeed(Buffer.from(seed, "hex"));
var childkey = await hdkey.derive("m/0/2147483647'/1");

var xprv = await childkey.getPrivateExtendedKey();
console.log(xprv);
// -> "xprv9zFnWC6h2cLgpmSA46vutJzBcfJ8yaJGg8cX1e5StJh45BBciYTRXSd25UEPVuesF9yog62tGAQtHjXajPPdbRCHuWS6T8XA2ECKADdw4Ef"

var xpub = await childkey.getPublicExtendedKey();
console.log(xpub);
// -> "xpub6DF8uhdarytz3FWdA8TvFSvvAh8dP3283MY7p2V4SeE2wyWmG5mg5EwVvmdMVCQcoNJxGoWaU9DCWh89LojfZ537wTfunKau47EL2dhHKon"
```

Newer, "hardened" derivation paths look like this:

```js
// as defined by BIP-44
var childkey = await hdkey.derive("m/44'/0'/0'/0/0");
```

### `await hdkey.sign(hash)`

Signs the buffer `hash` with the private key using `secp256k1` and returns the
signature as a buffer.

### `await hdkey.verify(hash, signature)`

Verifies that the `signature` is valid for `hash` and the `hdkey`'s public key
using `secp256k1`. Returns `true` for valid, `false` for invalid. Throws if the
`hash` or `signature` is the wrong length.

### `hdkey.wipePrivateData()`

Wipes all record of the private key from the `hdkey` instance. After calling
this method, the instance will behave as if it was created via
`HDKey.fromExtendedKey(xpub)`.

### `hdkey.getPrivateKey()`

Get the `hdkey`'s private key, stored as a buffer.

### `hdkey.publicKey`

Get the `hdkey`'s public key, stored as a buffer.

### `await hdkey.getPrivateExtendedKey()`

Get the `hdkey`'s `xprv`, stored as a string.

### `await hdkey.getPublicExtendedKey()`

Get the `hdkey`'s `xpub`, stored as a string.

## References

- https://github.com/bitcoinjs/bitcoinjs-lib/blob/master/src/hdnode.js
- http://bip32.org/
- http://blog.richardkiss.com/?p=313
- https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki
- http://bitcoinmagazine.com/8396/deterministic-wallets-advantages-flaw/

## License

MIT
