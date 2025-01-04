# Hashmash

The **Hashmash** challenge involves reconstructing a secret flag (`FLAG`) from two lists, `D1` and `D2`, which are derived from the flag using SHA-256 hashes. The flag is divided into two halves, and each half is processed differently to generate `D1` and `D2`. Below is a detailed explanation of how the problem was solved, along with the solution code.

## Understanding the Problem

**Given Data**
1. `D1`: A list of SHA-256 hashes. Each hash corresponds to the SHA-256 of the first `i+1` characters of the flag, where `i` ranges from 0 to `half-1`. This means `D1` is generated as follows:
```python
D1 = [sha256(FLAG[:i+1]).hexdigest() for i in range(half)]
```
2. `D2`: A list of integers. Each integer is the first byte of the SHA-256 hash of the first `half + i + 1` characters of the flag, where `i` ranges from `0` to `half-1`. This means `D2` is generated as follows:
```python
D2 = [sha256(FLAG[:half + i + 1]).digest()[0] for i in range(half)]
```
**Objective**

Reconstruct the original flag (`FLAG`) using the given `D1` and `D2` lists.

## Approach to Solve the Problem
1. **Determine the Length of the Flag:**
    - The length of `D1` gives us half the length of the flag since `D1` is generated from the first half of the flag.
    - Therefore, the total length of the flag is `2 * len(D1)`.
2. **Reconstruct the First Half of the Flag:**
    - Iterate through each character position in the first half of the flag.
    - For each position, try all printable ASCII characters (32 to 126) and compute the SHA-256 hash of the current candidate string.
    - If the computed hash matches the corresponding hash in `D1`, the character is correct, and we move to the next position.
3. **Reconstruct the Second Half of the Flag:**
    - Use a backtracking approach to reconstruct the second half of the flag.
    - For each character position in the second half, try all printable ASCII characters and compute the SHA-256 hash of the current candidate string (which includes the first half and the current second half).
    If the first byte of the computed hash matches the corresponding value in `D2`, proceed to the next position.
    - Ensure the last character of the flag is `}`. 

## Solution Code 
```python
from hashlib import sha256

# Given data
D1 = ['8ce86a6ae65d3692e7305e2c58ac62eebd97d3d943e093f577da25c36988246b', '901a6dbb9f97c74007595dc1901290775e1529ae3f8d9a5640bbe91c4bf41c93', 'dc2a01319c8546410c711a2e401938939cb4814daac73a67c252ca26868400cb', '15f5a3d0069a9ab88cf387c7fcad24a62a224581b38d8ab9352a30abbefb8f64', '78c29bd5820b5cc5eb3b3c92827871e0bcb6c045859258ec40492d983f650baf', 'a13751379a54d556a80871873dc27644b1f0a66da441c461838b101a46d36f92', 'a3b99dd37325ea2c0d5eb60c76e3a39c660d6b5b440b3eafb9dcf5a1717ca344', '231f7a838f5debde215a2e2c74fad32e79e0782f800bfe39b54dcb06ee9892a2', 'd7b0fa0e74ba2014fe5c23c7e28dba562d43a47baeb6f0a0966c6d46d3f7d0a5', 'f40357c3c2ac5f74139ebc29166d92d6480c9a9c0d1376a9c9ef5b6c9c5b8f5b', 'e8bf443ec5b05a828b1217e6508e5737eb90518b310c23e44c4d092a56d48a34', 'f9a509621e4d9af0aef6b628591a01c2c75dc2cb957b52b89f97ae8926beeaa2', '72b8710e6d475eb802a2f0deba7fc37b3379dd597387fc5bd895942f6ac8c5a1', '2a46859282468dce001a26d0ede083f3c2cd8e8c5e17ed88f58e445c5ded64f5', '3e01dc25eb1baafa87dfa38dd077afce7a354b058f62f51c926f2628aebf1d26', '1fece0a3f260b1ce9151d7c16cdb58cad26ffe110f6be3ff270dc5b77010b83d', '684b8cd562d1741c7dd1c3f6ab06f0c9df0473f8431826386d7a65e302ea832e', 'bb7755dab81ab295c56b40975257c526018df0c9647402568768db3be5831dd0', 'd6f718ead48dbcff45ec8f612961b6818b8615b7631daf7942386d8dd7985967', '33797dfa26db72662d5651eb25d4d6510dec5c46c1a623c3c9322859f1c95847', 'bac9ab3ce066098a0bde23768aa113ecaf07803f084efb9fe8e2c7ec05e2fec1', 'e64831491afbb012f377bb58d6f2776813f217c24876d8b6e60688312e38d9b1', '1115f3d57f8d2f23ead7019747225fbf70045edc847fcc47e55b646405bae549', 'ba8e2a06994d0240ee60809bf86df988f1aa740ed4a0211c71fec5875b84ccc5', '5c7880b1dee83f0d7dec570800fc13e9cc557bd914e880c726b7bb6317b54c64', '6caf52846161319fd353c147f4cee1facf8d2506e62b08fb0229a102311e61c7', '72a3a98f3689314fc08db5d8e7a91652e2440898f4ec2bec6897f41e3267a174', 'ee1a111ab87a1e1e4da3962e20df737b6e34044ff274a3e7ae63d4255d6b7594', '03af69e3a8dccbca188b390e11dd38caff5fd70a23bf2c35e3752616818afac1', 'c71f632e1fdee4ee3ef214b1efdb9b6012547a2088fe54f75adca8d3fa55d02b', '5ac663c39cad50e7036a2a2d2527a65a69627476927ed6a405ab166d875e4fa7', 'f1a408975b72a772e98af08827056fb2fdb6c7082d486bd76b4be3929c8bd069', '0f4582100fc61731d946eaf6b288bbb402b925bd383e421ca028f19ff5839eec', '0580bb32ce7c3b941216cff02f3c19d7b0959dcc317787a3b9c0da439d68d4e2', 'fc4b02185991268a8034c0173df1f11404b037039b0d46599d2db886e5deae86', 'c9ce9ec15c04ba72137e2fa70171d2900ed5917938c14ba63d638971c382bfde', '9943e264c931d150eaad0f92da0373f881600fe9c930abc377d123156e501e7d', 'f74fb4aa537513496d3d4dced35c059b372ca2f6acc937d1b5b38a85799309cc', '3668b0c67347d254987491114cf774babd4d77a88a599bb010f2beb92aadf4f6', '56a7b9a211db73bdb2b42c4a54defab2eaa81e21c5c9bc7addff56cd7b637c11', '28aaea9c3e3e74e0b55fd579bfe894e4fd99ded6d3a8e3f802d20e76510a086a', '9798ad79f78c78f9d8e5a649d7e7778dbf305c30ca368f5a4af9b68ac419878b', 'a9cd3b73e792c3657819b900abb1abc7cccac6db40d3ce6c34f6dbaf6335569d', '3456753cc04eeb48a2b046579f171aa7dbfb24313478625408feae9871f4c5ae', '8f88d817c0aadff8876bfc61c16fd815a8c58b52f31eff1ea9906f606ef0099e', 'cf6fcb9e200b7d2fbd01773efb5e6abad96a63826ed380883ce1c5282a630e6e', '84fa03bc6c1d4d3b9ea3a0f16e9c9df5398a01b09161388474839bd53993b5db', 'd0383a2f3359d6382441141d310dd17554372b10b27b38ef9c13557bcd06383e', '7763d89ec6ef546936652b8b8250a0e81b6c3f1490ae1dfbb829f4080129067f', 'fda6a9e2a9ff0f7f3f35f44915eb831b76dcda291cd5b5b2ca95b9b802cdc77a', 'f5943185b2051b78d3cdd9053b6ac24bff2ded1c3359c92efd5eb266c3474496', '86a1a068ba8293987a7264085b6ae932e08d17e37d169ad1925c5d9c22772c59', '5631140684b89df9a779446c01c5e74c1a159005eb2356407099ed0623377164']
D2 = [61, 40, 86, 13, 146, 121, 101, 199, 108, 169, 239, 16, 155, 40, 205, 205, 239, 211, 52, 138, 167, 159, 52, 21, 145, 245, 119, 115, 46, 111, 166, 69, 104, 27, 52, 50, 194, 23, 166, 61, 32, 55, 119, 20, 27, 9, 27, 194, 62, 199, 111, 47, 102]

# Determine the length of the flag
half = len(D1)
flag_length = 2 * half

# Reconstruct the first half of the flag
flag = ""
for i in range(half):
    for char in range(32, 127):  # ASCII printable characters
        candidate = flag + chr(char)
        if sha256(candidate.encode()).hexdigest() == D1[i]:
            flag += chr(char)
            break

# Reconstruct the second half of the flag using backtracking
def reconstruct_second_half(flag, index):
    if index == half:
        # Ensure the last character is '}'
        if flag[-1] == '}':
            return flag  # Successfully reconstructed the full flag
        else:
            return None  # Invalid last character
    for char in range(32, 127):
        candidate = flag + chr(char)
        if sha256(candidate.encode()).digest()[0] == D2[index]:
            result = reconstruct_second_half(candidate, index + 1)
            if result:
                return result
    return None  # No valid character found for this position

# Start reconstruction from the second half
full_flag = reconstruct_second_half(flag, 0)
if full_flag:
    print(f"Recovered FLAG: {full_flag}")
else:
    print("Failed to reconstruct the full FLAG.")
```

## Explanation of the Code
1. **Reconstructing the First Half:**
    - We iterate through each position in the first half of the flag.
    - For each position, we try all printable ASCII characters and compute the SHA-256 hash of the current candidate string.
    - If the computed hash matches the corresponding hash in `D1`, we add the character to the flag and move to the next position.
2. **Reconstructing the Second Half:**
    - We use a recursive backtracking approach to reconstruct the second half of the flag.
    - For each position, we try all printable ASCII characters and compute the SHA-256 hash of the current candidate string (which includes the first half and the current second half).
    - If the first byte of the computed hash matches the corresponding value in `D2`, we proceed to the next position.
    - The recursion terminates when we reach the end of the second half and ensure the last character is `}`.

## Conclusion
This challenge demonstrates how to reverse-engineer a flag from its SHA-256 hashes using a combination of brute force and backtracking. By carefully analyzing the structure of the problem and implementing an efficient solution, we were able to recover the original flag.