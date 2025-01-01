Noir library for homomorphic ElGamal Encryption. Uses embedded curve of the back-end for operations. Generic over plain-text bit-length.

# Examples

```noir
let randomness = EmbeddedCurveScalar::from_bytes(bytes, offset);

// Encrypt a 16-bits length message using recipient's public key.
let ciphertext = ElGamalCiphered::<16>::new(key_public, message, randomness);

// Get the sum of the ciphertexts.
let ciphertext_combined = ciphertext + ciphertext_another;

// Decrypt the cipher-text. The result will be the sum of the plain-texts.
let decrypted = ciphertext_combined.decrypt_to_scalar(key_private);
```

## oracle and bigger messages
For better performance (and any other reason) you might want to do the last step - solving discrete logarithm - outside of Noir and get the scalar via an oracle.
```noir
let (decrypted_point, bit_length) = ciphertext.decrypt_to_point(key_private);

// Call your oracle.
let decrypted_scalar = unsafe {oracle_dl(decrypted_point, bit_length)};

assert_dl(decrypted_scalar, decrypted_point);
```

## bit-length conversion method
If you need to account for a bigger message or the sum of plain-texts may overflow the given limit there's a conversion method.
```noir
let ciphertext_combined = 
    ciphertext.convert::<16+1>() + ciphertext_another.convert::<16+1>();
```

## get bit-length
If you are going to transfer a cipher-text or store it (in a note e.g.) it'd be a good idea to store the bit-length along with it.
```noir
let l = ciphertext.bit_length();
```

# previous work used
* https://github.com/jat9292/noir-elgamal/
* https://github.com/ewynx/noir-elgamal