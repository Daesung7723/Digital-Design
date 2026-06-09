# Ubuntu 네트워크 실습

## Python 네트워크 점검 도구와 VMware FTP 실습

---

## 1. 실습 개요

이번 실습에서는 Ubuntu에서 네트워크 상태를 확인하고, Apache 웹 서버를 실행한 뒤, Python으로 주요 포트가 열려 있는지 점검합니다.

또한 VMware 환경에서 Ubuntu를 사용하는 경우를 고려하여, **자기 VM 내부 실습**과 **옆자리 학생 VM과의 FTP 실습**을 구분하여 진행합니다.

핵심 개념은 다음과 같습니다.

> 리눅스에서 서비스가 실행된다는 것은 특정 포트에서 네트워크 요청을 기다린다는 의미이다.

기본 실습 흐름은 다음과 같습니다.

```text
네트워크 상태 확인
→ Apache 웹 서버 실행
→ Python으로 포트 점검
→ Python으로 웹 서버 응답 확인
→ VMware 환경에서 FTP 연결 가능 여부 확인
→ 가능한 경우 옆자리 학생과 FTP 실습
```

---

## 2. 실습 목표

실습을 마치면 다음 내용을 할 수 있어야 합니다.

1. Ubuntu에서 네트워크 상태를 확인할 수 있다.
2. `NetworkManager`와 `nmcli`의 역할을 설명할 수 있다.
3. Apache 웹 서버가 실행되면 80번 포트가 열리는 것을 확인할 수 있다.
4. Python의 `socket` 모듈로 특정 포트가 열려 있는지 확인할 수 있다.
5. Python으로 웹 서버 응답 상태를 확인할 수 있다.
6. VMware 네트워크 모드에 따라 VM 간 통신 가능 여부가 달라질 수 있음을 이해할 수 있다.
7. 조건이 맞는 경우 옆자리 학생의 Ubuntu VM에 FTP로 접속할 수 있다.

---

## 3. VMware 환경 주의사항

이번 실습은 VMware에서 Ubuntu를 실행하는 환경을 기준으로 합니다.

VMware의 네트워크 모드에 따라 내 Ubuntu VM이 다른 학생의 Ubuntu VM과 통신할 수 있는지가 달라집니다.

| VMware 네트워크 모드    | 옆자리 학생 VM 접속 가능성 | 설명                                                  |
| ----------------- | ----------------:| --------------------------------------------------- |
| NAT               | 낮음               | 내 노트북 내부의 가상 네트워크를 사용하므로 다른 학생 VM에 직접 접속하기 어려울 수 있음 |
| Bridged           | 가능성 있음           | VM이 강의실 네트워크에 직접 연결되어 다른 학생 VM과 통신할 수 있을 가능성이 있음    |
| Host-only         | 낮음               | 내 노트북의 Host OS와 VM 사이 통신 중심                         |
| 같은 노트북에서 VM 2개 실행 | 가능               | 두 VM이 같은 가상 네트워크에 있으면 VM끼리 통신 가능                    |

따라서 이번 실습은 다음 기준으로 진행합니다.

```text
필수 실습: 자기 Ubuntu VM 내부에서 완료 가능해야 한다.
선택 실습: 옆자리 학생 VM과 통신이 가능한 경우에만 진행한다.
```

옆자리 학생과 통신하려면 보통 VMware 네트워크 모드가 `Bridged`여야 할 수 있습니다.  
하지만 학교 Wi-Fi나 강의실 네트워크 정책에 따라 Bridged 모드에서도 학생 간 통신이 차단될 수 있습니다.

접속이 안 되는 주요 원인은 다음과 같습니다.

```text
VMware가 NAT 모드로 설정되어 있음
무선 AP의 client isolation 설정
학교 네트워크 보안 정책
방화벽 설정
서로 다른 네트워크 대역
서버 학생의 IP 주소 입력 오류
```

---

## 4. 주요 개념

### 4.1 NetworkManager

`NetworkManager`는 Ubuntu에서 네트워크 연결을 관리하는 서비스입니다.

확인할 수 있는 정보는 다음과 같습니다.

```text
IP 주소
게이트웨이
DNS
네트워크 장치 연결 상태
```

상태 확인 명령어:

```bash
systemctl status NetworkManager
```

정상 실행 상태라면 다음과 같은 표현이 보입니다.

```text
active (running)
```

---

### 4.2 nmcli

`nmcli`는 NetworkManager를 터미널에서 확인하고 제어하는 명령어입니다.

네트워크 장치 상태 확인:

```bash
nmcli dev status
```

네트워크 장치 상세 정보 확인:

```bash
nmcli dev show
```

---

### 4.3 주요 포트 번호

| 포트 번호 | 서비스                | 의미             |
| -----:| ------------------ | -------------- |
| 21    | FTP                | 파일 전송 서비스      |
| 22    | SSH                | 원격 접속 서비스      |
| 80    | HTTP               | 웹 서버 서비스       |
| 8000  | Python HTTP Server | Python 간단 웹 서버 |

예를 들어 Apache 웹 서버가 실행 중이면 일반적으로 80번 포트가 열립니다.  
FTP 서버가 실행 중이면 일반적으로 21번 포트가 열립니다.

---

## 5. 실습 1: 네트워크 상태 확인

### 5.1 NetworkManager 상태 확인

터미널에서 다음 명령어를 실행합니다.

```bash
systemctl status NetworkManager
```

확인할 내용:

```text
Active: active (running)
```

---

### 5.2 네트워크 장치 상태 확인

```bash
nmcli dev status
```

출력 예시:

```text
DEVICE   TYPE      STATE      CONNECTION
ens33    ethernet  connected  netplan-ens33
lo       loopback  connected  lo
```

다음 내용을 확인합니다.

```text
1. 네트워크 장치 이름
2. 장치 유형
3. 연결 상태
4. 연결 이름
```

---

### 5.3 IP 주소 확인

다음 명령어 중 하나를 실행합니다.

```bash
nmcli dev show
```

또는:

```bash
ip addr
```

자신의 IP 주소를 확인하여 기록합니다.

```text
내 Ubuntu IP 주소: ______________________________
```

---

## 6. 실습 2: Apache 웹 서버 설치 및 확인

### 6.1 Apache 설치

터미널에서 다음 명령어를 실행합니다.

```bash
sudo apt update
sudo apt install apache2
```

설치가 완료되면 Apache 상태를 확인합니다.

```bash
systemctl status apache2
```

정상 실행 상태:

```text
active (running)
```

---

### 6.2 웹 브라우저에서 접속 확인

웹 브라우저에서 다음 주소로 접속합니다.

```text
http://127.0.0.1
```

또는 자신의 Ubuntu IP 주소로 접속합니다.

```text
http://내_IP_주소
```

Apache 기본 페이지가 보이면 성공입니다.

---

### 6.3 테스트 HTML 페이지 생성

다음 명령어를 실행합니다.

```bash
echo "<h1>Hello Ubuntu Network</h1>" | sudo tee /var/www/html/test.html
```

웹 브라우저에서 다음 주소로 접속합니다.

```text
http://127.0.0.1/test.html
```

또는:

```text
http://내_IP_주소/test.html
```

다음 문장이 보이면 성공입니다.

```text
Hello Ubuntu Network
```

---

## 7. 실습 3: Python 포트 점검 프로그램 작성

### 7.1 실습 목적

Python을 사용하여 특정 IP 주소의 포트가 열려 있는지 확인합니다.

이번 실습에서 확인할 포트는 다음과 같습니다.

```text
21번 포트 → FTP
22번 포트 → SSH
80번 포트 → HTTP
8000번 포트 → Python 간단 웹 서버
```

---

### 7.2 Python 파일 생성

터미널에서 다음 명령어를 실행합니다.

```bash
nano network_check.py
```

---

### 7.3 코드 작성

아래 코드를 입력합니다.

```python
import socket

def check_port(host, port):
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.settimeout(3)

    result = sock.connect_ex((host, port))
    sock.close()

    return result == 0

host = input("점검할 IP 주소를 입력하세요: ")

ports = {
    21: "FTP",
    22: "SSH",
    80: "HTTP",
    8000: "Python HTTP"
}

print("\n[포트 점검 결과]")

for port, service in ports.items():
    if check_port(host, port):
        print(f"{service} 서비스({port}번 포트): 열려 있음")
    else:
        print(f"{service} 서비스({port}번 포트): 닫혀 있음")
```

저장 후 종료합니다.

```text
Ctrl + O → Enter → Ctrl + X
```

---

### 7.4 프로그램 실행

```bash
python3 network_check.py
```

입력 예시:

```text
127.0.0.1
```

또는 자신의 Ubuntu IP 주소를 입력합니다.

```text
192.168.xx.xx
```

---

### 7.5 실행 결과 예시

Apache가 실행 중인 경우 다음과 비슷하게 출력됩니다.

```text
점검할 IP 주소를 입력하세요: 127.0.0.1

[포트 점검 결과]
FTP 서비스(21번 포트): 닫혀 있음
SSH 서비스(22번 포트): 닫혀 있음
HTTP 서비스(80번 포트): 열려 있음
Python HTTP 서비스(8000번 포트): 닫혀 있음
```

결과를 아래에 기록합니다.

```text
점검한 IP 주소: ______________________________

21번 FTP 포트: ______________________________

22번 SSH 포트: ______________________________

80번 HTTP 포트: _____________________________

8000번 Python HTTP 포트: _____________________
```

---

## 8. 선택 실습 1: Python으로 웹 서버 응답 확인하기

시간이 남는 경우 진행합니다.

### 8.1 Python 파일 생성

```bash
nano web_check.py
```

---

### 8.2 코드 작성

```python
import urllib.request

url = input("확인할 웹 주소를 입력하세요: ")

try:
    response = urllib.request.urlopen(url, timeout=5)
    print("응답 코드:", response.status)
    print("응답 메시지:", response.reason)

    html = response.read().decode("utf-8", errors="ignore")
    print("\n[HTML 내용 일부]")
    print(html[:200])

except Exception as e:
    print("접속 실패:", e)
```

---

### 8.3 프로그램 실행

```bash
python3 web_check.py
```

입력 예시:

```text
http://127.0.0.1/test.html
```

예상 출력:

```text
확인할 웹 주소를 입력하세요: http://127.0.0.1/test.html
응답 코드: 200
응답 메시지: OK

[HTML 내용 일부]
<h1>Hello Ubuntu Network</h1>
```

---

## 9. 선택 실습 2: VMware에서 옆자리 학생과 연결 테스트

이 실습은 FTP 실습 전에 반드시 먼저 진행합니다.  
연결 테스트가 실패하면 옆자리 학생과 FTP 실습을 진행하지 않고 자기 VM 내부 실습으로 대체합니다.

---

### 9.1 역할 나누기

두 명이 한 조가 되어 역할을 나눕니다.

```text
학생 A: 서버 역할
학생 B: 클라이언트 역할
```

이후 시간이 되면 역할을 바꿔서 다시 진행합니다.

```text
학생 B: 서버 역할
학생 A: 클라이언트 역할
```

---

### 9.2 VMware 네트워크 모드 확인

VMware에서 Ubuntu VM의 네트워크 설정을 확인합니다.

확인 경로 예시:

```text
VMware → 해당 Ubuntu VM 선택 → Settings → Network Adapter
```

확인할 항목:

```text
NAT
Bridged
Host-only
```

옆자리 학생과 직접 통신하려면 일반적으로 `Bridged` 모드가 유리합니다.

단, 수업 중 임의로 네트워크 모드를 변경하기 어렵거나 학교 네트워크 정책상 통신이 차단될 수 있습니다.  
이 경우에는 자기 VM 내부 실습만 진행합니다.

---

### 9.3 서버 역할 학생: IP 주소 확인

서버 역할 학생은 자신의 Ubuntu IP 주소를 확인합니다.

```bash
ip addr
```

또는:

```bash
nmcli dev show
```

확인한 IP 주소를 클라이언트 역할 학생에게 알려줍니다.

```text
서버 역할 학생의 Ubuntu IP 주소: ______________________________
```

---

### 9.4 클라이언트 역할 학생: ping 테스트

클라이언트 역할 학생은 서버 역할 학생의 IP 주소로 연결 테스트를 합니다.

```bash
ping 서버학생_IP주소
```

예시:

```bash
ping 192.168.0.25
```

응답이 오면 두 VM 사이의 기본 네트워크 통신이 가능한 상태입니다.

응답 예시:

```text
64 bytes from 192.168.0.25: icmp_seq=1 ttl=64 time=3.21 ms
```

종료는 다음 키를 누릅니다.

```text
Ctrl + C
```

응답이 오지 않으면 다음 중 하나일 수 있습니다.

```text
VMware가 NAT 모드로 설정되어 있음
학교 네트워크에서 학생 간 통신을 차단함
방화벽이 ping 요청을 차단함
서버 학생의 IP 주소를 잘못 입력함
두 학생의 VM이 서로 다른 네트워크에 있음
```

이 경우 옆자리 학생과의 FTP 실습은 생략하고 자기 VM 내부 실습을 계속 진행합니다.

---

### 9.5 Python 포트 점검 프로그램으로 옆자리 VM 확인

ping이 성공한 경우, 클라이언트 역할 학생은 앞에서 작성한 `network_check.py`를 실행합니다.

```bash
python3 network_check.py
```

점검할 IP 주소에는 서버 역할 학생의 Ubuntu IP 주소를 입력합니다.

```text
점검할 IP 주소를 입력하세요: 서버학생_IP주소
```

서버 역할 학생의 VM에서 Apache가 실행 중이면 다음과 같이 나올 수 있습니다.

```text
HTTP 서비스(80번 포트): 열려 있음
```

이 결과는 서버 역할 학생의 Apache 웹 서버가 네트워크 요청을 기다리고 있음을 의미합니다.

---

## 10. 선택 실습 3: VMware용 FTP 실습

이 실습은 VMware 환경을 고려한 FTP 실습입니다.  
반드시 아래 순서대로 진행합니다.

---

### 10.1 FTP 실습 진행 조건

다음 조건을 만족할 때만 옆자리 학생과 FTP 실습을 진행합니다.

```text
1. 두 학생의 VM이 서로 통신 가능하다.
2. ping 테스트가 성공했다.
3. 서버 역할 학생의 Ubuntu IP 주소를 정확히 확인했다.
4. FTP 서버 설치와 접속을 진행할 시간이 있다.
```

조건을 만족하지 못하면 `10.9 자기 VM 내부 FTP 실습`으로 대체합니다.

---

### 10.2 역할 설정

2인 1조로 실습합니다.

```text
학생 A: FTP 서버 역할
학생 B: FTP 클라이언트 역할
```

먼저 A가 서버, B가 클라이언트로 실습한 뒤, 시간이 있으면 역할을 바꿉니다.

```text
학생 B: FTP 서버 역할
학생 A: FTP 클라이언트 역할
```

---

### 10.3 서버 역할 학생: FTP 서버 설치

서버 역할 학생은 다음 명령어로 FTP 서버를 설치합니다.

```bash
sudo apt update
sudo apt install vsftpd
```

설치 후 실행 상태를 확인합니다.

```bash
systemctl status vsftpd
```

정상 상태:

```text
active (running)
```

만약 실행 중이 아니라면 다음 명령어로 시작합니다.

```bash
sudo systemctl start vsftpd
```

---

### 10.4 서버 역할 학생: 테스트 파일 준비

서버 역할 학생은 홈 디렉터리에 FTP로 받을 수 있는 테스트 파일을 만듭니다.

```bash
echo "Hello FTP from Ubuntu VM" > ftp_test.txt
```

파일이 생성되었는지 확인합니다.

```bash
ls -l ftp_test.txt
```

현재 계정의 홈 디렉터리에 파일이 있어야 합니다.

```text
/home/사용자계정/ftp_test.txt
```

---

### 10.5 서버 역할 학생: IP 주소 다시 확인

서버 역할 학생은 자신의 Ubuntu IP 주소를 다시 확인합니다.

```bash
ip addr
```

또는:

```bash
nmcli dev show
```

클라이언트 역할 학생에게 알려줄 IP 주소를 기록합니다.

```text
FTP 서버 IP 주소: ______________________________
```

---

### 10.6 클라이언트 역할 학생: 21번 포트 확인

클라이언트 역할 학생은 `network_check.py`를 실행합니다.

```bash
python3 network_check.py
```

점검할 IP 주소에는 서버 역할 학생의 Ubuntu IP 주소를 입력합니다.

```text
점검할 IP 주소를 입력하세요: FTP서버_IP주소
```

다음 결과가 나오면 FTP 서버가 접속을 기다리는 상태입니다.

```text
FTP 서비스(21번 포트): 열려 있음
```

21번 포트가 닫혀 있으면 FTP 접속 전에 다음을 확인합니다.

```text
서버 역할 학생의 vsftpd가 active 상태인가?
서버 역할 학생의 IP 주소가 맞는가?
ping 테스트가 성공했는가?
VMware 네트워크 모드가 통신 가능한 상태인가?
학교 네트워크에서 접속을 차단하고 있지는 않은가?
```

---

### 10.7 클라이언트 역할 학생: FTP 접속

클라이언트 역할 학생은 서버 역할 학생의 IP 주소로 FTP 접속을 시도합니다.

```bash
ftp FTP서버_IP주소
```

예시:

```bash
ftp 192.168.0.25
```

로그인 화면이 나오면 서버 역할 학생의 Ubuntu 사용자 계정과 비밀번호를 입력합니다.

```text
Name: 서버학생_Ubuntu_계정
Password: 서버학생_Ubuntu_비밀번호
```

로그인 성공 메시지 예시:

```text
230 Login successful.
ftp>
```

---

### 10.8 클라이언트 역할 학생: FTP 명령 실행

FTP 접속 후 다음 명령어를 순서대로 실행합니다.

현재 원격 디렉터리 확인:

```ftp
pwd
```

원격 파일 목록 확인:

```ftp
dir
```

서버 학생이 만든 파일 가져오기:

```ftp
get ftp_test.txt
```

FTP 종료:

```ftp
bye
```

FTP를 종료한 뒤, 클라이언트 역할 학생의 현재 디렉터리에서 파일이 받아졌는지 확인합니다.

```bash
ls -l ftp_test.txt
cat ftp_test.txt
```

정상적으로 전송되었다면 다음 내용이 보입니다.

```text
Hello FTP from Ubuntu VM
```

---

### 10.9 자기 VM 내부 FTP 실습으로 대체하기

옆자리 학생 VM과 연결되지 않으면 자기 VM 내부에서 FTP 실습을 진행합니다.

이 경우 접속 대상은 자기 자신입니다.

```text
127.0.0.1
localhost
```

서버 설치:

```bash
sudo apt update
sudo apt install vsftpd
systemctl status vsftpd
```

테스트 파일 생성:

```bash
echo "Hello FTP localhost" > ftp_local_test.txt
```

FTP 접속:

```bash
ftp 127.0.0.1
```

또는:

```bash
ftp localhost
```

로그인 후 다음 명령어를 실행합니다.

```ftp
pwd
dir
get ftp_local_test.txt
bye
```

FTP 종료 후 파일 확인:

```bash
ls -l ftp_local_test.txt
cat ftp_local_test.txt
```

이 실습은 같은 VM 내부에서 진행되므로 VMware NAT 모드에서도 수행할 수 있습니다.

---

### 10.10 FTP 실습 결과 기록

아래 내용을 기록합니다.

```text
FTP 실습 방식:
[ ] 옆자리 학생 VM과 FTP 실습
[ ] 자기 VM 내부 localhost FTP 실습

FTP 서버 IP 주소: ______________________________

ping 성공 여부:
[ ] 성공
[ ] 실패
[ ] 자기 VM 내부 실습이라 해당 없음

21번 포트 상태:
[ ] 열려 있음
[ ] 닫혀 있음

FTP 접속 성공 여부:
[ ] 성공
[ ] 실패

가져온 파일명: ______________________________

실습 중 발생한 문제 또는 알게 된 점:
________________________________________________
________________________________________________
```

---

## 11. 결과 해석

### 11.1 포트가 열려 있다는 의미

포트가 열려 있다는 것은 해당 포트에서 어떤 프로그램이 네트워크 요청을 기다리고 있다는 뜻입니다.

예를 들어:

```text
80번 포트 열림 → Apache 웹 서버가 요청을 기다리는 중
21번 포트 열림 → FTP 서버가 요청을 기다리는 중
22번 포트 열림 → SSH 서버가 요청을 기다리는 중
8000번 포트 열림 → Python 간단 웹 서버가 요청을 기다리는 중
```

---

### 11.2 서비스와 포트의 관계

서버 프로그램은 보통 백그라운드에서 실행되며, 특정 포트를 통해 요청을 기다립니다.

```text
사용자 요청 → IP 주소 → 포트 번호 → 서비스 프로그램
```

예시:

```text
웹 브라우저 → http://127.0.0.1 → 80번 포트 → Apache
FTP 클라이언트 → ftp 서버IP주소 → 21번 포트 → vsftpd
```

---

### 11.3 VMware 환경에서 localhost와 IP 주소의 차이

VMware Ubuntu 안에서 `127.0.0.1`은 자기 Ubuntu VM 자신을 의미합니다.

```text
127.0.0.1 → 자기 Ubuntu VM 내부
localhost → 자기 Ubuntu VM 내부
내 Ubuntu IP 주소 → 같은 네트워크에서 접근 가능한 VM 주소
옆자리 학생 IP 주소 → 네트워크 설정이 허용될 때만 접근 가능
```

따라서 자기 VM 내부에서 Apache를 확인할 때는 다음 주소를 사용할 수 있습니다.

```text
http://127.0.0.1
```

다른 학생이 내 VM의 Apache에 접속하려면 내 Ubuntu VM의 IP 주소를 사용해야 합니다.

```text
http://내_Ubuntu_IP주소
```

단, VMware 네트워크 모드와 학교 네트워크 정책에 따라 다른 학생의 접속이 차단될 수 있습니다.

---

## 12. 제출물

다음 항목을 제출합니다.

```text
1. network_check.py 파일
2. 실행 결과 화면 캡처 1장
3. 자신의 Ubuntu IP 주소
4. 21, 22, 80, 8000번 포트 점검 결과
5. 80번 포트가 열려 있는 이유를 한 문장으로 설명
```

설명 예시:

```text
80번 포트가 열려 있는 이유는 Apache 웹 서버가 실행 중이기 때문이다.
```

옆자리 학생과 연결 테스트 또는 FTP 실습을 진행한 경우에는 다음 내용도 추가로 제출합니다.

```text
6. 옆자리 학생 VM과 ping 테스트 성공 여부
7. 옆자리 학생 VM의 21번 또는 80번 포트 점검 결과
8. FTP 접속 성공 여부
9. FTP로 가져온 파일명
10. 접속이 안 된 경우 추정 원인
```

---

## 13. 추가 과제

여유가 있는 학생은 다음 중 하나를 선택하여 수행합니다.

---

### 추가 과제 1: 점검 포트 추가

`network_check.py`에 다음 포트 중 하나 이상을 추가합니다.

| 포트   | 서비스        |
| ----:| ---------- |
| 53   | DNS        |
| 3306 | MySQL      |
| 5432 | PostgreSQL |
| 8080 | 대체 HTTP 포트 |

---

### 추가 과제 2: Python 간단 웹 서버 실행 후 점검

현재 디렉터리에서 Python 내장 웹 서버를 실행합니다.

```bash
python3 -m http.server 8000
```

새 터미널을 열고 `network_check.py`로 8000번 포트를 확인합니다.

예상 결과:

```text
Python HTTP 서비스(8000번 포트): 열려 있음
```

브라우저 접속:

```text
http://127.0.0.1:8000
```

---

### 추가 과제 3: 웹 응답 코드 비교

`web_check.py`를 실행하여 다음 주소의 응답 코드를 비교합니다.

```text
http://127.0.0.1
http://127.0.0.1/test.html
http://127.0.0.1/notfound.html
```

확인할 내용:

```text
정상 페이지 → 200 OK
없는 페이지 → 404 Not Found
```

---

## 14. 자주 발생하는 오류

### 14.1 Apache 설치가 되지 않는 경우

인터넷 연결을 확인한 뒤 다음 명령어를 다시 실행합니다.

```bash
sudo apt update
sudo apt install apache2
```

---

### 14.2 80번 포트가 닫혀 있는 경우

Apache가 실행 중인지 확인합니다.

```bash
systemctl status apache2
```

실행되어 있지 않으면 다음 명령어로 시작합니다.

```bash
sudo systemctl start apache2
```

---

### 14.3 웹 페이지가 보이지 않는 경우

먼저 로컬 주소로 접속합니다.

```text
http://127.0.0.1
```

로컬 접속이 성공하면 자신의 IP 주소로 다시 접속합니다.

```text
http://내_IP_주소
```

---

### 14.4 test.html이 보이지 않는 경우

파일이 올바른 위치에 생성되었는지 확인합니다.

```bash
ls -l /var/www/html/test.html
```

파일 내용을 확인합니다.

```bash
cat /var/www/html/test.html
```

---

### 14.5 Python 코드 실행 오류

Python 3가 설치되어 있는지 확인합니다.

```bash
python3 --version
```

현재 디렉터리에 파일이 있는지 확인합니다.

```bash
ls
```

실행 명령어를 다시 확인합니다.

```bash
python3 network_check.py
```

---

### 14.6 옆자리 학생 VM으로 ping이 안 되는 경우

다음을 확인합니다.

```text
서버 학생의 IP 주소가 맞는가?
VMware 네트워크 모드가 NAT인지 Bridged인지 확인했는가?
두 학생의 VM이 같은 네트워크 대역에 있는가?
학교 네트워크에서 학생 간 통신을 차단하고 있지는 않은가?
방화벽이 ping 요청을 차단하고 있지는 않은가?
```

해결이 어려우면 자기 VM 내부 실습으로 대체합니다.

```bash
ping 127.0.0.1
```

---

### 14.7 FTP 접속이 안 되는 경우

다음을 순서대로 확인합니다.

```bash
systemctl status vsftpd
```

vsftpd가 실행 중이 아니면 시작합니다.

```bash
sudo systemctl start vsftpd
```

21번 포트가 열려 있는지 확인합니다.

```bash
python3 network_check.py
```

그래도 접속이 안 되면 다음을 확인합니다.

```text
서버 IP 주소가 맞는가?
ping은 성공하는가?
21번 포트가 열려 있는가?
FTP 로그인 계정과 비밀번호가 맞는가?
VMware 네트워크 모드가 통신 가능한 상태인가?
학교 네트워크에서 21번 포트를 차단하고 있지는 않은가?
```

---

## 15. 최종 정리

이번 실습의 핵심은 다음과 같습니다.

```text
1. Ubuntu의 네트워크 관리는 NetworkManager가 담당한다.
2. nmcli 명령으로 네트워크 장치와 연결 상태를 확인할 수 있다.
3. 서버 프로그램이 실행되면 특정 포트가 열린다.
4. Apache 웹 서버는 기본적으로 80번 포트를 사용한다.
5. FTP 서버는 기본적으로 21번 포트를 사용한다.
6. Python socket 모듈로 포트 열림 여부를 확인할 수 있다.
7. VMware 환경에서는 네트워크 모드에 따라 다른 VM과의 통신 가능 여부가 달라진다.
8. 옆자리 학생과 통신이 안 되면 localhost를 이용한 자기 VM 내부 실습으로 대체할 수 있다.
```

마지막으로 다음 문장을 기억합니다.

> 서비스가 실행된다는 것은 특정 포트에서 네트워크 요청을 기다린다는 의미이다.
