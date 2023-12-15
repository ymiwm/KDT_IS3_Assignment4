# K-Digital Training Information Security 3rd Assignment 4

### Overview

**Subject: 모의 컨설팅**

**Environment**

```
Actor
OS: Mac OS X - Apple Silicon M2 Pro
Tools: Burp Suite

Target
Service: Web
Language: JS, HTML
Server: Apache Tomcat
DB: MySQL
```

1. Source Code Link<a href="#1-source-code-link"><sup>[1]</sup></a>

2. Implementation<a href="#2-implementation"><sup>[2]</sup></a>

3. Wrap Up<a href="#3-wrap-up"><sup>[3]</sup></a>

4. WIP<a href="#4-wip"><sup>[4]</sup></a>

5. Review<a href="#5-review"><sup>[5]</sup></a>

6. Ref Links<a href="#6-ref-links"><sup>[6]</sup></a>

---

#### 1. Source Code Link

[Link - KDT_IS3_Assignment4](https://github.com/ymiwm/KDT_IS3_Assignment4)

---

#### 2. Implementation

0. **Prologue**  
처음엔 블랙박스를 기준으로 웹 해킹을 수행하려 했으나, 소스 코드가 공개되지 않은 점과 웹 해킹의 방식과 들어가는 리소스를 고려.  
화이트박스 혹은 Well-known 취약점을 사용하여 Exploit을 진행해보고 추가적인 취약점에 관해 테스트 및 그에 관한 컨설팅 수행.  
각 항목마다 정보보호 3원칙에 위배된다 생각되는 부분을 적음(***C / I / A***)

3 Priciples of Information Security  
> ***C***: Confidentiality  
> &nbsp;***I***: Integrity  
> ***A***: Availability

1. **Information and Checklist(Well-Known)**
    1. Information  
        - Write in C#

    2. Checklist
        1. SQL injection(Well-known)  
        잘 알려진 취약점으로 Sign in 시 `' or 1=1--` 쿼리를 입력하면 *id*는 *true*를 반환하고 뒤에 배치되어 있는 것으로 상정되는 *pw* 쿼리에 의해 무시됨. -> Admin으로 접속됨.  
            - ***C***: 제 3자가 DB에 접근하는 것으로 기밀성 문제.
            - ***I***: Query를 이용해 정보 수정도 가능할 것으로 예상되어 무결성 문제.
            - ***A***: Query를 이용해 정보 삭제도 가능할 것으로 예상되어 가용성 문제.

        2. Sending plain data  
        Sign in 시 *Burp Suite*으로 데이터를 intercept 한 결과, Encryption, Hashing을 거치지 않은 plain data를 전송.
            - ***C***: Data를 intercept하여 기밀성 문제.

        3. XSS(Well-known)  
        Search 부에 스크립트를 삽입하여 예외 동작 가능.
            - ***C***: 
            - ***I***: 
            - ***A***: 

        4. Modifying request(MITM)  
        *Burp Suite*을 사용해 Transfer Fund의 *request*값을 변경하여 자금 전달과 관련한 중대한 취약점.
            - ***C***: 중간자가 정보를 열람함으로 기밀성 문제.
            - ***I***: Request가 수정되며 무결성 문제.
            - ***A***: Deposit값을 없애버리는 것도 가능하게 함으로 가용성 문제.

        5. Out of range  
        Transfer Fund에서 deposit보다 큰 값을 이체할 시, 예외 처리가 없어 account가 음수값으로 설정됨.  
        또한 아주 큰 값이 Deposit에 들어올 시 일정 자리수 밑으로 0으로 표기됨.
            - ***I***: 정상적이지 못한 데이터가 처리됨으로 무결성 문제.

        6. Insufficient Authentificaiton & Insufficient Authorization  
        Admin으로 접속한 후 Edit Users에 다른 인증 절차 없이 접근하여 유저 정보 조작 가능
            - ***C***: 다른 유저의 정보가 노출됨으로 기밀성 문제
            - ***I***: 다른 유저의 정보를 수정함으로 무결성 문제
            - ***A***: 다른 유저가 자신의 계정에 접근 못함으로 가용성 문제

        7. Access in the same time  
        다른 기기에서 Admin 계정에 동시 접근 가능

2. **Exploit**  
    
    0. Index
    ![Index](/img/Index/Index.png)

    1. SQL injection(Well-known)  
    id가 들어갈 자리는 ```true```가 되고 뒤의 query는 무시.
    ![SQL Injection 0](/img/SQLI/SQL%20Injection%200.png)
    접속 성공
    ![SQL Injection 1](/img/SQLI/SQL%20Injection%201.png)

    2. Sending plain data  
    위의 injection 수행 시 request 내용이 그대로 드러남.
    ![Sending Plain Data 0](/img/Sending%20Plain%20Data/Sending%20Plain%20Data%200.png)

    3. XSS(Well-known)  
    Search 부에 스크립트 입력
    ![XSS 0](/img/XSS/XSS%200.png)
    동작 확인
    ![XSS 1](/img/XSS/XSS%201.png)

    4. Modifying request(MITM)  
    Source account info(800006 Savings)
    ![Transfer Source 0](/img/Modifying%20Request/Transfer%20Source%200.png)
    Destination account info(800007 Checking)
    ![Transfer Destination 0](/img/Modifying%20Request/Transfer%20Destination%200.png)
    Modified destination account info(800005 Checking)
    ![Transfer Modified Destination](/img/Modifying%20Request/Transfer%20Modified%20Destination%200.png)
    Transfer attempt
    ![Transfer Attempt](/img/Modifying%20Request/Transfer%20Attempt.png)
    Transfer intercept
    ![Transfer Request Original](/img/Modifying%20Request/Transfer%20Request%20Original.png)
    Transfer request modifying
    ![Transfer Request Modified](/img/Modifying%20Request/Transfer%20Request%20Modified.png)
    Modified transfer succeed
    ![Modified Request Succeed](/img/Modifying%20Request/Modified%20Request%20Succeed.png)
    Result of each account  
    Source
    ![Transfer Source 1](/img/Modifying%20Request/Transfer%20Source%201.png)
    Destination
    ![Transfer Destination 1](/img/Modifying%20Request/Transfer%20Destination%201.png)
    Modified destination
    ![Transfer Modified Destination 1](/img/Modifying%20Request/Transfer%20Modified%20Destination%201.png)


    5. Out of range
    ![Invalid Data 0](/img/Out%20Of%20Range/Invalid%20Data%200.png)

    6. Insufficient Authentificaiton & Insufficient Authorization
    Access to user data without any authentification and authorization
    ![Edit User 0](/img/Insufficient%20Authentification%20and%20Authorization/Edit%20User%200.png)
    ![Edit User 1](/img/Insufficient%20Authentification%20and%20Authorization/Edit%20User%201.png)
    ![Edit User 2](/img/Insufficient%20Authentification%20and%20Authorization/Edit%20User%202.png)

    7. Access in the same time
    Can access to admin account in different ```JSESSIONID```
    ![Invaild Access 0 ](/img/Invalid%20Access/Invalid%20Access%200.png)
    ![Invaild Access 1](/img/Invalid%20Access/Invalid%20Access%201.png)
    ![Invaild Access 2](/img/Invalid%20Access/Invalid%20Access%202.png)
    ![Invaild Access 3](/img/Invalid%20Access/Invalid%20Access%203.png)


3. **Defense**
    1. SQL injection(Well-known)
        - Prepared statement와 parameter binding 및 필터링으로 방어.
    
    2. Sending plain data
        - 전송 전 Encryption, Hashing 과정을 추가하여 데이터 난독화.

    3. XSS(Well-known)
        - 문자 필터링으로 방어. (*script*문 등을 필터링 하거나, htmlentities 함수 사용.)

    4. Modifying request(MITM)  
        *Burp Suite*을 사용해보면서 개인적으로 방어 방법이 떠오르지 않아 Man-In-The-Middle 공격의 방어 기법을 일단 적용
        - Detection rule
            - 알 수 없는 원인으로 페이지나 애플리케이션 로딩이 오래 걸리는 경우.
            - URL이 HTTPS가 아닌 HTTP로 시작하는 경우.
            - 앱에서 연결이 반복하여 끊기거나 장애가 일어날 시.
            - 애플리케이션 URL에 기묘한 주소나 문자가 있는 경우.
        - Prevention
            - Access point에 WAP(Wireless Application Protocol) Encryption 실시.
            - HTTPS 사용
            - 모든 통신에 End-to-End Encryption 수행
            - 인적 자원의 monitoring으로 이상 징후 확인
            - Public key authentification, RSA 사용
            - RASP(Runtime Application Self-Protection) 사용

    5. Out of range
        - Deposit amount를 DB로부터 불러와 분기함으로써 방어.
        - 현실적이지 못한 값이 입력된 것은 사실이지만, 이를 모니터링하거나 허용되지 않은 동작으로 분리.

    6. Insufficient Authentificaiton
        - 인증, 인가 절차 추가(세션 및 서버 사이드 스크립트 사용)

    7. Access in the same time
        


---

#### 3. Wrap Up

1. Server side script JSP(Java Server Page)의 원리 학습이 필요해 보임.

2. SQL injection에 사용하기 좋은 query와 자주 접하기 힘든 문법을 학습할 수 있어 좋았다.

3. 아직 Insufficient Authentification vs Authorization과  
XSS vs CSRF의 차이점을 명확히 하지 못했다. 추가 학습 필요.

4. Feedback에 존재하는 comment라는 id를 가진 textarea가 comment.txt 파일로 저장되는 것을 확인. XSS를 시도하였으나 텍스트파일이라 그런지 별일이 안일어났음. 이에 관해 알아보기.

**Suspicious**

1. Deposit에 지수 형태로 표기된 금액에 관해 시스템을 유추할 수 있을 것으로 예상됨.
![Exponent](/img/Suspicious/Exponent.png)

2. Search New Articles에서 'article' 테이블로 추정되는 정보를 얻을 수 있음. Query를 보내는 것으로 추정되어 SQL injection 시도해보았으나 실패.
    - Try:
        - 마지막 구문이 like일 것으로 추정되어 와일드카드(%) 사용 -> 실패.
        - URL에 출력되는 '*query=*'를 보면 utf-8로 인코딩 된 것으로 추정되어 인코딩이 안된 텍스트를 직접 넣어봄 -> 실패
![SQL](/img/Suspicious/SQL.png)

---

#### 4. WIP

1. 추가적인 취약점을 체크리스트를 작성하여 찾아보기.

---

#### 5. Review

- 피드백 후 추가 예정

---

#### 6. _Ref Links_
- 웹 해킹 데모 사이트  
https://demo.testfire.net/index.jsp
- 데모 사이트에 관한 설명  
https://github.com/OWASP-Foundation/OWASP-wiki-md/blob/master/AltoroMutual.md

---

[_Go to top_ ↑](#k-digital-training-information-security-3rd-assignment-4)
