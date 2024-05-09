---
title: Simple Des
date: 2023-09-15 23:42:21 +09:00
categories: [Study, Crypto]
tags: [symmetric, key]     # TAG names should always be lowercase
math: true
mermaid: true
---
> Implement the 3-rounds simple DES cipher using Python. You should submit sdes.pdf and 
sdes.py file in which sdes.pdf contains the souce code and the running results of implemented 
ciphers, and the sdes.py is the souce code of your implementation.
- sdes_genkey(): It outputs a key of random values.
- sdes_encrypt(key, pblock): It takes the key and a plaintext block, and then it outputs a ciphertext 
block.
- sdes_decrypt(key, cblock): It takes the key and a ciphertext block, and then it outputs a plaintext 
block.
When implementing the simple DES cipher, you should use a list of binary value for input and 
output for easy implementation.

## 1. sdes_genkey()
- 9bit의 랜덤값 출력
- Simple Des를 수행하기 위해 9bit의 random한 key가 필요함


```python
def sdes_genkey():
	key = []
    for i in range(9):
    	tmp = random.randint(0,1)
        key.append(tmp)
    return key
```
sdes_genkey()함수에서는 1과 0중에서 랜덤하게 9번 추출하여 list를 구성하였다. 
random.randint()함수는 주어진 범위안에 있는 정수를 랜덤하게 추출하는 것이기에 해당 함수를 이용하였다.

![](assets/img/_POST/Simple Des/image1.png)


## 2. sdes_encrypt(key, pblock)
- 키와 평문 블록을 입력받아 암호 블록 출력
- key(9bit), pblock(12bit)

Simple des의 암호화는 총 3라운드로 진행되고 하나의 라운드가 아래 그림과 같은 방식으로 진행<br>
![](assets/img/_POST/Simple Des/image2.png)<br>
암호화 하고자 하는 pblock 12bit를 6bit씩 Left와 Right로 나누어, Right는 다음 단계의 Left로 그대로 전달된다. Left는 f(R, k)와 xor연산을 하여 다음 단계의 Right가 된다.<br>
이때, key는 8bit이며 입력받은 9it의 키에서 각 라운드마다 라운드키를 생성하여 사용한다.<br>
![](assets/img/_POST/Simple Des/image3.png)<br>
f(R,k)에서 사용되는 function은 위의 그림과 같다.<br>
Function을 수행하기 전 6bit의 Right를 expand함수를 이용하여 8bit로 확장시킨다. 확장시킨 값을 라운드 key와 XOR연산을 하고 4bit씩 나누어 각 S-box를 통과시켜 3bit, 3bit의 총 6bit의 결과값을 출력한다.<br>
앞서, 설명했던 simple des의 encryption방법을 그대로 구현하였다.

```python
def round_key(key, num):
    if(num+8 > 9):
        add = num-1
        R_key = key[num:9]
        R_add = key[0:add]

        R_key = R_key + R_add
    else:
        R_key = key[num:num+8]
    
    return R_key

def expander(right):
    expand_R = []
    expand_R.append(right[0])
    expand_R.append(right[1])
    expand_R.append(right[3])
    expand_R.append(right[2])
    expand_R.append(right[3])
    expand_R.append(right[2])
    expand_R.append(right[4])
    expand_R.append(right[5])
    return expand_R

def S_box1(L_Right):
    Sbox1 = [['101', '010', '001', '110', '011', '100', '111', '000'],
            ['001', '100', '110', '010', '000', '111', '101', '011']]

    if(L_Right[0] == 0):
        k = 0
    else:
        k = 1

    binary = ""
    for i in range (1,4):
        binary += str(L_Right[i])

    return list(Sbox1[k][int(binary, 2)])
    
def S_box2(R_Right):

    Sbox2 = [['100', '000', '110', '101', '111', '001', '011', '010'],
            ['101', '011', '000', '111', '110', '010', '001', '100']]

    if(R_Right[0] == 0):
        k = 0 
    else:
        k = 1

    binary = ""
    for i in range (1,4):
        binary += str(R_Right[i])

    return list(Sbox2[k][int(binary, 2)])  
    
def sdes_encrypt(key, pblock):

    for i in range(3):

        Left = pblock[0:6] # Left ->  0~5 list item
        Right = pblock[6:12] # Right -> 6~11 list item

        Origin_Right = Right
        # function <- R
        
        Rkey = round_key(key, i) # make Round key

        Right = expander(Right) # Right Expand: 6bit -> 8bit

        
        for j in range(8):
            Right[j] = Rkey[j] ^ Right[j] # round_key XOR expander_Right

        # Right를 4bit씩 나누기
        L_Right = Right[0:4] # L_Right -> 0~3 list item
        R_Right = Right[4:8] # R_Right -> 4~7 list item

        L_Right = S_box1(L_Right) # S_box1
        R_Right = S_box2(R_Right) # S_box2

        Right = L_Right + R_Right

        # S_box를 통해 return 되는 list는 문자열 list임
        # -> 정수형 list로 변경
        Right = list(map(int, Right))

        for j in range(6):
            Right[j] = Left[j] ^ Right[j]
            # Left 6bit와 Right 6bit XOR 연산
        pblock = Origin_Right + Right


    return pblock
```
총 3라운드를 진행해야 하기 때문에, for문을 사용하였다.
먼저, 12bit의 pblock을 각 6bit씩 Left와 Right로 나누어 Left변수와 Right변수에 저장하였다. Right는 그대로 다음단계의 Left가 되기 때문에 Orgin_Right 변수에 따로 원래의 Right를 저장해주었다.<br>
round_key()함수를 이용해서 라운드 키를 만들어 Rkey변수에 저장했다. 라운드 키는 주어진 9bit의 key를 n번째부터 시작하여 8bit를 사용한 키이다. 이때 n은 라운드를 의미한다.<br>
6bit의 Right를 8bit로 확장시키기 위해 expander()함수를 이용하였다. expander함수는 3번째 bit를 4번째와 6번째로 4번째 bit를 4번째와 5번째로 하고 나머지는 그 앞뒤로 채워 총 8bit를 만들어주는 함수이다.<br>
다음으로 확장한 Right와 라운드 key인 Rkey와 xor연산을 시킨다. xor연산을 시킨 Right를 각 4bit씩 나누어 L_Right와 R_Right 변수에 각각 대입하고 S_box1과 S_box2 함수를 수행한다.<br>
S_box 함수는 4bit중 첫번째 bit가 row를 결정하고 나머지 3bit가 column를 결정하여 s_box의 3bit를 반환한다. 이렇게 반환된 3bit씩의 List를 합하여 Right에 저장시키고 Left와 xor연산을 하여 Right변수에 저장한다.<br> 
마지막으로 이전 라운드의Right와 Left xor f(R, k)의 list를 연결시켜 암호화를 완료한다.<br>

![](assets/img/_POST/Simple Des/image4.png)<br>


## 3. sdes_decrypt(key, cblock)
- 키와 암호문 블록을 입력받아 평문 블록 출력
- key(9bit), cblock(12bit)

Simple des의 decryption은 encryption과 거의 동일하게 진행된다. Left와 Right의 자리를 변경하여 진행된다는 차이점이 존재한다. 즉, 𝑅𝑖 = (𝐿𝑖−1 ⨁𝑓(𝑅𝑖−1,𝐾𝑖)) ⨁ 𝑓(𝐿𝑖,𝐾𝑖) 를 계산하면 xor연산으로 인해 𝑅𝑖 = 𝐿𝑖−1 결과가 나오게 된다.
```python
def sdes_decrypt(key, cblock):
    
    cblock_inverse = cblock[6:]
    cblock_inverse.extend(cblock[:6])
    cblock = cblock_inverse

    for i in range(3):

        Left = cblock[0:6] # Left ->  0~5 list item
        Right = cblock[6:12] # Right -> 6~11 list item

        Origin_Right = Right
        # function <- R
        
        Rkey = round_key(key, 2-i) # make Round key

        Right = expander(Right) # Right Expand: 6bit -> 8bit
        
        for j in range(8):
            Right[j] = Rkey[j] ^ Right[j] # round_key XOR expander_Right

        # Right를 4bit씩 나누기
        L_Right = Right[0:4] # L_Right -> 0~3 list item
        R_Right = Right[4:8] # R_Right -> 4~7 list item

        L_Right = S_box1(L_Right) # S_box1
        R_Right = S_box2(R_Right) # S_box2

        Right = L_Right + R_Right

        # S_box를 통해 return 되는 list는 문자열 list임
        # -> 정수형 list로 변경
        Right = list(map(int, Right))

        for j in range(6):
            Right[j] = Left[j] ^ Right[j]
            # Left 6bit와 Right 6bit XOR 연산
        cblock = Origin_Right + Right

    pblock_inverse = cblock[6:]
    pblock_inverse.extend(cblock[:6])
    pblock = pblock_inverse
    #결과가 Left와 Right가 반대로 되어 나오기 때문에 수행
    return pblock
```
Decryption도 Encryption과 동일하게 총 3라운드를 반복해야 하므로 for문을 수행하며 for문 내부의 코드는 이전의 encryption코드와 동일하다.<br>
그러나 앞서 설명했듯, Left와 Right의 자리가 변경되어 수행되어야 하기 때문에 list slice를 이용하여 6bit씩 변경해주었다.<br>

![](assets/img/_POST/Simple Des/image5.png)<br>


## 전체 코드
```python
# Simple DES
import random

def sdes_genkey():

    key = []
    for i in range(9):
        tmp = random.randint(0,1)
        key.append(tmp)
    return key

def round_key(key, num):
    if(num+8 > 9):
        add = num-1
        R_key = key[num:9]
        R_add = key[0:add]

        R_key = R_key + R_add
    else:
        R_key = key[num:num+8]
    
    return R_key

def expander(right):
    expand_R = []
    expand_R.append(right[0])
    expand_R.append(right[1])
    expand_R.append(right[3])
    expand_R.append(right[2])
    expand_R.append(right[3])
    expand_R.append(right[2])
    expand_R.append(right[4])
    expand_R.append(right[5])
    return expand_R

def S_box1(L_Right):
    Sbox1 = [['101', '010', '001', '110', '011', '100', '111', '000'],
            ['001', '100', '110', '010', '000', '111', '101', '011']]

    if(L_Right[0] == 0):
        k = 0
    else:
        k = 1

    binary = ""
    for i in range (1,4):
        binary += str(L_Right[i])

    return list(Sbox1[k][int(binary, 2)])
    
def S_box2(R_Right):

    Sbox2 = [['100', '000', '110', '101', '111', '001', '011', '010'],
            ['101', '011', '000', '111', '110', '010', '001', '100']]

    if(R_Right[0] == 0):
        k = 0 
    else:
        k = 1

    binary = ""
    for i in range (1,4):
        binary += str(R_Right[i])

    return list(Sbox2[k][int(binary, 2)])  
    
def sdes_encrypt(key, pblock):

    for i in range(3):

        Left = pblock[0:6] # Left ->  0~5 list item
        Right = pblock[6:12] # Right -> 6~11 list item

        Origin_Right = Right
        # function <- R
        
        Rkey = round_key(key, i) # make Round key

        Right = expander(Right) # Right Expand: 6bit -> 8bit

        
        for j in range(8):
            Right[j] = Rkey[j] ^ Right[j] # round_key XOR expander_Right

        # Right를 4bit씩 나누기
        L_Right = Right[0:4] # L_Right -> 0~3 list item
        R_Right = Right[4:8] # R_Right -> 4~7 list item

        L_Right = S_box1(L_Right) # S_box1
        R_Right = S_box2(R_Right) # S_box2

        Right = L_Right + R_Right

        # S_box를 통해 return 되는 list는 문자열 list임
        # -> 정수형 list로 변경
        Right = list(map(int, Right))

        for j in range(6):
            Right[j] = Left[j] ^ Right[j]
            # Left 6bit와 Right 6bit XOR 연산
        pblock = Origin_Right + Right


    return pblock
        
        
def sdes_decrypt(key, cblock):
    
    cblock_inverse = cblock[6:]
    cblock_inverse.extend(cblock[:6])
    cblock = cblock_inverse

    for i in range(3):

        Left = cblock[0:6] # Left ->  0~5 list item
        Right = cblock[6:12] # Right -> 6~11 list item

        Origin_Right = Right
        # function <- R
        
        Rkey = round_key(key, 2-i) # make Round key

        Right = expander(Right) # Right Expand: 6bit -> 8bit
        
        for j in range(8):
            Right[j] = Rkey[j] ^ Right[j] # round_key XOR expander_Right

        # Right를 4bit씩 나누기
        L_Right = Right[0:4] # L_Right -> 0~3 list item
        R_Right = Right[4:8] # R_Right -> 4~7 list item

        L_Right = S_box1(L_Right) # S_box1
        R_Right = S_box2(R_Right) # S_box2

        Right = L_Right + R_Right

        # S_box를 통해 return 되는 list는 문자열 list임
        # -> 정수형 list로 변경
        Right = list(map(int, Right))

        for j in range(6):
            Right[j] = Left[j] ^ Right[j]
            # Left 6bit와 Right 6bit XOR 연산
        cblock = Origin_Right + Right

    pblock_inverse = cblock[6:]
    pblock_inverse.extend(cblock[:6])
    pblock = pblock_inverse
    #결과가 Left와 Right가 반대로 되어 나오기 때문에 수행
    return pblock
```