Linux Tips
==========
리눅스 명령어 사용법과 팁을 정리한 문서. (알파벳순 오름차순)

## ab
ab 테스트

```
ab -r -n 100000 -c 1000 <url>
```

[참고](http://zgadzaj.com/benchmarking-nodejs-basic-performance-tests-against-apache-php)


# curl

파일 다운로드:

```
curl -O url
```

## du
현재 폴더의 용량 계산:

```
du -csh ./
```


## find
파일 내용 검색

php 파일만 검색:

```
find ./ -name "*.php"
```

php 파일 내용중 "new" 키워드 검색

```
find ./ -name "*.php" | xargs grep "new"
```

## ftp
root 계정 활성화: `/etc/ftpusers` 파일에서 root를 주석처리

## netstat
열린 포트확인:

```
netstat -an | grep "LISTEN"
```

## ps

프로세스 확인:

```
pa aux
```

## rsync
로컬파일을 리모트와 싱크함

현제 폴더 싱크:

```
$ rsync -zvr --delete . root@hostname:/업로드할 경로
```

일부 폴더 제외 후 싱크:

```
$ rsync -zvr --delete --exclude .git . root@hostname:/업로드할 경로
```

[참고](http://www.joinc.co.kr/modules/moniwiki/wiki.php/Site/Tip/Rsync)


## ssh
[참고](https://github.com/jeonghwan-kim/ssh-settings)


## scp

원격지로 로컬 파일 이동

포트 설정하여 전송:

```
scp -P 3000 ~/.ssh/id_rsa.pub user@hostname:/id_rsa.pub
```

폴더 전송:

```
scp -r ./local-folder user@hostname:/remote-folder
```

리모트 서버가 비밀번호 인증일 경우 `-o IdentitiesOnly=ture`를 추가한다.

# stat
파일의 메타 정보 조회 

## tar
압축:

```
tar cvf [파일이름.tar] [압축할 파일 이름들 ...]
```

풀기:

```
tar zxvf [파일이름]
```

## top
시스템 모니터링

```
top
```

## useradd
사용자 계정 추가:

```
sudo useradd newUser
sudo passwd newUser
```

[참고](http://www.cyberciti.biz/faq/ubuntu-add-user-to-group/)


## usermod
사용자 그룹 변경:

```
sudo usermod -aG docker ${USER}
```

## 배포판 확인
데비안 계열:

```
$ cat /etc/issue
```
