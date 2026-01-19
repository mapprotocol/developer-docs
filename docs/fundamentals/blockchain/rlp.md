---
title: RLP Serialization
description: 
lang: en
sidebarDepth: 2
---

Recursive Length Prefix (RLP) serialization is widely used in the `MAPO-Relay-Chain`'s execution clients. It's a format for efficiently transmitting data between nodes, and RLP helps standardize this process. The purpose of RLP is to encode arbitrary nested binary data arrays, making it the primary encoding method for serializing objects within the `MAPO-Relay-Chain` execution layer. RLP's sole purpose is to encode the structure, leaving the encoding of specific data types (e.g., strings, floating-point numbers) to higher-level protocols. However, positive integers in RLP must be represented in big-endian binary form without leading zeros (making the integer value zero equivalent to an empty byte array). Deserializing integers with leading zeros is considered invalid. Integer representations of string lengths must also be encoded this way, as well as integers within the payload.
The term `MAPO-Relay-Chain`, hereafter referred to as `MAPO`.

more details[Ethereum Yellow Paper (Appendix B)](https://ethereum.github.io/yellowpaper/paper.pdf#page=19)。

## definition

The recursive length prefix encoding function takes an item as its argument. An item is defined as:

- A string, which is a byte array.
- A list of items is also an item.
  
For example, all of the following are items:

- An empty string.
- A string containing the word "cat."
- A list containing any number of strings.
- More complex data structures like `["cat", ["puppy", "cow"], "horse", [[]], "pig", [""], "sheep"]`.
- 
Please note that in the context of the rest of this page, the term "string" means "a certain amount of binary data bytes"; no special encoding is used, and no information about the content of the string is implied.

The definition of Recursive Length Prefix (RLP) encoding is as follows:

- For values in the range `[0x00, 0x7f]` (decimal `[0, 127]`), a single byte represents its RLP encoding.

- If the length of a string is between 0 and 55 bytes, the RLP encoding consists of a single byte with a value in the range `[0x80, 0xb7]` (decimal `[128, 183]`), followed by the string itself. The first byte's value is **0x80** (decimal 128) plus the length of the string.

- If the length of a string is greater than 55 bytes, the RLP encoding starts with a single byte with a value of **0xb7** (decimal 183), followed by the binary length of the string in bytes, then the string itself. For example, a 1024-byte string is encoded as `\xb9\x04\x00` (decimal `185, 4, 0`), where `0xb9` (183 + 2 = 185) is the first byte, followed by 2 bytes representing the actual string length, `0x0400` (decimal 1024). So, the first byte's value is in the range `[0xb8, 0xbf]` (decimal [184, 191]).

- If the total payload length of a list (that is, the combined length of all its recursively length-prefix-encoded items) is 0-55 bytes, then the recursive length-prefix encoding consists of a single byte with the value **0xc0**, plus the list length, Followed by a string of items recursively length-prefixed encoding. Therefore, the range of the first byte is `[0xc0, 0xf7]` (decimal `[192, 247]`)..

- If the total payload length of a list is greater than 55 bytes, the RLP encoding starts with a single byte with a value of **0xf7** (decimal 247), followed by the binary length of the payload in bytes, then the payload itself, and finally the RLP-encoded items. The first byte's value is in the range `[0xf8, 0xff]` (decimal `[248, 255]`).

the code is：

```python
def rlp_encode(input):
    if isinstance(input,str):
        if len(input) == 1 and ord(input) < 0x80: return input
        else: return encode_length(len(input), 0x80) + input
    elif isinstance(input,list):
        output = ''
        for item in input: output += rlp_encode(item)
        return encode_length(len(output), 0xc0) + output

def encode_length(L,offset):
    if L < 56:
         return chr(L + offset)
    elif L < 256**8:
         BL = to_binary(L)
         return chr(len(BL) + offset + 55) + BL
    else:
         raise Exception("input too long")

def to_binary(x):
    if x == 0:
        return ''
    else:
        return to_binary(int(x / 256)) + chr(x % 256)
```

## examples

- String “dog”= [ 0x83, 'd', 'o', 'g' ]
- List [ "cat", "dog" ] = `[ 0xc8, 0x83, 'c', 'a', 't', 0x83, 'd', 'o', 'g' ]`
- Empty string ('null') = `[ 0x80 ]`
- Empty list = `[ 0xc0 ]`
- Integer 0 = `[ 0x80 ]`
- Encoded integer 0 ('\\x00') = `[ 0x00 ]`
- Encoded integer 15 ('\\x0f') = `[ 0x0f ]`
- Encoded integer 1024 ('\\x04\\x00') = `[ 0x82, 0x04, 0x00 ]`
- [The set-theoretic representation](http://en.wikipedia.org/wiki/Set-theoretic_definition_of_natural_numbers) of the number 3 is commonly expressed as，`[ [], [[]], [ [], [[]] ] ] = [ 0xc7, 0xc0, 0xc1, 0xc0, 0xc3, 0xc0, 0xc1, 0xc0 ]`
- String “Lorem ipsum dolor sit amet, consectetur adipisicing elit”= `[ 0xb8, 0x38, 'L', 'o', 'r', 'e', 'm', ' ', ... , 'e', 'l', 'i', 't' ]`

## rlp-decoding

According to the rules and process of the RLP encoding, the input for recursive length prefix decoding is treated as a binary data array. The recursive length prefix decoding process proceeds as follows:

1.  Decode the data type, actual data length, and offset based on the first byte (the prefix) of the input.

2.  Decode the data accordingly based on the data type and offset.

3.  Continue decoding the remaining part of the input.
The rules for decoding the data type and offset are as follows:

1.  If the range of the first byte (the prefix) is [0x00, 0x7f], the data is a string, and the string itself is the first byte.

2.  If the range of the first byte is [0x80, 0xb7], the data is a string, and the first byte is followed by a string with a length equal to the first byte minus 0x80.

3.  If the range of the first byte is [0xb8, 0xbf], the data is a string, and the first byte is followed by a string length equal to the first byte minus 0xb7, with the string following the string length.

4.  If the range of the first byte is [0xc0, 0xf7], the data is a list, and the first byte is followed by the recursive length prefix encoding strings of all items in the list. The total payload of the list is equal to the first byte minus 0xc0.

5.  If the range of the first byte is [0xf8, 0xff], the data is a list, and the first byte is followed by a payload length equal to the first byte minus 0xf7. The recursive length prefix encoding strings of all items in the list are then appended after the total payload of the list.

the code is：

```python
def rlp_decode(input):
    if len(input) == 0:
        return
    output = ''
    (offset, dataLen, type) = decode_length(input)
    if type is str:
        output = instantiate_str(substr(input, offset, dataLen))
    elif type is list:
        output = instantiate_list(substr(input, offset, dataLen))
    output + rlp_decode(substr(input, offset + dataLen))
    return output

def decode_length(input):
    length = len(input)
    if length == 0:
        raise Exception("input is null")
    prefix = ord(input[0])
    if prefix <= 0x7f:
        return (0, 1, str)
    elif prefix <= 0xb7 and length > prefix - 0x80:
        strLen = prefix - 0x80
        return (1, strLen, str)
    elif prefix <= 0xbf and length > prefix - 0xb7 and length > prefix - 0xb7 + to_integer(substr(input, 1, prefix - 0xb7)):
        lenOfStrLen = prefix - 0xb7
        strLen = to_integer(substr(input, 1, lenOfStrLen))
        return (1 + lenOfStrLen, strLen, str)
    elif prefix <= 0xf7 and length > prefix - 0xc0:
        listLen = prefix - 0xc0;
        return (1, listLen, list)
    elif prefix <= 0xff and length > prefix - 0xf7 and length > prefix - 0xf7 + to_integer(substr(input, 1, prefix - 0xf7)):
        lenOfListLen = prefix - 0xf7
        listLen = to_integer(substr(input, 1, lenOfListLen))
        return (1 + lenOfListLen, listLen, list)
    else:
        raise Exception("input does not conform to RLP encoding form")

def to_integer(b):
    length = len(b)
    if length == 0:
        raise Exception("input is null")
    elif length == 1:
        return ord(b[0])
    else:
        return ord(substr(b, -1)) + to_integer(substr(b, 0, -1)) * 256
```
