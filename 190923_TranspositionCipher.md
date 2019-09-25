# 전치 암호 (Transposition Cipher)
### 정의
특정 글자를 다른 글자로 치환하는 치환 암호(Substitution Cipher)와 다르게 전치 암호(Transposition Cipher)는 특정 글자를 다른 글자로 치환하지 않고, 글자들의 순서를 변경하여 암호화한다. 예를 들어, 평문(Plaintext)의 첫 번째 글자와 열 번째 글자를 바꾸고, 두 번째 글자와 네 번째 글자를 바꿔서 암호화 할 수 있다. 이렇게 단순히 순서만 변경한 암호문(Ciphertext)은 한 눈에 알아보기 어려워진다. 카이사르 암호에 비해 전치 암호는 가능한 키의 수가 메시지의 길이에 좌우되므로 무차별 대입 공격이 보다 어렵다.  

### 과정

1. 메시지와 키의 문자 수를 체크한다.
2. 키와 같은 개수의 상자를 한 줄에 그린다. (ex. KEY=8 -> 상자 8개)
3. 왼쪽에서 오른쪽으로 상자를 채우고 상자 한 개에 글자 한 개를 삽입한다.
4. 문자는 상자 끝에 이르면 상자의 줄을 추가한다.
5. 마지막 문자까지 오면 마지막 행에 있는 사용하지 않은 상자는 Marking 한다.
6. 왼쪽 상단부터 시작해 각 열을 내려가면서 문자를 입력한다.
7. 열의 가장 아래에 이르면 오른쪽의 다음 열로 이동한다.

### 예시
- 암호화 할 메시지 : "Common sense is not so common." (공백, 마침표 포함 30자)
- KEY = 8 (KEY 범위는 2부터 메시지 크기의 절반인 15까지 가능)
- 메시지가 길수록 키 사용 범위 증가
- 키 번호와 일치하도록 8개 상자를 추가해 글자 수만큼 행 추가

|1차|2차|3차|4차|5차|6차|7차|8차
|--|--|--|--|--|--|--|--|
|C|o|m|m|o|n|  |s|
|e|n|s|e|  |i|s|  |
|n|o|t|  |s|o|  |c
|o|m|m|o|n|.|||

- 암호문은 상단 왼쪽 상단부터 글자를 읽어서 구성한다.
-  암호문은 "Cenoonommstmme oo snnio. s s c"로 구성된다.

###  암호화 코드

``` python
# transpositionEncrypt.py

import pyperclip

def main() :
    myMessage = input("암호화 할 평문 입력 >> ")
    myKey = int(input("KEY 입력 >> "))

    ciphertext = encryptMessage(myKey, myMessage)

    # 암호화된 문자열의 끝에 공백 여부를 판단하기 위해
    # 파이프 문자 "|"와 함께 출력
    print("*** 암호화 완료 ***")
    print(ciphertext + '|')

    # ciphertext에 들어있는 암호화된 문자열을 클립보드에 복사
    pyperclip.copy(ciphertext)

def encryptMessage(key, message) :
    # ciphertext의 각 문자열은 격자상 하나의 열에서 옴
    ciphertext = [''] * key

    # ciphertext의 각 열(column)을 순회
    for column in range(key) :
        currentIndex = column

        # currentIndex가 message 길이를 넘을 때까지 순회 반복
        while currentIndex < len(message) :
            ciphertext[column] += message[currentIndex]
            currentIndex += key

    return ''.join(ciphertext)

# transpositionEncrypt.py를 실행하면(모듈로 import 한 게 아니라면) main() 실행
if __name__ == '__main__' :
    main()
```

### 실행 내용
```
암호화 할 평문 입력 >> Common sense is not so common.
KEY 입력 >> 8
*** 암호화 완료 ***
Cenoonommstmme oo snnio. s s c|
```

### 복호화 코드
``` python
# transpositionDecrypt.py

import math,pyperclip

def main() :
    message = input("복호화 할 평문 입력 >> ")
    for key in range(2, len(message)//2 + 1) :
        plaintext = decryptMessage(key, message)
        print("KEY : %d >> %s%s" % (key, plaintext,'|'))

def decryptMessage(key, message) :

    # 전치 그리드에서 사용할 columns의 길이
    numOfColumns = int(math.ceil(len(message) / float(key)))
    
    # 전치 그리드에서 사용할 rows의 길이
    numOfRows = key

    # 그리드의 마지막 "column"에 칠할 박스의 숫자
    numOfShadeBoxes = (numOfColumns * numOfRows) - len(message)

    # 그리드의 열 한개에 담긴 평문의 각 문자열
    plaintext = [''] * numOfColumns

    #암호화된 message를 따라가며 그리드의 다음 글자를 가리키는 column과 row 변수
    column = 0
    row = 0

    for symbol in message :
        plaintext[column] += symbol
        column += 1 # Point to next column

        # 더 이상 열이 없거나 검은 칠을 한 박스에 이르면
        # 첫 번째 열로 돌아가 다음 행 처리
        if (column == numOfColumns) or (column == numOfColumns - 1 and row >= numOfRows - numOfShadeBoxes) :
            column = 0
            row += 1

    return ''.join(plaintext)

if __name__ == '__main__' :
    main()
```

### 실행 내용
```
복호화 할 평문 입력 >> Cenoonommstmme oo snnio. s s c
KEY : 2 >> Coeon osonnnoimom.s tsm mse  c|
KEY : 3 >> Ctneminmooe.o  nosoo m sms snc|
KEY : 4 >> Cmo.es  ntssomn omnsnei o ocmo|
KEY : 5 >> Coms emensnm n osoisotoo nm .c|
KEY : 6 >> Cntonseomoi nmm osomes. os n c|
KEY : 7 >> Cnt so eomon.snmmon  ome iscos|
KEY : 8 >> Common sense is not so common.|
KEY : 9 >> Commosi senseonos not  n. comm|
KEY : 10 >> Coosmosi seomteonos nnmm  n. c|
KEY : 11 >> Coosmosi   eomteonosscnnmm  n.|
KEY : 12 >> Coosmosno   eomteoni.sscnnmm  |
KEY : 13 >> Coosm osno   eomteo ni.sscnnmm|
KEY : 14 >> Coomtm osno   eomsmeo ni.sscnn|
KEY : 15 >> Cnoomtm osno   eonmsmeo ni.ssc|
```