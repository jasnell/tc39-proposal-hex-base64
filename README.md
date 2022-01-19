# Hex and Base64 TypedArray toString options

## The Problem

It is common for applications to exchange binary data in Base64, Base32, or Hex format. Unfortunately, there are no standard APIs for doing so. Node.js includes support for hex and base64 options in it's non-standard `Buffer` API. Deno has it's own separate APIs for this. Browser developers have to choose from various user land options of varying quality. These encodings are standardized and common enough that options should be provided directly by the language.

## Proposal

Add new `TypedArray.prototype.toEncodedString()` and `TypedArray.fromEncodedString()` methods for encoding and decoding `TypedArray` data to hex, base64, or base32 formats.

```
TypedArray.prototype.toEncodedString('hex' | 'base64' | 'base64url' | 'base32')

TypedArray.fromEncodedString('...', 'hex' | 'base64' | 'base64url' | 'base32')
```

When using `fromEncodedString()` to create an instance of a `TypedArray`, the decoded length of the input string mod the TypedArray types element size must equal zero or a `RangeError` must be thrown.

For example:

```
// This works... because element size of Uint8Array is 1
Uint8Array.fromEncodedString('q83v', 'base64');  // Ok!

// But this fails... because element size of Uint32Array is 4, and q83v expands to only 3 bytes
Uint32Array.fromEncodedString('q83v', 'base64');  // RangeError!
```

## Examples

```
const u8 = Uint8Array.fromEncodedString('abcdef', 'hex'); // 171, 205, 239

console.log(Uint8Array.from([171, 205, 239]).toEncodedString('hex')); // abcdef
```
