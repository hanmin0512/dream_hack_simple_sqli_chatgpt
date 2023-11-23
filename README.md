# dream_hack_simple_sqli_chatgpt
dreamhack simple_sqli_chatgpt 문제풀이

# 코드 분석
1. sqlite3를 사용한다.
2. DB연결, 테이블생성, 데이터 삽입, 쿼리 실행 함수, 로그인 함수가 있다.
3. 쿼리 실행 함수는 쿼리를 실행하여 얻은 값을 반환 한다.
4. 로그인 함수에는 flag 값을 반환하는 조건이 명시되어있다.

# flag 반환 조건
```python
if userid == 'admin' and userlevel == 0:
    return f'hello {userid} flag is {FLAG}'
```
> userid가 admin이고, userlevel이 0이어야 한다.


# 테이블 값 확인
```python
db.execute(f'insert into users(userid, userpassword, userlevel) values ("guest", "guest", 0), ("admin", "{binascii.hexlify(os.urandom(16)).decode("utf8")}", 0);')
```
이 부분을 보면 테이블은 아래와 같이 생성 된다.
| userid | userpassword | userlevel |
|-------|-------|-------|
| guest  | guest  | 0  |
| admin  | b'd1be788954980499ddbd145883da92b7'  | 0  |

- flag반환 조건에 userpassword는 없으므로 무시해준다.
- guest, admin둘다 userlevel이 0 이다.
- 그러나 폼에 0을 입력하면 테이블 상단에 있는 guest가 반환 된다.
- 그렇다면 조금더 명확한조건의 sql문으로 변조해야한다.

# SQL injection
```python
res = query_db(f"select * from users where userlevel='{userlevel}'")
```
- 위 쿼리에서 userlevel에 0을 뒤에 sql문을 추가해서 userid = admin을 추가해준다.
- 이미 코드에 싱글 쿼텐션으로 감싸져 있기 때문에 조건을 넣어주고 추가한 조건뒤에 싱글 쿼텐션을 안적어주면 된다.
- 정답 : ``` 0' and userid = 'admin ``` 이 입력 값은 {userlevel} 안에 들어간다.
- 완성된 sql query : ``` select * from users where userlevel = '0' and userid = 'admin' ```

# 화면
- 폼에 0을 입력했을 때
<img width="451" alt="스크린샷 2023-11-23 오전 11 27 39" src="https://github.com/hanmin0512/dream_hack_simple_sqli_chatgpt/assets/37041208/03211746-2e71-455a-983b-e7127795ee08">

- 폼에 sql injection 공격을 했을 때
<img width="509" alt="스크린샷 2023-11-23 오전 11 28 56" src="https://github.com/hanmin0512/dream_hack_simple_sqli_chatgpt/assets/37041208/46b4c099-2960-4502-82ab-c7b158e5c5ec">
<br>
<img width="392" alt="스크린샷 2023-11-23 오전 11 29 03" src="https://github.com/hanmin0512/dream_hack_simple_sqli_chatgpt/assets/37041208/f988a395-2ae1-4a07-adf2-bca7ed0af0a8">



  
