---
title: Charactrer Frequency
date: 2023-09-22 21:12:14 +09:00
categories: [Study, Crypto]
tags: [symmetric, key]     # TAG names should always be lowercase
math: true
mermaid: true
---

> A. char_freq(string): It takes a string (ex "abcdefaaa") and returns a dictionary that contains a frequency listing.<br>
B. char_freq_file(file): It takes a filename in which the file contains a strings of English characters and returns a dictionary that contains a frequency listing.<br>
C. histogram(dict_freq): It takes a frequency dictonary (that is returned by char_freq() or char_freq_file()) and prints a (simple) histogram to the screen. Note that a simple way to show the histogram is to print * character.

---
## PART A
***char_freq(string)***
- 문자열을 입력받아 각 문자의 개수를 카운트하여 딕셔너리 반환


```python
def char_freq(string):
    num = 0
    dic = {}
    while num < len(string):
        Char = string[num]
        if Char in dic:
            dic[Char]+=1
        else:
            dic[Char]=1
        num = num + 1
    print(dic)
```
string의 길이만큼 반복문을 수행하고, Char변수에 string의 num번째 값을 대입하여 만약 딕셔너리에 해당 Char변수의 key가 있는지 확인한다. 만약 있다면 해당 key의 value에 1을 더해주고 없다면 key와 value값 1을 딕셔너리에 추가해준다.

![](assets/img/_POST/Characater Frequency/image1.png)


## PART B
***char_freq_file(file)***
- file의 이름을 전달하여 해당 파일의 string을 읽어 frequency listing을 포함하는 딕셔너리를 출력
```python
def char_freq_file(file):
    FILE = open(file, 'r')
    s = FILE.read()
    print(s)
    char_freq(s)
    FILE.close()
```

file의 이름을 전달받아 open함수를 이용하여 읽기모드 ‘r’로 파일을 연다. 이후 파일에서 문자열을 읽어 변수 s에 저장한다. 변수 s에 파일의 내용이 제대로 저장되었는지 확인하기 위해 s를 출력하였다. 이후 앞서 문제 a에서 만들었던 char_freq(string) 함수를 호출하여 frequency listing을 포함하는 딕셔너리를 만들어 출력해준다.

![](assets/img/_POST/Characater Frequency/image2.png)


## PART C
***histogram(dic_freq)***
- char_freq_file 또는 char_freq함수를 통해 반환되는 결과를 히스토그램으로 출력

```python
import matplotlib.pyplot as plt
import mataplotlib

def histogram(dic_freq):
    x=[]
    y=[]
    x = dic_freq.keys()
    y = dic_freq.values()
    print(x)
    print(y)

    xs = [i for i, _ in enumerate(x)]
    plt.bar(xs, y)
    plt.ylabel("Number")
    plt.title("Frequecnty Listing")
    plt.xticks(xs, x)
    plt.show()
```

Histogram을 출력하기 위해 matplotlib.pylot 모듈을 사용한다.
histogram함수의 매개변수로 딕셔너리를 입력받는다. 따라서 딕셔너리의 key를 x값으로 value를 y값으로 만들어준다.<br>
➔ X = dic_freq.keys()<br>
➔ Y = dic_freq,values()<br>
이후 각 x 리스트와 y 리스트를 출력하여 key값과 value값이 적절하게 들어갔는지 확인한다. X 리스트의 인덱스와 값을 사용하여 xs라는 새로운 리스트를 생성한다. 그런 다음, plt.bar 함수를 사용하여 xs리스트를 x축으로 하고 y리스트를 y축으로 하는 그래프를 생성한다. 마지막으로 plt.show() 함수를 통해 그래프를 출력한다. 
![](assets/img/_POST/Characater Frequency/image3.png)


![](assets/img/_POST/Characater Frequency/image4.png)

### 전체 코드

```python
import matplotlib.pyplot as plt
import matplotlib

def char_freq(string):
    num = 0
    dic = {}
    while num < len(string):
        Char = string[num]
        if Char in dic:
            dic[Char]+=1
        else:
            dic[Char]=1
        num = num + 1
    print(dic)

def char_freq_file(file):
    FILE = open(file, 'r')
    s = FILE.read()
    print(s)
    char_freq(s)
    FILE.close()
    
def histogram(dic_freq):
    x=[]
    y=[]
    x = dic_freq.keys()
    y = dic_freq.values()
    print(x)
    print(y)

    xs = [i for i, _ in enumerate(x)]
    plt.bar(xs, y)
    plt.ylabel("Number")
    plt.title("Frequecnty Listing")
    plt.xticks(xs, x)
    plt.show()

```