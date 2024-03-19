# lfsr-pw

A steganographic algorithm that uses public-key cryptography. It can be used
either for watermarking or covert cryptographic communication. This algorithm
uses parity modulation to minimise how much the image is modified and linear
feedback shift registers to provide pseudorandomness.

### Parity Modulation
An odd value encodes '1' and an even value '0'.
For each member of an array of values, either add or subtract one to ensure the
value is odd or even as needed.

## Watermarking Method
1. Create a cryptographic signature of the image.
2. Split this signature into two halves.
3. Use a linear feedback shift register to generate two pseudorandom streams.
4. Use these streams to generate coordinates.
5. For each coordinate, take a pixel||chunk.
6. For each pixel||chunk, take the three (e.g. RGB) or four (e.g. RGBA) channels.
7. Use parity modulation to encode the signature into the image.

## Public Key Cryptography Method
1. Obtain the receiver's public key.
2. Encrypt the secret with this key.
3. Split the key into two and feed the two halves into two linear feedback shift
registers.
4. Use these streams to generate coordinates.
5. For each coordinate, take a pixel||chunk.
6. For each pixel||chunk, take the three (e.g. RGB) or four (e.g. RGBA) channels.
7. Use parity modulation to encode the encrypted secret into the image.

## Notes
- This algorithm works best with original, colorful and large images.
- Having four channels is convenient as then one can encode a whole hexadecimal
number.
- The algorithm leaves to the implementation the following details:
  + What constitutes a 'chunk.' Some formats like PNG and BMP have this as part of their specifications.
  + How the lfsr streams are used to generate coordinates. If the method used can 
    lead to rare duplicates, then either the x or y coordinate can be increased until an unmodified chunk is found.
    The downside is that this involves keeping track of used values.
  + Whether and when 1 is added or subtracted.

## Theorised Attacks
- Frequency analysis. It is possible that this method is vulnerable to being detected by
  analysing how many odd and even chunks it has compared to most images in general.
  + This may be mitigated by using original and colorful images. 
    A chi-rho test is suggested to detect an image's suitability.
- Repeated coordinates. If a public key is used, then the same coordinates will be used each time.
  + This can be mitigated somewhat by using the last value used as input the next time to the LFSRs.
    However there are only so many coordinates that LFSRs will give before they finish their period.
- Noise introduction. If this method is used for watermarking, it is a trivial thing to randomly
  change the parity of parts of the image and destroy the hidden signature.
