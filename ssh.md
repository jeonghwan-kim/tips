# 원격로그인(SSH) 접속  

## 비밀번호 없이 로그인

공개키를 사용하면 비밀번호 없이 서버에 원격로그인 할 수 있습니다.
편리해서 가장 많이 사용하는 방법이기도 합니다.

서버와 클라이언트 양 측에서 설정 작업을 해야 합니다.

### 클라이언트

공개키를 생성합니다.

```
$ ssh-kengen -t rsa
```

생성시 물어보는 질문에 계속 엔터키를 누르면 홈 폴더의 `.ssh`폴더에 공개키와 비밀키 파일이 생성됩니다.

* `id_rsa`: 비밀키
* `id_rsa.pub`: 공개키

공개키인 `id_rsa.pub`를 서버로 보냅니다.
서버로 파일을 보내는 방법은 두 가지가 있습니다.

* ssh-copy-id
* scp

ssh-copy-id는 로컬에 저장된 모든 공개키를 서버로 자동으로 전송합니다.

```
$ ssh-copy-id user@hostname
```

그러나 ssh-copy-id는 22번 포트만 사용합니다.
서버의 ssh 데몬이 22번 포트를 사용하지 않는다면 공개키를 보낼수 없습니다.
그럴 경우는 scp 명령어를 사용해야 합니다.

```
$ scp -P 3000 ~/.ssh/id_rsa.pub user@hostname:/id_rsa.pub
```

### 서버

클라이언트에서 scp 명령어로 보낸 공개키는 홈폴더의 `id_rsa.pub` 파일로 생성되었습니다.
서버는 ~/.ssh/authorized_keys 파일에 공개키를 저장합니다.
만약 서버에 이 파일이 없다면 새로 생성한 뒤, `id_rsa.pub`의 내용을 추가합니다.

```
$ touch ~/.ssh/authorized_keys
$ cat ~/id_rsa.pub >> ~/.ssh/authorized_keys
```

파일의 권한을 수정합니다.

```
$ sudo chown 0700 ~/.ssh
$ sudo chown 600 ~/.ssh/authorized_keys
```

데몬 설정파일 `/etc/ssh/sshd_config`을 열어 아래 항목들의 주석을 제거하거나 없는 경우 추가합니다.

```
RSAAuthentication       yes
PubkeyAuthentication    yes
AuthorizedKeysFile      .ssh/authorized_keys
PasswordAuthentication  no
```

sshd 데몬을 재구동합니다.

```
$ sudo service sshd restart
```

클라이언트와 서버작업을 모두 마쳤습니다.
이제 클라이언트에서는 비밀번호없이 아래 명령어로 원격로그인을 할수 있습니다.

```
$ ssh host@username
```


## 비밀번호로 로그인

비밀번호 로그인할 경우

```
$ ssh -o IdentitiesOnly=true user@hostname
```

만약 접속되지 않을 경우 서버 데몬 설정파일 `/etc/ssh/sshd_config`을 변경합니다.

```
PasswordAuthentication  no
```

sshd 재구동 후 로그인을 시도합니다.


## 포트번호 지정

기본 포트 22번이 아닌 3000번 등의 포트를 사용할 경우

```
$ ssh -p 3000 user@hostname
```

## 별명

클라이언트에서 서버로 접속시 클라이언트의 ~/.ssh/config 파일을 이용하여 쉽게 서버에 접속할 수 있습니다.
아래는 ~/.ssh/config의 내용입니다.

```
Host foo
  Hostname hostname
  Port 22
  User user
```

커맨드라인에서 아래 명령어를 입력해보세요.

```
$ ssh foo
```

ssh는 config 파일을 참고하여

```
$ ssh -p 22 user@hostname
```

을 자동으로 실행하여 서버에 접속 시도할 것입니다.


## 문제 해결

### ssh 접속시 인증 시도 실패시

에러 메세지: 

> Too many authentication failures for username

~/.ssh 폴더에 저장된 인증키 정보를 가지고 너무 많이 시도하면 발생한다.
키파일만 사용하겠다는 옵션(`IdentitiesOnly=yes`)을 추가하여 해결할 수 있다.

```
ssh ubuntu@hostname -i -o IdentitiesOnly=yes -i key_file_path
```

### ssh 접속시 ECDSA host key 관려 오류 

에러 메세지: 

> Warning: the ECDSA host key for 'myserver' differs from the key for the IP address '192.168.123.456'

로컬 캐쉬를 제거하고 재시도한다.

```
$ ssh-keygen -R 192.168.123.456
```
