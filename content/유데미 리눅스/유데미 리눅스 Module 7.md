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