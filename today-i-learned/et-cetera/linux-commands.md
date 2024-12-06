---
description: >-
  회사에서는 ELK로 로그를 수집/조회하고 CI/CD가 구축되어 있다 보니 생각보다 리눅스서버에 직접 접근 할일이 줄어들었다. 오랜만에 리눅스
  사용 시  자꾸 몇몇 명령어를 까먹어 버벅 거릴 때가 있어 틈 날 때마다 보려고 명령어들을 정리해보았다.
---

# 🪄 Linux : Commands

## **Basic Commands**

| 명령어     | 설명               | 예제              |
| ------- | ---------------- | --------------- |
| `pwd`   | 현재 작업 디렉토리 경로 출력 | `pwd`           |
| `ls`    | 디렉토리 내용 확인       | `ls -l`         |
| `cd`    | 디렉토리 이동          | `cd /home/user` |
| `echo`  | 문자열 출력 또는 파일에 추가 | `echo "Hello"`  |
| `man`   | 명령어 매뉴얼 확인       | `man ls`        |
| `clear` | 터미널 화면 지우기       | `clear`         |

***

## **File and Directory Management**

| 명령어     | 설명               | 예제                            |
| ------- | ---------------- | ----------------------------- |
| `touch` | 빈 파일 생성          | `touch newfile.txt`           |
| `mkdir` | 새 디렉토리 생성        | `mkdir new_folder`            |
| `rm`    | 파일/디렉토리 삭제       | `rm file.txt`, `rm -r folder` |
| `cp`    | 파일 복사            | `cp file1.txt file2.txt`      |
| `mv`    | 파일 이동 또는 이름 변경   | `mv oldname.txt newname.txt`  |
| `find`  | 파일 검색            | `find / -name file.txt`       |
| `cat`   | 파일 내용 출력         | `cat file.txt`                |
| `less`  | 파일 내용 페이지 단위로 보기 | `less file.txt`               |
| `head`  | 파일의 첫 몇 줄 출력     | `head -n 10 file.txt`         |
| `tail`  | 파일의 마지막 몇 줄 출력   | `tail -n 10 file.txt`         |

***

## **User and Permission Management**

| 명령어      | 설명                | 예제                    |
| -------- | ----------------- | --------------------- |
| `whoami` | 현재 로그인된 사용자 이름 출력 | `whoami`              |
| `id`     | 사용자 및 그룹 정보 확인    | `id`                  |
| `chmod`  | 파일 권한 변경          | `chmod 755 file.txt`  |
| `chown`  | 파일 소유자 변경         | `chown user file.txt` |
| `su`     | 다른 사용자로 전환        | `su root`             |
| `passwd` | 사용자 비밀번호 변경       | `passwd`              |

***

## **Process and System Management**

| 명령어        | 설명                   | 예제              |
| ---------- | -------------------- | --------------- |
| `ps`       | 현재 실행 중인 프로세스 목록     | `ps aux`        |
| `top`      | 실시간 프로세스 및 시스템 상태 보기 | `top`           |
| `kill`     | 특정 프로세스 종료           | `kill 12345`    |
| `df`       | 파일 시스템의 디스크 사용량 확인   | `df -h`         |
| `du`       | 디렉토리/파일 크기 확인        | `du -sh folder` |
| `uptime`   | 시스템 가동 시간 확인         | `uptime`        |
| `uname`    | 시스템 정보 출력            | `uname -a`      |
| `reboot`   | 시스템 재부팅              | `reboot`        |
| `shutdown` | 시스템 종료               | `shutdown now`  |

***

## **Networking Commands**

| 명령어        | 설명                    | 예제                             |
| ---------- | --------------------- | ------------------------------ |
| `ping`     | 네트워크 연결 상태 확인         | `ping google.com`              |
| `ifconfig` | 네트워크 인터페이스 설정 및 정보 확인 | `ifconfig`                     |
| `netstat`  | 네트워크 상태 및 포트 확인       | `netstat -tuln`                |
| `curl`     | HTTP 요청 전송 및 데이터 가져오기 | `curl http://example.com`      |
| `wget`     | 파일 다운로드               | `wget http://example.com/file` |
| `scp`      | 원격 서버 간 파일 복사         | `scp file.txt user@host:/path` |
| `ssh`      | 원격 서버 접속              | `ssh user@host`                |

***

## **Compression and File Management**

| 명령어      | 설명         | 예제                             |
| -------- | ---------- | ------------------------------ |
| `tar`    | 파일 압축 및 해제 | `tar -czf archive.tar.gz file` |
| `gzip`   | 파일 압축      | `gzip file.txt`                |
| `gunzip` | gzip 압축 해제 | `gunzip file.txt.gz`           |
| `zip`    | ZIP 압축     | `zip archive.zip file1 file2`  |
| `unzip`  | ZIP 압축 해제  | `unzip archive.zip`            |

***

## **Miscellaneous Commands**

| 명령어       | 설명                  | 예제                         |
| --------- | ------------------- | -------------------------- |
| `alias`   | 명령어 단축키 설정          | `alias ll='ls -l'`         |
| `history` | 명령어 실행 이력 확인        | `history`                  |
| `grep`    | 텍스트 검색              | `grep "keyword" file.txt`  |
| `wc`      | 텍스트의 행, 단어, 문자 수 출력 | `wc file.txt`              |
| `diff`    | 두 파일 비교             | `diff file1.txt file2.txt` |
| `sort`    | 파일 내용 정렬            | `sort file.txt`            |
