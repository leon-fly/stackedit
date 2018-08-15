## Bitcoin

### Bitcoin consists of
 - A decentralized peer-to-peer network (**the bitcoin protocol**)
 - A public transaction ledger (**the blockchain**)
 - A set of rules for independent transaction validation and currency issuance (**consensus rules**)
 - A mechanism for reaching global decentralized consensus on the valid blockchain (**Proof-of-Work algorithm**)

### History of Bitcoin
 - "Bitcoin: A Peer-to-Peer Electronic Cash System", 2008, Satoshi Nakamoto
 - "Byzantine Generals" problem: Satoshi Nakamoto's solution, which uses the concept of Proof-of-Work to achieve consensus without a central trusted authority.

### How Bitcoin Works

**Traditional banking and payment system** - Centralized
[How does money transfer work?](https://www.quora.com/How-does-money-transfer-between-banks-and-different-countries-work)

### Keys and Address

#### Private Key
256 bits as 64 hexadecimal digits:

    1E99423A4ED27608A15A2616A2B0E9E52CED330AC530EDCC32C8FFC6A526AEDD

#### Public Key

[Elliptic Curve Cryptography](https://www.wolframalpha.com/input/?i=Elliptic%20Curve)

> K=k*G

k is the private key, G is the generator point.

#### Bitcoin Address
 - A bitcoin address can represent the owner of a private/public key pair, or it can represent some‐ thing else, such as a payment script. 
 - The bitcoin address is what appears most commonly in a transaction as the “recipient” of the funds.
 - A Bitcoin address is a unique number that “holds” bitcoin currency. You use the address to receive and send bitcoins.

> A = RIPEMD160(SHA256(K))  

where K is the public key and A is the resulting bitcoin address.

**P2PKH (pay-to-public-key hash) Address**
 - bitcoin address that begin with '1'
 - one public key hash and one private key signature as proof of ownership

**P2SH (pay-to-script hash)**
 - bitcoin address that begin with '3'
 - require M signatures from a total of N keys
> script hash = RIPEMD160(SHA256(script))

#### Encoding & Format

**Base58 encoding**
Base58 is Base64 without the 0 (number zero), O (capital o), l (lower L), I (capital i), and the symbols “+” and “/”.

**Keys formats**
 - private key
	 - raw 32 bytes
	 - hex 64 hexadecimal digits
	 - WIF prefix '5', Base58Check encoding: Base58 with version prefix of 128- and 32-bit checksum
 - public key
	 - a point (x, y) on an elliptic curve 

#### Vanity Addresses
Vanity addresses are valid bitcoin addresses that contain human-readable messages.

### Wallet

**Bitcoin wallet** contains keys, not coins. Wallets are keychains containing pairs of private/public keys. Users sign transactions with the keys. The coins are stored on the blockchains in the form of transaction output.

Two primary types of wallets, distinguished by whether the keys they contain are lated to each other or not : 
 1. Nondeterministic Wallet, where each key is independently generated from a random number.
2. Deterministic Wallet, where all keys are derived from a single master key.
 

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwNDcyMzM4MTUsMTI0NDkwNzQ4MiwtMT
M4MTEzNzcwMiwtMzA1Mzc4NDUyLC01MjI4MjIwNzYsMTQzMjY3
NjkwNywxNDcwMzE2NDUyLC0xNTQyNzM4NjY0LC0xMTY0NTAzNz
kzLC0xMzQwMTE5NDMsLTIxMjUxNTMyNDksLTE4MTQzNjQ1Mjdd
fQ==
-->