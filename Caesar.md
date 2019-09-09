## 카이사르 암호화 코드
``` python
### encrypt.py
plaintext = input("암호화 할 평문 입력 >> ")
key = int(input("key 입력 >> "))

encrypt=''

for i in plaintext :
    # 알파벳이 아닌 경우 (특수문자, 공백 등)
    if i.isalpha() == False :
        encrypt += i
    # 대문자인 경우
    elif i.isupper() == True :
        c = chr(ord(i) + key)
        if ord(c) > 90 :
            c = chr(64 + key - (90 - ord(i)))
        encrypt += c
    # 소문자
    else :
        c = chr(ord(i) + key)
        if ord(c) > 122 :
            c = chr(96 + key - (122 - ord(i)))
        encrypt += c

print("** 암호화 완료 **")
print(encrypt)
```

소문자는 소문자로, 대문자는 대문자로 변환했다.  
특수기호나 공백은 그대로 출력되도록 했다.


## 카이사르 복호화 코드
``` python
### decrypt.py
encrypt = input("암호문 입력 >> ")
key = int(input("Key 입력 >> "))

plaintext = ''

for i in encrypt :
    # 알파벳이 아닌 경우 (특수문자, 공백 등)
    if i.isalpha() == False :
        plaintext += i
    # 대문자
    elif i.isupper() == True:
        c = chr(ord(i) - key)
        if ord(c) < 65 :
            c = chr(90 - key + (ord(i) - 64))
        plaintext += c
    # 소문자
    else :
        c = chr(ord(i) - key)
        if ord(c) < 97 :
            c = chr(122 - key + (ord(i) - 96))
        plaintext += c
            
print("**  복호화 완료 **")
print(plaintext)
```