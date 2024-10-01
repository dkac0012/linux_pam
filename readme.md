
# 보안 강화를 위한 PAM 설정

PAM은 애플리케이션과 서비스가 어떻게 사용자 인증을 처리할지 쉽게 구성하고 확장할 수 있도록 설계된 프레임워크로 PAM을 사용하면 다양한 인증 방법(예: 비밀번호, 생체 인식, 토큰 등)을 동일한 인터페이스로 처리할 수 있다.


## 실습환경

OS : window 10 pro

SW : Oracle VMvirtualbox, MobaXterm



## 작업과정

### IP 할당
위의 상황은 이미지를 복제하여 생성하였으므로 ip충돌이 일어날 것을 방지하여 DHCP설정을 해주었다. <br/>
설정 전의 ip는 192.168.10.4이며 설정 후 ip는 192.168.10.21로 변경하였다.<br/>

**작업 전** <br/>
myservice03 vm을 생성함.<br/><br/>
<img src="https://github.com/user-attachments/assets/de30de3e-8fa2-48d0-8143-2840e71efc17" width="650" />

**설정** 

```bash
sudo vi /etc/netplan/00-installer-config.yaml # 편집 명령어
sudo netplan apply # 설정 적용 명령어
```

```bash
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      addresses:
        - 10.0.2.21/24  # 변경된 고정 IP 주소
      routes:
        - to: default
          via: 10.0.2.1  # 게이트웨이
      nameservers:
        addresses:
          - 8.8.8.8
      dhcp4: false
```


**설정 전**
```bash
ipconfig # ip 설정 확인 명렁어
```
![image](https://github.com/user-attachments/assets/4abc44a1-7e34-482e-9c1d-90a668cbbbaa)


**설정 후**

설정은 MobaXterm에서 확인

![image](https://github.com/user-attachments/assets/e9d06e85-8944-4129-a841-daebb7791555)

### PAM

#### trouble shooting 1

```password requisite pam_pwquality.so minlen=8 ```

##### error
![image](https://github.com/user-attachments/assets/400f904a-681f-4e67-8d94-ff6018e8e2ea)


#### trouble shooting 2

linux에서 pam 프레임워크를 인식하지 못한다 생각하여 다음 코드를 추가하였습니다.

```password sufficient pam_unix.so ```

##### error

![image](https://github.com/user-attachments/assets/400f904a-681f-4e67-8d94-ff6018e8e2ea)

#### trouble shooting 3

##### 로그 분석

```tail -10 /var/log/auth.log ```

<img src="https://github.com/user-attachments/assets/5d28196d-dc3f-4dbd-9743-4a6ea7977da9" width="650" /> </br>

로그를 보아 pam설치되지 않았거나 pam이 망가졌다.


##### 재설치

```sudo apt-get install libpam-pwquality ``` <br/>
<img src="https://github.com/user-attachments/assets/1f2375c8-c977-44e5-997c-b0a858eb6827" width="650" /> <br/>

##### 결과

<img src="https://github.com/user-attachments/assets/159ed788-e7b7-4e28-8c5e-1a9547dee3ac" width="650" /> <br/>
