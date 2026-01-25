Index: [[유데미 리눅스]]
# 87. Linux File Editor

- vi 
- ed
- ex
- emacs
- pico
- vim - vi의 업그레이드 버전

#### 자주 사용되는 키

- `i` : 삽입 모드
- `a`: 한 칸 띄고 삽입 모드
- `esc` : 모드 나가기
- `r` : 대체 / r 누르고 바꿀 글자 정하면 한 글자 바꿔줌
- `x` : 한 글자 삭제
-  `d` : 라인 삭제
- `:q!` : 저장 안하고 나가기
- `:wq!`, `zz` : 저장하고 나가기
- `u`: 되돌리기
- `o`: 밑에 빈 줄 추가 및 삽입 모드 들어감
 - `/`: 작업 모드에서 키워드 입력 시 검색 가능 


# 88. vi 와 vim 차이

vim이 더 쉽긴 함
근데 vi는 리눅스 출시때부터 있던 거라 옛날 버전에서는 안 되는 경우 있음
vim은 vi의 모든 기능에 다른 기능 추가된 거라 vim 배우면 vi는 당연히 사용 가능함


## vi
- installed more places
- shorter name
- simpler
- **small**
- **ubiquitous**
- **intuitive command language** 
- **learning curve**
- **powerful once learned**

(볼드는 vim도 해당되는 것)

## vim
- completion
- spell check
- comparison
- merging
- unicode
- (vimdiff)
- regular expressions
- scripting languages
- plugins
- GUI
- folding
- syntax highlighting

[openvim 튜토리얼- 튜토리얼 시켜줌](https://openvim.com/)
[vimgenius - 퀴즈형식](http://vimgenius.com/)
[vim-adventures 게임형식](https://vim-adventures.com/)



# 89. "sed" 커맨드

`sed`
- 파일의 string을 다른 걸로 교체해줌
- 라인 찾거나 삭제 가능 (많은 줄 할 때 자동화해주는 느낌인듯)
- empty line 삭제 
- To replace tabs with spaces
- Show defined lines from a file
- substitute within vi editor

## 1. 내용 바꿔서 출력하기
`sed 's/Kenny/Lenny/g' <파일이름>` 
: 해당 파일의 모든 Kenny를 Lenny로 바꿈. g는 global로, 모든 Kenny를 지정
: 하지만 이렇게 하면 변경된 내용을 출력할 뿐, 파일 내용은 그대로

## 2. 파일 내용까지 바꾸기
**옵션 -i 추가**
`sed -i 's/Kenny/Lenny/g <파일이름>` 

## 3. 단어 삭제하기
**두번째 칸 비우기**
`sed 's/Costanza//g' <파일이름>`


## 4. 단어 포함된 라인 삭제하기
**d = delete 추가**
`sed '/Seinfeld/d' <파일이름>` 


## 5. 빈 줄 삭제하기
**정규표현식 ^$ 사용**
**d = delete**
`sed '/^$/d' <파일이름>`
- `^` -> 문자열의 시작
- `$` -> 문자열의 끝 


## 6. 특정 줄 삭제하기
`sed '1d' <파일이름>` : 첫번째 줄 삭제
`sed '1d, 2d <파일이름>`  : 1, 2번째 줄 삭제


## 7. 탭 삭제하기
`sed '/\t/ /g' <파일이름>`
- `\t`: 탭
=> 탭을 공백으로 만들기
반대로 넣기도 가능. 


## 8. 특정 줄만 출력
`sed -n 12,18p <파일이름>` : 12~18 줄 출력
`sed 12,18d <파일이름>`: 12~18줄만 빼고 출력


## 9. 각 줄 사이에 빈 줄 넣기
`sed G <파일이름>`


## 10. 특정 줄만 제외하고 단어 바꾸기
`sed '8!s/seinfeld/S/g' <파일이름>`: 8번째 줄의 seinfeld만 빼고 S로 바뀜 



# 90. 유저 계정 관리

커맨드
`useradd`
`groupadd`
`userdel`
`groupdel`
`usermod`

파일
`/etc/passwd`
`/etc/group`
`/etc/shadow`

예시) 
`useradd -g superheros -s /bin/bash -c "user description" -m -d /home/spiderman spiderman`

`-s`: give a shell envrionment
`-c`: define the user description 
`-m`, `-d`: define the user home directory, the user itself and the name of the user


## 실습

root 계정으로 바꾸기 -> `su -` (비번 `root`)
1. 사용자 생성 `useradd <유저네임>` 
2. 사용자 생성 확인 `id <유저네임>` : uid, gid 출력 (유저 생성 시 그룹도 같이 생성됨)

![[Pasted image 20250825230642.png]]

3. 사용자 생성 확인 : home 디렉토리 가서 `ls -l`
![[Pasted image 20250825230934.png]]


4. 그룹 생성: `groupadd <그룹네임>`
5. 그룹 생성 확인: `cat /etc/group` 마지막에 추가되었는지 확인

![[Pasted image 20250825231232.png]]

6. 유저 삭제 `userdel <유저네임>`
	- `-r` 옵션은 사용자의 홈 디렉토리까지 삭제 
	
![[Pasted image 20250825231430.png]]

6. 그룹 삭제 `groupdel <그룹이름>`
7. 그룹 삭제 확인 `cat /etc/group`
![[Pasted image 20250825231734.png]]

*출력같은 거 할 때 다양한 명령어 써보는 것도 좋을 듯*


8. 유저 수정 `usermod`
	- `-a`, --append : 유저를 그룹에 추가. `-G` 옵션과 같이 써야 함.
	- `-b`, --badname: 기준에 맞지 않는 이름도 허락
	- `-c`, --comment : 유저의 password 파일의 comment field에 새 값 추가
	- `-d`, --home: 유저의 새 로그인 디렉토리 
	- `-e`: --expiredate: 유저 계정의 사용 만료날짜 (YYYY-MM-DD)
	- `-f`: --inactive : 패스워드가 만료된 후 계정이 사용불가될 때까지의 기간 (0이면 패스워드 만료 후 바로 계정이 사용불가됨. -1이면 이 기능을 비활성화)

8-1. 유저를 다른 그룹에 추가
`useradd spiderman`
`id spiderman`
`usermod -G superheros spiderman`
-> spiderman이라는 유저 생성 후 superheros라는 그룹에 추가
결과: 기존 그룹과 새 그룹 둘 모두 포함되게 됨
![[Pasted image 20250908000848.png]]

`ls -ltr`로 보면 spiderman의 그룹이 여전히 spiderman임
![[Pasted image 20250908001030.png]]

자신만의 그룹을 가지면서 superheros 그룹에도 포함된 것
![[Pasted image 20250908001134.png]]


9. 유저 그룹 바꾸기
`chgrp -R [그룹명] [파일명]` / `chgrp -R superheros spiderman`

`-R`: 지정한 디렉토리의 하위에 있는 모든 파일도 함께 지정한 그룹으로 변경



10. `etc` 폴더 파일 살펴보기

`cat /etc/passwd`: 사용자 정보 볼 수 있음

- 첫번째 : 사용자 이름
- 두번째: 패스워드(x는 shadow 파일에 암호화되어 저장되어 있다는 뜻)
- 세번째: User ID
- 네번째: Group ID
- 다섯번째: description
- 여섯번째: home 디렉토리 경로
- 일곱번째: 유저가 사용하는 shell 

![[Pasted image 20250908001711.png]]


`cat /etc/group`: 그룹 정보를 담은 파일

- 첫번째: 그룹 이름
- 두번째: 그룹 비밀번호
- 세번째: GID
- 네번째: 해당 그룹에 속해있는 다른 그룹 

![[Pasted image 20250908001933.png]]


### /etc/shadow 파일

`cat /etc/shadow`: 실제 패스워드 정보 담긴 파일. 보안에 매우 중요한 파일 !

![[Pasted image 20250908002132.png]]

 **필드 설명**
`root:$6$IjQpzV6K7afv5/iZ$VWQdOfzavvgCqzQ92sD36aRHhJbjlfqchebm3lPkk0fpIdFuna0bZA8rDTTQJYqlcT6GY0pqr9q0Jm0x74ixq/::0:99999:7:::`
`gyeong:$6$/44ybHJc4O.xjo5v$ZntPQpmsCDXROd6ddHgZd7JbAjXltDpy71WB.cO3e27.rvOKrjd5VnomxUMBZzllY70f25Rh4RHNHg5.U25CS0:20038:0:99999:7:::
`ironman:$6$rounds=100000$QtgS3Dpt24sFhWBn$fYYph64T1CaKNc4GG3/Rl/inF/YEmIiJuJ5LEn6C/q2.47sPowgEPTBQ/nUVIdmc8SVkylBn8mdWdAbd/iO5J0:20299:0:99999:7:::`

- 첫 번째: 사용자 계정명
- 두 번째: 암호화된 패스워드. `$`로 필드가 구분되어 있음. 
> **\$ algorithm_id \$ salt $ encrypted_password **

**algorithm_id**: 암호학적 해시의 id
	- 1: MD5(가장 취약한 일방향 해쉬로 요즘엔 쓰지 않음)
	- 2: BlowFish
	- 5: SHA-256
	- 6: SHA-512
**salt**: 각 해시에 첨가할 랜덤값
**encrypted_password**: 알고리즘과 salt로 패스워드를 암호화한 것.
	- 이 필드가 `*` 이면 잠긴 상태고 로그인 불가, 별도 인증방식으로 로그인 가능
	- 이 필드가 `!` 이면 잠긴 상태이고 로그인 불가 or 사용자 생성 후 패스워드 설정 안 한 상태
	- 이 필드가 비어 있으면 패스워드가 설정되지 않았지만 로그인 가능

- 세 번째: 마지막으로 패스워드를 변경한 날. 1970년 1월 1일 기준을 표시. gyeong의 경우 20038일 지남
- 네 번째: 패스워드 최소 사용기간. 변경 이후 최소 이 정도가 지난 이후 또 변경 가능
- 다섯 번째: 패스워드 최대 사용기간. 마지막 변경 이후 만료일수. 90일 권장
- 여섯번째: 패스워드 만료 전에 경고할 경고 일수 
- 일곱번째: 만료된 이후 계정이 잠기기 전까지 비활성 일수(date). 해당 기간 동안 패스워드 변경해야 잠기지 않음
- 마지막: 계정 만료일. 1970년 1월 1일 기준으로 일수로 표시




11. 매개변수 지정해서 유저 생성

`useradd -g superheros -s /bin/bash -c "Ironman Character" -m -d /home/ironman ironman`

- `-g`: 그룹 지정(이미 생성되어 있는 그룹)
- `-s`: 사용자가 사용할 shell 
- `-c`: 사용자 description
- `-m`: 홈 디렉토리 생성 (레드햇 계열은 자동으로 되지만 다른 배포판은 안됨)
- `-d`: 홈 디렉토리 지정. 최종 디렉토리만 생성하므로 중간 경로 있으면 미리 생성해야 함 

![[Pasted image 20250908002824.png]]

12. 사용자 비밀번호 생성
`passwd ironman` : 사전 상에 있거나 짧거나 하면 BAD 패스워드라고 뜨는데 되긴 됨


----

#2025-12-12
# 97. Enable Password Aging

**중요한 파일 : /etc/login.defs**

다시 하니까 기억 안난다.
==리눅스 접속 명령어 : gyeong@192.168.219.110
패스워드: password==
root 비밀번호: root

## chage 명령어 (change age, 유저별)

- 현재 패스워드 설정 보기
`more /etc/login.defs` 

-> 만약 전체 유저 패스워드 설정을 변경하고 싶다면 이 파일을 변경

![[Pasted image 20251212113308.png]]

원하는 부분만 보고 싶다면 -> vi 로 열어서 '/찾고 싶은 키워드' 치면 됨


![[Pasted image 20251212114652.png]]

-  유저별 password 설정 변경하기
`chage [-d lastday] [-m mindays] [-M maxdays] [-I inactive] [-E expiredate] [-w warndays] user`
![[Pasted image 20251212120051.png]]

아래와 같이 설정이 변한 걸 볼 수 있음. 

![[Pasted image 20251212120125.png]]


# 98. Switch Users and sudo Access

- `su - username`: 다른 사용자 계정으로 전환 (그냥 su - 하면 root 됨, su root도 됨)
- `sudo command`: 관리자 아닌 유저가 관리자 권한으로 실행, 해당 유저가 권한이 있어야 함
- `visudo` : 사용자가 특정 명령을 실행할 수 있는 권한을 구성하는 etc/sudoers 파일을 편집
(파일을 편집하다가 문법 오류가 나면 컴파일이 안 돼서 sudo 사용 불가. visudo를 쓰는 게 좋음)

File
- **/etc/sudoers**

 visudo를 치면 아래와 같이 sudoers 파일을 vi로 연다.
 /wheel을 검색한다. 
 
![[Pasted image 20251212120942.png]]

- Allow root to run any commands anywhere 에서 root 밑에 사용자 이름 추가해서 똑같이 쓰면 root와 같이 모든 커맨드 사용 가능, 그러나 이건 사용자 수준에서 접근하는 것
- 그 밑에, wheel이라는 그룹에 사용자를 추가하면 마찬가지로 모든 커맨드 사용가능, 그룹 수준
-> visudo 명령어로 이걸 하면 됨

`usermod -aG wheel babubutt`: 이미 있는 사용자(`-a`) babubutt를 wheel 그룹에 추가(`-G`) 

![[Pasted image 20251212121736.png]]

% 참고: wheel 그룹은 bit wheel 이라는 관용어구에서 유래. root 비밀번호를 알더라도 wheel 그룹에 속해 있지 않으면 sudo 명령어를 사용할 수 없음. 



# 99. Monitor Users

**옵션과 함께 사용하는 게 좋음**

- `users`: 현재 로그인한 사용자 이름 목록록
- `who`: 시스템에 로그인한 사용자 보여줌. 터미널 하나 더 열면 하나 더 생김.
![[Pasted image 20251212161902.png]]

- `last`: 첫날부터 로그인한 사용자의 세부 정보
![[Pasted image 20251212163407.png]]

- `w`: who와 비슷하지만 좀 더 자세한 정보 제공
![[Pasted image 20251212163559.png]]

- `finger`: 시스템에 직접 설치해야 하는 프로그램. -> 이미 깔려있는듯?
![[Pasted image 20251212163637.png]]


- `id`: 그냥 실행하면 현재 사용자 정보, id [사용자명]하면 해당 사용자 정보 알려줌.

![[Pasted image 20251212163728.png]]


---


#2025-12-13

# Lab: 팀 계정 관리 및 비밀번호 정책 시행

1. Examine existing user and group information
- `id`
- `groups`
- `cat` on passwd file
- `cat` on group file
- account details / group information

1. Review password and account details
- `cat` on passwd file
- `grep` with labsuser
- `ls` with long format on /etc library
- Account and authentication

1. Check accees rights and environment
- `whoami`
- `echo` on home variable
- `echo` on shell variable
- `id`

1. Monitor user activity
- `who` is logged in
- what sessions are active
- login history
	- `who`
	- `w`
	- `last`


# 101. Talking to Users

리눅스에 로그인한 다른 유저들에게 메시지 보내기 (이게 된다니)

먼저 ssh로 다른 유저 이름으로 접속한다. (접속한 후 계정을 바꿔도 who에는 처음 접속한 유저로 나옴)

![[Pasted image 20251213142908.png]]

나는 ssh에서 babutbutt으로 로그인했다.

[ssh 터미널에서 wall 메시지가 안 보이는 문제]([https://blog.naver.com/student_id/224108381720](https://blog.naver.com/student_id/224108381720))
 

---

#2025-12-14
# 102. setuid, setgid and sticky bit

***중요: 이 비트들은 c언어 exe만 적용되며 bash shell scrip는 적용 안 됨***

- `setuid`: 
	- `chmod u+s xyz.sh`
	- `chmod u-s xyz.sh`
	
- `setgid`
	- `chmod g+s xyz.sh`
	- `chmod g-s xyz.sh`
	
- sticky bit 
	- `-rwxrwxrwt`의 마지막 t 자리에 할당됨. 예시) `/tmp` 디렉토리

- setuid랑 setgid가 설정된 모든 커맨드 볼 수 있음
	- `find / -perm /6000 -type f`


1. which 명령어로 passwd 파일 위치 찾고 권한 보기
	- user의 실행 부분이 setuid 비트로 설정되어 있음.
	
![[Pasted image 20251214175135.png]]

1. passwd 명령어 실행 후 프로세스 보기 (`ps -ef | grep passwd`)
	- passwd의 owner인 root가 실행했다는 걸 볼 수 있음. 
	
![[Pasted image 20251214175303.png]]

![[Pasted image 20251214175332.png]]


<실습>

아래와 같이 폴더를 만들어 777 권한을 준다. 
-> 모든 사용자가 다른 사용자가 만든 폴더를 실수로 건들 수 있음.

`drwxrwxrwx.   2 root root    6 Dec 14 18:00 allinone`

이를 방지하기 위해 마지막 x 자리에 sticky bit를 설정해준다.

`chmod +t /allinone`

![[Pasted image 20251214180815.png]]


그러면 아래와 같이 777 권한인 폴더라도 다른 사용자 거라면 삭제가 안 된다.

![[Pasted image 20251214181038.png]]

(디렉토리 삭제: `rm -rf [디렉토리])


# Directory Services

기업에서 사용자 계정을 관리하는 도구들

### 프로토콜
- LDAP : Lightweight Directory Access Protocol
	- 네트워크 상에서 조직, 개체, 사용자 정보 등을 찾고 수정하기 위한 통신 프로토콜

### 서버 소프트웨어 (LDAP를 사용함)
- Active Directory 
	- Microsoft에서 만든, 사실상 표준이나 다름없음. 
- OpenLDAP 
	- 오픈소스라 무료. 리눅스/유닉스 환경에서 가장 많이 쓰임
	- AD처럼 화려한 기능은 부족, 가볍고 표준을 엄격하게 지킴
- IBM Directory Server
	- IBM에서 만든 기업용 유료 LDAP 서버
- JumpCloud
	- 클라우드형 AD.
	- 서버를 실제로 구축할 필요 없음, 윈도우, 맥, 리눅스, 구글 워크스페이스 등을 한 번에 묶어줌.

### 연결 도구
- WinBIND = Used in Linux to communicate with Windows(Samba)
	- 윈도우 유저가 리눅스에 로그인하고 싶을 때 사용
	- 예를 들어 회사는 AD로 계정 관리하는데 개발팀은 리눅스 서버를 쓸 때. 로그인 시 리눅스가 윈도우 AD에게 인증을 요청함. 
	- 핵심: 리눅스 서버를 윈도우 AD 도메인에 가입시켜서 윈도우 아이디로 리눅스에 로그인하게 해줌


### 관리 시스템
- IDM(Identity Management)
	- LDAP나 AD는 정보를 저장하는 곳이라면 IDM은 계정 수명 주기를 관리하는 통합 솔루션
	- 기능: 버튼 하나만 눌러도 AD 계정 생성 + 이메일 생성 + 사원증 발급 권한까지 자동으로 함.

---

#2025-12-15


# 105. System Utility Commands

- `date`: 현재 시스템 날짜, 시간 알려줌
- `uptime`: 시스템 시간, running 시간, 유저 수, cpu 로드 평균 알려줌
- `hostname`: 장비 이름 알려줌
- `uname`: OS 알려줌 (-a 붙이면 더 자세한 정보)
- `which`: 특정 커맨드 파일 위치를 알려줌
- `cal`: cal만 입력하면 해당 달력 나오고 그 뒤에 년도나 월, 일 지정 가능
- `bc`: 간단한 계산기. quit 입력하면 나감

![[Pasted image 20251215182528.png]]


# 106 ~ Processes and Jobs

## Systemctl

**서비스 이름 말고 PID로도 사용 가능**

- `systemctl` or `service`
	: 버전 7 이상은 systemctl로 대체, 그 전은 service
	- `systemctl start|stop|status servicename.service`
	- `systemctl enable|disable servicename.service` : 부팅할 때 같이 활성화 or 비활성화
	- `systemctl restart|reload servicename.servcie`
	- `systemctl list-units --all` : 모든 서비스 정보 

> 활성화/실행 중인 프로세스일 경우
![[Pasted image 20251215183915.png]]

>활성화/실행 중이지 않은 프로세스
>config 파일이 enabled 이지만 inactive 상태
![[Pasted image 20251215184227.png]]

>비활성화 중인 프로세스
>config 파일이 disabled 상태
![[Pasted image 20251215184331.png]]


`systemctl list-units` 을 치면 아래와 같이 실행 중인 서비스의 UNIT, LOAD, ACTIVE, SUB, DESCRIPTION이 나옴 (`--all`을 붙이면 전체 서비스)
![[Pasted image 20251215184648.png]]


만약 third party 등 새로운 애플리케이션을 깔아서 systemctl로 관리하고 싶으면 `/etc/systemd/system/servicename.service`에 새 unit file을 만들어야 함. 

- `systemctl poweroff|halt|reboot`: 시스템 전원 끄기/중지/재시작


## ps 커맨드

`ps`: 현재 shell의 프로세스들을 보여줌.
![[Pasted image 20251215185231.png]]

`ps -e`: 현재 실행 중인 모든 프로세스들
![[Pasted image 20251215185356.png]]

`ps aux`: 실행 중인 모든 프로세스들을 BSD 포맷으로 보여줌
![[Pasted image 20251215185539.png]]

`ps -ef`: (제일 많이 사용) full format으로 모든 실행 중인 프로세스 보여줌
![[Pasted image 20251215185627.png]]


`ps -u username`: username이 실행 중인 모든 프로세스 

![[Pasted image 20251215185710.png]]


## top 커맨드

`top`
: 리눅스 프로세스와 실시간 view를 보여줌(PID, USER, PR 등... 3초마다 실시간으로 변함)
: 시스템 정보 요약과 리눅스 커널에서 현재 관리되고 있는 프로세스 or thread 리스트 보여줌
: top 커맨드를 사용하면 상호작용 모드로 들어가며 q로 나올 수 있음
: root가 아니어도 됨

![[Pasted image 20251215190235.png]]


- `top -u username`: username이 사용하는 프로세스 보여줌
- `top` + c: command의 경로 보여줌
![[Pasted image 20251215191057.png]]
- `top` + m : 메모리 사용량을 보여주고 정렬

![[Pasted image 20251215191148.png]]

- `top` + k : PID를 입력해서 프로세스 끌 수 있음.

#2025-12-16

## kill 커맨드

`kill`: 프로세스를 수동으로 종료하는 명령어. systemctl 명령어로도 안 될 때 사용

`kill [option] [PID]`
option: signal name 또는 signal number/ID

`kill -l`: 전체 signal name 리스트를 볼 수 있음.

`kill PID`: default signal로 프로세스 종료
`kill -1`: 재시작
`kill -2`: ctrl+C와 같은 역할
`kill -9`: Forcefully kill the process
`kill -15`: Kill a process gracefully 

**비슷한 명령어**
`killall`: 관련된 프로세스들까지 함께 다 종료
`pkill`: 프로세스 이름으로 종료 가능


Signal 옵션에 대한 강의 있는데 그냥 듣기만 함. 중요해 보이는데 나중에 알아보기

- Standard signal
SIGINT, SIGTERM, SIGKILL, SIGSTOP, SIGCONT

- Real-time signal
SIGRTMIN

## crontab 커맨드

`crontab`: 작업 스케쥴하는 커맨드

`crontab -e`: crontab 편집
`crontab -l`: List the crontab entries
`crontab -r`: crontab 삭제
`crond`: crontab deamon/service that manages scheduling
`systemctl status crond`: crond service 들 관리

crontab의 각 column은 시간을 나타냄.
분, 시간, 일(1~31), 달(1~12), 주중 며칠인지(0~6 = 일요일부터 토요일)
 ![[Pasted image 20251216131824.png]]
![[Pasted image 20251216131821.png]]

근데 나는 왜 파일이 안 생기지?
> 24시간 기준으로 써야 됨


![[Pasted image 20251216172522.png]]

![[Pasted image 20251216172558.png]]

## at 커맨드

`at` : 딱 하나만 스케줄할 수 있는 명령어. ctrl+d 로 나올 수 있음. 

`at HH:MM PM`: 스케줄하기
`atq`: list 보기
`atrm #` 엔트리 삭제
`atd`: 스케줄링을 담당하는 `at` daemon/service
`systemctl status atd`: atd service 상태 보기


- 스케쥴하기
![[Pasted image 20251216173531.png]]

**그 외 스케쥴링 방법**

`at 2:45 AM 101621`: 10월 16일, 2021년 스케쥴
`at 4PM + 4 days` : 지금부터 4일 후 오후 4시
`at now +5 hours`: 지금부터 5시간 후
`at 8:00 AM Sun`: 첫 번째 일요일 오전 8시
`at 10:00 AM next month` : 다음 달 오전 10시


---

#2025-12-21


# 114. Additional Cron Jobs

디폴트로 4개의 종류가 있음. 
- hourly
- daily
- weekly
- monthly

위의 모든 cron 파일들은 여기에 저장됨:
- `/etc/cron._` (directory)

![[Pasted image 20251221174559.png]]


![[Pasted image 20251221174701.png]]

각 디렉토리에 들어가서 ls -l 하면 지정되어 있는 cron을 볼 수 있음.
만약 추가하고 싶다면 맞는 디렉토리에 스크립트를 추가하면 됨.

- 스케쥴링 되어 있는 작업들의 타이밍을 보고 싶다면:
`/etc/anacrontab` (hourly는 제외)

![[Pasted image 20251221175059.png]]

- houlry로 지정된 cron들은 다음 파일에서 볼 수 있음:
`/etc/cron.d/0hourly`
![[Pasted image 20251221175307.png]]


# 115. Process Management


`bg`: background에서 실행중인 프로세스 보여줌
`jobs`: 실행 중인 프로세스 목록 (ps -ef | grep과 비슷한 결과)
`nohup process &`: 프로세스를 터미널에 종속되지 않게 실행
	`nohup process # > /dev/null 2>&1 &`: 결과에 나오는 로그 안 보게
이거 한 후 `fg` 실행하면 foreground에 실행 중인 프로세스를 볼 수 있음.
`nice -n 5 sleep 10` : sleep 10 명령어를 우선순위 5를 주어서 실행. -20~19까지. 낮을수록 높음


# 116. System Monitoring

`df`: disk partition 정보를 보여준다.
- `-h`: 읽기 쉬운 단어로 보여줌
![[Pasted image 20251221205925.png]]

![[Pasted image 20251221210027.png]]

`dmesg`: warning, error message 등 부팅 이후의 하드웨어 관련 이슈 로그들 볼 수 있음.

![[Pasted image 20251221210402.png]]

`iostat`: disk 관련 사용량 보여줌.  
- `iostat 1`: 1초마다 갱신해서 보여줌 

![[Pasted image 20251221210555.png]]

`ip route`: 라우팅 관련 정보 (게이트웨이, IP 등)
- `ip route | column -t`: 좀 더 읽기 쉽게 보여줌
![[Pasted image 20251221210641.png]]

`ss | more`: socket statistics. 시스템의 네트워크 소켓 상태와 통계 확인 가능
![[Pasted image 20251221210856.png]]

`free` : swap space, free 등 물리적 메모리 사용 현황 알려줌

![[Pasted image 20251221211022.png]]

`cat /proc/cpuinfo`: 시스템 Cpu 리소스 정보를 저장하는 파일
`cat /proc/meminfo`: 메모리 정보 저장하는 파일. 
	- `/proc`: 시스템 리소스 정보를 저장하는 폴더
![[Pasted image 20251221211213.png]]


---

#2025-12-22

# 117. Log Monitoring

`/var/log`: 리눅스의 로그들이 저장되는 기본 폴더.
`ll`: 해당 디렉토리의 모든 파일들을 알파벳 순으로 정렬해 보여줌

로그 폴더에 있는 대표적인 것들
`boot.log`: 시스템 부팅 과정의 이슈 로그. 재부팅하면 덮어씌워짐
`chrony/`: cron 로그들을 볼 수 있음.
`maillog`: email 관련된 활동 로그
`secure`: 로그인, 로그아웃 활동과 관련된 로그. 잘못된 비밀번호 입력 등도 여기 있음.
`messages`: 하드웨어, 소프트웨어, 앱 등 모든 로그가 들어있음. 


# 119. System Maintenance Commands

`shutdown` 
`init 0-6`: systemd service.  0부터 6까지의 옵션이 있음.
`reboot`: 재부팅
`halt` : 프로세스 실행 여부와 상관없이 시스템 종료 (= `shutdown -h`)


# 120. Changing System Hostname

`hostnamectl - set-hostname newhostname`: hostname을 newhostname으로 바꾸기

![[Pasted image 20251222183446.png]]

# 121. Finding System Information

`cat /etc/redhat-release`: 운영체제 이름
![[Pasted image 20251222183847.png]]
`uname -a` 
![[Pasted image 20251222184445.png]]


`dmidecode`: 하드웨어, 메모리 등 많은 정보 알려줌


# 122. System Architecture

32비트와 64비트 CPU의 차이: 초당 계산할 수 있는 수
32비트에서 64비트 프로그램 불가, 반대는 가능

`arch`: cpu 칩셋 알려줌
![[Pasted image 20251222184904.png]]



---

#2025-12-23

# 123. SOS Report

`sosreport`: 진단 보고서 작성? 같은 거. 모아서 var/tmp 디렉토리에 tar 파일로 저장함.


# 124. Terminal Control Keys

`ctrl + u` : 커맨드 라인에 있는 모든 것들 지워줌
`ctrl + c` : stop or kill 
`ctrl + z`: suspend 
`ctrl + d`: interacvie program으로부터 나오기 


# 125. Terminal Commands

`clear`: clears your screen

`exit`: exit out of the shell, terminal or a user session

`script`: stores terminal activities in a log file that can be named by a user, when a name is not provided by a user, the default file name, typescript is used.

`script` 명령어를 이름과 함께 실행하면 해당 이름을 가진 파일이 디렉토리에 생기고, 그 시점부터 모든 shell 내용이 파일에 저장됨. exit 하면 저장 끝낼 수 있음.
ex) script activity.log


# 126. Recover Root Password

1. 시스템 재시작 ()
2. grub 파일 편집
3. 패스워드 변경
4. 재시작



 
![[Pasted image 20251223153956.png]]

![[Pasted image 20251223154111.png]]

여기서 컨트롤 x로 빠져나오면 싱글유저모드로 들어간다.
거기서 아래와 같이 명령어를 통해 비밀번호를 바꿔준다.

![[Pasted image 20251223154316.png]]

exit 하고 reboot 하면 됨.


# 127. Environment Variables

환경 변수란 프로세스의 실행에 영향을 주는 dynamic-named 한 값. 

`printenv` or `env`: 모든 환경 변수 보기

![[Pasted image 20251223171700.png]]

`echo $shell`: shell 자리에 환경 변수 이름 넣어서 특정 환경 변수 볼 수 있음. 

- 환경 변수 편집 (일시적. 로그아웃하면 사라짐)
`export TEST=1`
`echo $TEST`

- 환경변수 편집 (고정적)
`cp .bashrc bashrc.orig` > 오리지널 백업
`vi .bhasrc`
`TEST='123'`
`export TEST`

`vi /etc/profile` or `/etc/bashrc`
`Test='123'`
`export TEST`



# 128. The Screen Command

`screen`: 리눅스에서 세션을 생성하고 이를 관리하는 명령어. 연결이 끈겨도 백그라운드에서 작업이 계속 진행됨.

`screen` 으로 시작 -> screen : 0에서부터 열림

명령어 나중에 따로 알아보기


# 129. tmux Command
- `screen` 명령어에 비해 modern 하고 dynamic한 명령어. 
- 더 좋은 기능과 유연성을 가짐. 


1. tmux 있는지 확인하고 다운받기


![[Pasted image 20251225115447.png]]

2. tmux 실행
	- `[0]`: 현재 세션 넘버
	- 0:bash : Window 0를 뜻함.
	- `*`: 현재 활성화된 윈도우

![[Pasted image 20251225115528.png]]


세로로 나누기: `ctrl + b` ->`shift + %` 
(화면 스위칭은 컨b 누르고 방향키)

![[Pasted image 20251225115712.png]]


가로로 나누기: `ctrl + b` -> `shift + "`
![[Pasted image 20251225115839.png]]

아랫줄에 특정 윈도우에서 실행하고 있는 커맨드가 나옴.

![[Pasted image 20251225115930.png]]


tmux에서 나오기(백그라운드에 세션 남아 있음) : `ctrl + b` -> `d`

현재 실행중인 윈도우 확인 : `tmux ls`

![[Pasted image 20251225120055.png]]

다시 tmux로 돌아가기 : `tmux a`

원하는 이름으로 세션 시작하기: `tmux new -s [name]`

창 나누지 않고 새로운 윈도우 생성: `ctrl + b` -> `c`

다른 윈도우로 스위칭: `ctrl + b` + `n`

윈도우 닫기: `ctrl + d` -> 세션을 닫는 게 아니라 창 하나 닫는 거

특정 넘버의 세션으로 돌아가기: `tmux attach-session -t [#]`

윈도우 이름 재설정: `ctrl + b` + `,`

tmux 세션 종료: `tmux kill-session -t [#]`



