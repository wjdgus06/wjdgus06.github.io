---
title: Class Ciphers
date: 2023-09-13 03:11:51 +09:00
categories: [Study, Crypto]
tags: [symmetric, key]     # TAG names should always be lowercase
math: true
mermaid: true
---

> A. Shift Cipher<br>
	- shift_encrypt(key, msg): It takes a key and a message (example 'abc') and outputs a ciphertext (example 'def').<br>
	- shift_decrypt(key, ctx): It takes a key and a ciphertext and outputs a plaintext.<br>
    B. Vigenere Cipher<br>
    	- vigenere_genkey(n): It takes the length of key and outputs a key.<br>
        - vigenere_encrypt(key, msg): It takes the key generated by vigenere_genkey and a plaintext, and then it outputs a ciphertext.<br>
        - vigenere_decrypt(key, ctx): It takes the key generated by vigenere_genkey and a ciphertext, and then it outputs a plaintext.<br>
C. LFSR Cipher<br>
	- lfsr_genkey(m): It takes the length of lfsr and outputs a key that contains constants {c_i}\_{i=0}^{m-1} and initial values {x\_i}\_{i=1}^{m} where 0 <= c_i < 26 and 0 <= x_i < 26.<br>
	- lfsr_encrypt(key, msg): It takes the key generated by lfsr_genkey and a plaintext, and then it outputs a ciphertext.<br>
	- lfsr_decrypt(key, ctx): It takes the key and a ciphertext, and then it outputs a plaintext.<br>

> When implementing LFSR Cipher, you should use mod 26 instead of mod 2. You also use addition (+) and subtraction (-) instead of XOR.

## PART A) Shift Cipher
### - shift_encrypt(key, msg)
- 키와 메시지를 입력받아 암호문 출력
- c = x + k mod 26
```python
def shift_encrypt(key, msg): #c = x + k mod 26
    n = key % 26
    ct = msg
    for x in range(len(msg)):
        tmp = ord(msg[x]) + n #ord(): alpha -> ascii
        #chr(): ascii -> aplha           
        ct = ct.replace(msg[x], chr(tmp)) #character change
    return ct
```
Key를 입력 받아 mod 26을 하여 n에 저장한다.
Plaintext인 msg의 각 문자에 대해 shift하기 위해 for함수를 적용했고, 문자를 아스키코드로 바꿔주는 함수인 ord()함수를 이용하여 n만큼 더해서 tmp에 저장한다. 이후,다시 아스키코드에서 문자로 변환해주는 chr() 함수를 이용하여 문자로 변환해주고 replace함수로 기존의 문자열의 문자를 shift한 문자로 바꿔준다.

### - shift_decrypt(key, ctx)
- 키와 암호문을 입력받아 복호문 출력
- x = c – k mod 26
```python
def shift_decrypt(key, ctx): #x = c - k mod 26
    n = key % 26
    msg = ctx
    for x in range(len(ctx)):
        tmp = ord(ctx[x]) - n #ord(): alpha -> ascii
        #chr(): ascii -> aplha           
        msg = msg.replace(ctx[x], chr(tmp)) #character change
    return (msg)
```
shift_decrypt는 shift_encrypt함수와 유사하게 진행되며 복호화하는 과정에서 ord()함수를 이용하고 n을 빼서 tmp에 저장한다. 이후 shift_encrypt함수에서 문자열을 구성하는 방식과 동일하게 plaintext를 만든다.
![](/assets/img/_POST/Classic Ciphers/image1.png)

## PART B) Vigenere Cipher
### - vigenere_genkey(n)
- 키의 길이를 입력받아 키 출력
- 𝐾𝐸𝑌(−): (𝑘1, 𝑘2, … , 𝑘𝑛), 𝑛
```python
def vigenere_genkey(n):
    import random
    import string
    key = ""
    for i in range(n):
        key += str(random.choice(string.ascii_lowercase))
    return(key)
```
Key를 랜덤하게 추출하기 위해서 random모듈과 string모듈을 사용한다. Key를 문자열로 반환할 것이기 때문에 key를 빈문자열 형태로 초기화한다. R길이 n의 랜덤한 문자열을 추출하기 위해 for문을 사용하였고 string.ascii_lowercase()함수와 random.choice()함수로 소문자를 랜덤하게 추출하여 key에 저장시켰다.

### - vigenere_encrypt(key, msg)
- vigenere_genkey에서 생성된 키와 일반 텍스트를 입력받아 암호문 출력
- 𝐸𝑁𝐶(𝑋): (𝑐1, 𝑐2, … , 𝑐𝑛) where 𝑐𝑖 = 𝑥𝑖 + 𝑘𝑖 mod 26
```python
def vigenere_encrypt(key, msg):
    ct = msg
    for x in range(len(msg)):
        n = (ord(key[x%len(key)])-ord('a'))% 26
        tmp = ord(msg[x]) + n #ord(): alpha -> ascii
        #chr(): ascii -> aplha
        List_ct = list(ct)
        List_ct[x] = chr(tmp) 
        ct = "".join(List_ct) #replace는 msg[x]와 같은 모든 문자에 대해 변경됨.
        # 해당 cipher는 각 문자마다 key가 변경되므로 replace를 사용할 수 없음
        # 주어진 문자열을 목록으로 변환하고, 이전문자는 지정된 인덱스의 새문자로 대체
        #character change
    return ct
```
Vigenere encrypt를 하기위해 key의 문자를 아스키코드로 변환한 후 문자 a를 빼서 숫자 key를 만들었다. 해당 key만큼 더해서 shift해주고 문자열로 만들어준다. 이때 shift_encrypt에서는 replace()함수를 사용하였지만, 이는 msg[x]와 같은 모든 문자를 치환하기 때문에 각 문자마다 key가 변경되는 vigenere cipher에는 적절하지 않다. 따라서 주어진 문자열을 list로 변환하고 이전 문자는 지정된 index의 새로운 문자로 대체한다.

### - vigenere_decrypt(key, ctx)
- vigenere_genkey에서 생성된 키와 암호문을 입력받아 복호문 출력
- 𝐷𝐸𝐶(𝐶): (𝑥1, 𝑥2, … , 𝑥𝑛) where 𝑥𝑖 = 𝑐𝑖 − 𝑘𝑖 mod 26 
```python
def vigenere_decrypt(key, ctx):
    msg = ctx
    for x in range(len(ctx)):
        n = (ord(key[x%len(key)])-ord('a'))% 26
        tmp = ord(ctx[x]) - n #ord(): alpha -> ascii
        #chr(): ascii -> aplha
        List_msg = list(msg)
        List_msg[x] = chr(tmp) 
        msg = "".join(List_msg)
    return msg
```
Vigenere decrypt는 encrypt함수와 유사하고 단지, 더하기가 아닌 빼기를 하여 복호화를 진행한다.

![](/assets/img/_POST/Classic Ciphers/image2.png)

## PART C) LFSR Cipher
### - lsfr_genkey(m)
- lsfr의 길이를 입력받아 키를 생성
-  inital value설정<br>
	{x\_i}\_{i=1}^{m}<br>
    {c\_i}_{i=0}^{m-1}<br>
- x_{𝑛+𝑚} = c0_x𝑛 + c1\_{x𝑛+1} + c2\_{x𝑛+2} + ⋯ + c𝑚\_{x𝑛+𝑚} 𝑚𝑜𝑑 2<br>
```python
def lfsr_genkey(m):
    import random
    x =[-1]
    c =[]
    for i in range(1, m+1):
        x.append(random.randrange(0, 26))
        #{x_i}_{i=1}^{m} 
    for i in range(m):
        c.append(random.randrange(0, 26))
        #{c_i}_{i=0}^{m-1}
    # intial value, c_i x_i

    for n in range(1, 26):
        tmp = 0
        i = 0
        j = n
        while i < m+n-1 and j < m+n:
            tmp += (c[i]*x[j]) % 26  
            i+=1
            j+=1
        tmp = tmp%26
        x.append(tmp)

    key = ""
    for i in range(1, 26+m):
        tmp = x[i] + ord('a')
        key += chr(tmp)

    return key
```
***키를 생성할 때 mod 2가 아닌 mod 26을 사용한다.*** 먼저 초기값을 설정하기 위해, random함수를 사용하여 0<=x<26의 x를 리스트에 인덱스를 i=1부터 m까지 설정하기 위해 x리스트의 x[0]에 먼저 -1을 삽입한 후 랜덤 값을 입력했다. 0<=c<26 범위의 c를 m개를 인덱스 0부터 m-1까지 랜덤 값을 입력 받았다. 이후 x_{𝑛+𝑚} = c0_x𝑛 + c1\_{x𝑛+1} + c2\_{x𝑛+2} + ⋯ + c𝑚\_{x𝑛+𝑚} 𝑚𝑜𝑑 2 의 식을 이용하여 x\_{𝑛+𝑚}까지의 x 리스트를 만들었다. 이 x값들을 문자열 key로 변환하기 위해 ord()함수와 chr()함수를 이용하였다.

### - lsfr_encrypt(key, msg)
- lsfr_genkey함수에서 생성된 키와 일반텍스트를 입력받아 암호문 출력
- 𝐸𝑁𝐶(𝑋): (𝑐1, 𝑐2, … , 𝑐𝑛) where 𝑐\_𝑖 = 𝑥\_𝑖 𝑥𝑜𝑟 𝑘𝑖 𝑚𝑜𝑑 2
```python
def lsfr_encrypt(key,msg):
    ct = msg
    for x in range(len(msg)):
        n = (ord(key[x%len(key)])-ord('a'))% 26
        tmp = ord(msg[x]) + n
        List_ct = list(ct)
        List_ct[x] = chr(tmp) 
        ct = "".join(List_ct)
    return ct
```
**암호화를 할 때 XOR이 아닌 +를 이용했다.** 기존의 LFSR cipher는 XOR을 이용하여 암호화를 진행하지만 해당 과제에서는 덧셈을 이용하여 암호화를 하였다.

### - lsfr_decrypt(key, ctx)
- lsfr_decrypt함수에서 생성된 키와 암호문을 입력받아 복호문 출력
- 𝐷𝐸𝐶(𝐶): (𝑥1, 𝑥2, … , 𝑥𝑛) where x\_𝑖 = 𝑐\_𝑖 𝑥𝑜𝑟 𝑘𝑖 𝑚𝑜𝑑 2
```python
def lsfr_decrypt(key, ctx):
    msg = ctx
    for x in range(len(ctx)):
        n = (ord(key[x%len(key)])-ord('a'))% 26
        tmp = ord(ctx[x]) - n
        List_msg = list(msg)
        List_msg[x] = chr(tmp) 
        msg = "".join(List_msg)
    return msg
```
**복호화를 할 때 XOR이 아닌 -를 이용했다.** 기존의 LFSR cipher는 XOR을 이용하여 복호화를 진행하지만 해당 과제에서는 뺄셈을 이용하여 복호화를 하였다.


![](/assets/img/_POST/Classic Ciphers/image3.png)
