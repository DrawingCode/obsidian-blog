[[유데미 리눅스]]

# 154. Network Files and Commands

인터페이스 설정 파일
- `/etc/nsswitch.conf` : 리눅스 시스템이 다양한 정보(사용자 이름, 호스트 이름 등)를 어디서 어떤 순서로 찾을지 결정하는 역할. 
- `/etc/resolv.conf` : DNS한테 물어볼 때 IP 주소 맵핑하는 역할
- `/etc/hosts` : ip에 이름 붙일 수 있음, dns에 물어보기 전 이 파일 확인
	- 구조: ip주소 호스트이름 별칭
	- 장비 관리할 때 여기 등록해두면 편할 듯.
- `/etc/sysconfig/network`: 리눅스 시스템의 전역 네트워크 설정을 담고 있는 파일. 현재 버전에서는 역할이 축소되어 주석만 있음. 
- `/etc/sysconfig/network-scripts` : 각 네트워크 인터페이스의 설정 파일이 모여 있는 디렉토리. 원래 이 디렉토리의 ifcfg 파일을 수정해서 IP를 잡았으나 현재는 사용 중단 예정 되어 NetworkManager에게 역할을 위임. 
	- `/etc/NetworkManager/system-connections/` 경로를 사용. `.nmconnection` 확장자


네트워크 커맨드
- `ping`
- `ip a`
- `ifup` or `ifdown`
- `netstat`
- `tcpdump`



## 1. /etc/nssiwtch.conf

정보종류: 소스1 소스2 \[옵션\]

=> 이 정보 종류를 찾을 때는 소스1을 찾아보고 없으면 소스2를 찾아보라는 뜻.

예시) 여기서 hosts 설정을 보자. 이 설정은 컴퓨터가 호스트 이름을 IP 주소로 바꿀 때 어떤 순서로 할지 결정한다. 
1. files: 로컬 파일 `/etc/hosts` 를 확인
2. dns: 파일에 없으면, 인터넷상의 DNS 서버에 물어봄
3. myhostname: 그래도 없으면, 내 로컬 호스트 이름인지 확인
![[Pasted image 20260107144046.png]]


**데이터베이스**
콜론 왼쪽에 있는 항목들을 이르는 말. 

|**데이터베이스**|**설명**|**관련 파일 (files 소스 사용 시)**|
|---|---|---|
|**passwd**|사용자 계정 정보 (아이디, UID, 쉘 등)|`/etc/passwd`|
|**shadow**|사용자의 암호화된 비밀번호|`/etc/shadow`|
|**group**|그룹 정보|`/etc/group`|
|**hosts**|호스트 이름과 IP 주소의 매핑 정보|`/etc/hosts`|
|**services**|포트 번호와 서비스 이름의 매핑 정보|`/etc/services`|
![[Pasted image 20260107144457.png]]

`sss`: System Security Services. 기업 환경에서 많이 쓰임. LDAP나 AD 같은 외부 중앙 서버에서 사용자 정보를 가져올 때 사용. 

## 2. /etc/resolv.conf

![[Pasted image 20260107144805.png]]



# 155. NIC Information / ethtool

`ip a`를 했을 때 나오는 lo, enp0s3 등이 노트북에서 가지고 있는 NIC 인터페이스

![[Pasted image 20260107171958.png]]

`ethtool [interface]` : 인터페이스 정보 볼 수 있음.

![[Pasted image 20260107172115.png]]



#2026-01-25
# 156. NIC or Port Bonding

**NIC Bonding이란? (= Network Bonding)**
: 여러 NIC를 하나의 인터페이스로 묶는 것
하나의 포트가 죽어도 다른 포트로 연결 가능. 
가용성 + 대역폭 두 배

ip a 하면 나오는 아래 enp0s3, enp0s8을 하나의 가상 인터페이스 합칠 예정


![[Pasted image 20260125183938.png]]



`modinfo bonding | more` 해서 이미 설치되어 있는지 검사
![[Pasted image 20260125184115.png]]


먼저 하나의 가상 인터페이스를 만들어 준다.
- `vi /etc/sysconfig/network-scripts/ifcfg-bond0`
- 아래 내용 입력
```BASH
DEVICE=bond0
TYPE=Bond 
NAME=bond0 
BONDING_MASTER=yes 
BOOTPROTO=none 
ONBOOT=yes 
IPADDR=192.168.219.109 # 새로 만들 인터페이스 주소 
NETMASK=255.255.255.0 
GATEWAY=192.168.1.1 # 현재 쓰고 있는 게이트웨이 주소
BONDING_OPTS=”mode=5 miimon=100”
```

- `miimon`은 몇 millisecond마다 각 slave 인터페이스들의 상태를 점검할지 정하는 것 
- 여기서 Bonding options는 7개의 모드가 있으며 아래 표와 같다.


![[Pasted image 20260125201335.png]]


/etc/sysconfig/ifcfg-ens0s3 내용을 모두 지우고 아래와 같이 써준다.
이때 MAC 주소는 `ip a` 쳐서 나오는 것으로 입력해야 함.


![[Pasted image 20260125200244.png]]

enp0s8에 대한 파일도 만들어 주는데, 방금 만든 ens0s3 파일을 복사해준다.
- `cp ifcfg-enp0s3 ifcfg-enp0s8`

그리고 vi로 열어서 DEVICE 이름과 HWADDR 주소를 enp0s8에 맞게 수정해 준다.


`systemctl restart NetworkManager` 로 서비스 재시작 후 `ifconfig`로 인터페이스를 본다.
(Centos 9 기준)

![[Pasted image 20260125200432.png]]

ssh 접속도 무사히 되는 것 확인.
![[Pasted image 20260125200531.png]]



다음 명령어로 bond0의 현재 런타임 상태를 볼 수 있다.
- `cat /proc/net/bonding/bond0`

![[Pasted image 20260125201657.png]]

엇...근데 enp0s8밖에 안 나온다.
이럴 때는 아래 명령어를 사용해 bond0의 slave가 누가 있는지 본다.
- `cat /sys/class/net/bond0/bonding/slaves`

![[Pasted image 20260125203135.png]]

enp0s8만 나오는 걸 보니 enp0s3가 연결이 안 된 것 같다.

음... 재부팅하니까 되긴 됨

![[Pasted image 20260125204117.png]]


---

정리
- `vi /etc/sysconfig/network-scripts/ifcfg-bond0`
- `vi /etc/sysconfig/network-scripts/ifcfg-enp0s3` / enp0s8도 똑같이 만들어주기
- `cat /proc/net/bonding/bond0`


---

#2026-04-26
# secure SSH 설정

### idle timeout inerval 설정하기

1. root로 로그인
2. `/etc/ssh/sshd_config` 파일에 다음 문장 추가
- `ClientAliveInterval 600`
- `ClientAliveCountmax 0`
3. 재실행 `# systemctl restart sshd`


#### 실습

sshd_config 파일을 따로 복사해서 백업해 두고 
vi로 들어가서 `shift+G`를 눌러 맨 아랫줄로 내려간 뒤
아래와 같이 문장을 추가해 준다.

![[Pasted image 20260426183945.png]]



### root 로그인 비활성화

1. root로 로그인
2. `/etc/ssh/sshd_config`파일에 들어가서 PermitRootLogin을 yes에서 no로 수정
- `PermitRootLogin no`
3. 재시작 `systemctl restart sshd`


### empty 패스워드 비활성화

1. root로 로그인
2. `/etc/ssh/sshd_config` 파일에 들어가서 PermitEmptyPasswords no에 # 삭제
3. 재시작

![[Pasted image 20260426184449.png]]


### 유저의 SSH 접속 제한

1. root로 로그인
2. `/etc/ssh/sshd_config` 파일에 추가
- `AllowUsers user1 user2`
3. 재시작


### 포트번호 바꾸기

1. root로 로그인
2. `/etc/ssh/sshd_config` 파일에서 아래 문장의 # 없애고 포트 번호 바꾸기
- `# Port 22`

![[Pasted image 20260426185212.png]]