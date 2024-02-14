![[Pasted image 20240213094227.png]]

### <span style="color:#87CEEB">ls</span>

list segments의 약자로 현재 디렉터리의 파일과 디렉터리를 보여준다.

- ls -l : 파일들의 상세 정보 보여줌
- ls -a : 숨김 파일 표실
- ls -t : 최신 파일부터 표시
- ls -rt : 오래된 파일부터 표시
- ls -F : 파일을 표시할 때 파일의 타입을 나타내는 문자열 표시 ( / 디렉터리, * 실행파일, @심볼릭 링크)
- ls -R : 하위 디렉터리의 내용까지 표시

심볼릭 링크(symbolic link) : 원본 파일을 가리키도록 링크만 연결시켜둔 것, 윈도우의 바로가기 링크와 같은 개념
- - -
cd 

charge directory의 약자로 디렉터리 이동 명령어
- cd ~ : 홈 디렉터리로 이동
- cd .. : 상위 디렉터리로 이동, cd ../../ 같은 식으로 여러 단계를 한 번에 이동 가능
- cd /dir : 절대 경로를 지정해 이동
- cd - : 바로 전의 디렉터리로 이동
- - -
### <span style="color:#87CEEB">mkdir</span>

make directory의 약자로 디렉터리를 만들 때 사용
- - -
### <span style="color:#87CEEB">cp</span>

copy의 약자로 파일 또는 디렉터리 복사할 때 사용

```bash
# source를 target으로 복사
$ cp source target

# target 파일이 이미 있는 경우 덮어쓰기
$ cp -f source target

# 디렉터리를 복사할 때 사용, 하위 디렉터리도 모두 복사
$ cp -R sourceDir targetDir
```

- - -

### <span style="color:#87CEEB">mv</span>

move의 약자, 파일 또는 디렉터리의 위치를 옮기거나 이름 변경에 사용

```bash
# afile 이름을 bfile로 변경
$ mv afile bfile

# afile 을 상위 디렉터리로 옮김
$ mv afile ../

# afile 을 /opt 이하 디렉터리로 옮김
$ mv afile /opt/
```

- - -
###<span style="color:#87CEEB">rm</span>

remove의 약자, 파일 또는 디렉터리 삭제

```bash
# afile 삭제
$ rm afile

# 디렉터리 adir을 삭제, 삭제 시 확인
$ rm -r adir

# 디렉터리 adir을 삭제, 삭제 시 확인 안함
$ rm -rf adir

# txt로 끝나는 모든 파일을 삭제할지 물어보면서 삭제
$ rm -i *.txt
```

- - -

### <span style="color:#87CEEB">cat</span>

catenate (잇다, 연결하다)의 약자, 파일의 내용을 확인할 때 사용

```bash
# test.txt 파일의 내용을 확인
$ cat test.txt
```

---

### <span style="color:#87CEEB">touch</span>

touch는 빈 파일을 생성, 혹은 파일의 날짜와 시간을 수정할 때 사용

```bash
# afile 생성
$ touch afile

# afile의 시간을 현재 시간으로 갱신
$ touch -c afile

# bfile의 날짜 정보를 afile의 정보와 동일하게 변경
$ touch -r afile bfile
```

---


<span style="color:#87CEEB">echo</span>

echo는  어떤 문자열을 화면에 보여줄 때 사용, echo와 리다이렉션을 사용해 파일을 생성, 추가하는 작업을 많이 함

```bash
# hellowworld 출력
$ echo `hellowworld`

# 패스로 지정한 문자열을 출력
$ echo $PATH

# 이스케이프 문자열을 해석
$ echo -e 문자열

# 개행을 표시할 수 있음
$ echo -e "안녕하세요\n이렇게 하면 \n새 줄이 생겨요"

# ls와 유사하게 현재 디렉터리의 파일과 폴더를 출력
$ echo *

# 리다이렉션 '>'을 사용해 hello.txt 파일 생성. 파일 내용에는 echo로 표시되는 내용이 들어감
$ echo hellow redirection > hello.txt

# 추가 연산자 >> 를 사용해 기존 파일에 문자열 추가
$ echo hello2 >> hello.txt
```

---

### <span style="color:#87CEEB">ip addr/ ifconfig</span>

접속한 리눅스의 IP 정보를 알아낼 때 사용

ip addr이 설치되어 있지 않은 경우에는 ifconfig를 사용

---

### <span style="color:#87CEEB">ss</span>

socket statistics의 약자, 네트워크 상태를 확인하는 데 사용. 원래는 netstat을 사용했는데, 최근에는 ss를 주로 사용

- ss -a : 모든 포트 확인
- ss -t : TCP 포트 확인
- ss -u : UDP 포트 확인
- ss -l : LISTEN 상태 포트 확인
- ss -p : 프로세스 표시
- ss -n : 호스트, 포트, 사용자명을 숫자로 표시

TCP 포트 중 LISTEN 상태인 포트의 번호를 알고 싶을 때
```bash
$ ss -tln
LISTEN 0 511 *:433 *:*
LISTEN 0 1 127.0.0.1:8006 *:*
LISTEN 0 511 *:80 *:*
```

---

### nc

netcat의 약자, 예전에는 포트가 열렸는지 확인하는 데 telnet 명령어를 사용했지만 요즘은 주로 nc를 사용

```bash

# 포트가 오픈됐는지 확인
$ nc IP주소 포트

# 더 자세한 정보가 남음
$ nc -v IP주소 포트

# 현재 서버의 포트를 오픈(방화벽에 해당 포트 번호가 설정 함)
$ nc -l 포트
```

---

### <span style="color:#87CEEB">which, whereis, locate</span>

which는 특정 명령어의 위치를 찾아줌

```bash

$ which git
/usr/local/bin/git

# which -a : 검색 가능한 모든 경로에서 명령어를 찾아줌
$ which -a git
/usr/local/bin/git
/usr/bin/git

# where : which -a 와 같음
$ where git
/usr/local/bin/git
/usr/bin/git

# whereis는 실행 파일, 소스, man 페이지의 파일을 찾아줌
$ whereis ssh
ssh: /usr/bin/ssh /usr/share/man/man1/ssh.1

# locate는 파일명을 패턴으로 빠르게 찾아줌
# 아래 예제는 .java 파일을 찾아주는 명령어
$ locate *.java
```

---

### <span style="color:#87CEEB">tail</span>

tail은 파일의 마지막 부분을 보여줌, head도 있는데 사용법은 같다
서버의 실시간 로그를 확인할 때 자주 사용

```bash
# 파일의 마지막 라인부터 숫자만큼의 라인 수를 보여주기
$ tail -n {숫자} {파일경로}

# 숫자로 지정한 라인부터 보여주기
$ tail -n +{숫자} {파일경로}

# 파일의 마지막 라인부터 숫자로 지정한 바이트 수 만큼 보여주기
$ tail -c {숫자} {파일경로}

# Ctrl + c로 중단하기 전까지 지정한 파일의 마지막에 라인이 추가되면 계속 출력
$ tail -f {파일경로} : 

# 파일의 마지막 라인부터 지정한 숫자만큼을 
# {초}로 지정한 시간이 지날 때마다 리프레시해서 보여주기
$ tail -n {숫자} -s {초} -f {파일경로}
```

- - - 

### <span style="color:#87CEEB">find</span>

find 명령어는 파일이나 디렉터리를 찾는 데 사용하는 명령어

```bash
# 확장자명으로 찾기
$ find {디렉터리} -name '*.bak'

# 디렉터리를 지정해 찾기
$ find {디렉터리} -path '**/검색 시 사용하는 디렉터리명/**.*.js'

# 파일명을 패턴으로 찾기
$ find {디렉터리} -name '*패턴*'

# 파일명을 패턴으로 찾되 특정 경로는 제외
$ find {디렉터리} -name '*.py' -not -path '*/site-packates/*'

# 파일을 찾은 다음 명령어 실행하기
$ find {디렉터리} -name '*.ext' -exec wc -l {} \;

# 최근 7일간 수정된 파일을 찾고 삭제하기
$ find {디렉터리} -daystart -mtime -7 -delete

# 0바이트인 파일을 찾고 삭제하기
$ find {디렉터리} -type f -empty -delete
```

---

### <span style="color:#87CEEB">ps</span>

process status
현재 실행 중인 프로세스 목록과 상태

- `-e`: 시스템에서 실행 중인 모든 프로세스를 출력합니다.
- `-f`: 상세한 정보를 출력합니다. 이 옵션을 사용하면 프로세스의 전체 명령줄을 포함하여 자세한 정보가 표시됩니다.
- `-u`: 현재 사용자와 관련된 프로세스만 출력합니다.
- `-x`: 시스템 프로세스 외에도 유휴 프로세스도 출력합니다.
- `-a`: 터미널과 관계없이 모든 프로세스를 출력합니다.

```bash
# 현재 실행중인 프로세스 상세 출력
$ ps -ef

# 실행 중인 모든 프로세스 보여주기
$ ps aux

# 실행 중인 모든 프로세스를 전체 커맨드를 포함해 보여주기
$ ps auxww

# 특정 문자열과 매칭되는 프로세스 찾기
$ ps aux | grep {패턴}

# 메모리 사용량에 따라 정렬하기
$ ps --sort size
```

---

### <span style="color:#87CEEB">grep</span>

grep은 입력에서 패턴에 매칭되는 내용을 찾는 명령어.
grep이라는 이름은 ed의 명령어인 g/re/p(내용 전체를 정규식으로 찾은 다음 프린트하라) 
보통 find, ps 등과 조합해 사용

```bash
# 파일에서 특정 패턴을 만족하는 부분 찾기
$ grep "패턴" 파일경로

# 파일명과 라인을 함께 표시하기
$ grep --with-filename --line-number "패턴" 파일경로

# 매칭하지 않는 부분 표시하기
$ grep --invert-match "패턴"

# cat과 함께 사용하기
$ cat 파일경로 | grep "패턴"
```

---

### <span style="color:#87CEEB">kill</span>

프로세스를 죽이는 명령어. SIGKILL, SIGSTOP 은 강제종료 나머지는 정상종료

```bash
# kill에서 사용할 수 있는 시그널 표시
$ kill -l

# 프로세스 죽이기 
$ kill 프로세스ID

# 백드라운드 잡 종료시키기
$ kill {잡ID}

# 프로세스 강제 종료
$ kill -9 | KILL 프로세스 ID
```

---

### <span style="color:#87CEEB">alias</span>

자주 사용하는 명령어를 alias를 사용해 줄일 수 있음

```bash
# 모든 alias 표시
$ alias

# alias 만들기
# 예) alias ll="ls =al"
$ alias 단어="명령"

# alias 삭제
$ unalias 단어
```

---

### <span style="color:#87CEEB">vi / vim</span>

vi 혹은 vim은 대부분의 리눅스에 기본적으로 설치되어 있는 텍스트 에디터

1. **파일 열기:**
    
	```bash
	vi filename
	```
    
2. **텍스트 입력 및 편집:**
    
    - `i`: 현재 커서 위치에서 입력 모드로 변경합니다.
    - `a`: 현재 커서 다음 위치에서 입력 모드로 변경합니다.
    - `o`: 현재 줄 다음에 새로운 빈 줄을 만들고 입력 모드로 변경합니다.
3. **텍스트 저장 및 종료:**
    
    - `Esc`: 입력 모드를 종료하고 명령 모드로 전환합니다.
    - `:w`: 현재 편집 중인 파일을 저장합니다.
    - `:q`: vi 편집기를 종료합니다. 만약 변경 사항이 저장되지 않았다면 종료되지 않습니다.
    - `:q!`: 변경 사항을 저장하지 않고 vi 편집기를 강제로 종료합니다.
    - `:wq` 또는 `:x`: 변경 사항을 저장하고 vi 편집기를 종료합니다.
4. **커서 이동:**
    
    - `h`: 왼쪽으로 커서 이동
    - `j`: 아래로 커서 이동
    - `k`: 위로 커서 이동
    - `l`: 오른쪽으로 커서 이동
5. **삭제 및 복사 붙여넣기:**
    
    - `x`: 현재 커서 위치의 글자를 삭제합니다.
    - `dd`: 현재 줄을 삭제합니다.
    - `yy`: 현재 줄을 복사합니다.
    - `p`: 현재 커서 위치 다음에 복사된 내용을 붙여넣습니다.

---

### <span style="color:#87CEEB">netstat</span>

`netstat` 명령어는 네트워크 통계를 표시하거나 현재 활성화된 네트워크 연결을 확인하는 데 사용됩니다. 주요 네트워크 정보를 조회하고 관리하는 데 도움이 됩니다.

`netstat` 명령어는 다양한 옵션과 함께 사용될 수 있으며, 주요 옵션은 다음과 같습니다:

- `-a` 또는 `--all`: 모든 소켓을 표시합니다. 리스닝과 비리스닝 상태의 모든 연결을 표시합니다.
- `-t` 또는 `--tcp`: TCP 프로토콜에 대한 정보만 표시합니다.
- `-u` 또는 `--udp`: UDP 프로토콜에 대한 정보만 표시합니다.
- `-n` 또는 `--numeric`: 주소와 포트 번호를 숫자로 표시합니다. 호스트명과 서비스명을 확인하지 않습니다.
- `-p` 또는 `--program`: 연결된 프로세스의 PID와 프로그램 이름을 표시합니다.
- `-l` 또는 `--listening`: 리스닝 상태인 소켓만 표시합니다. 대기중인 연결을 포함합니다.
- `-s` 또는 `--statistics`: 네트워크 통계를 요약하여 표시합니다.
- `-r` 또는 `--route`: 라우팅 테이블을 표시합니다.

예를 들어, `netstat -tuln` 명령어는 TCP와 UDP 연결에 대한 포트 번호와 주소를 숫자로 표시하고, 리스닝 상태인 모든 소켓을 표시합니다.

`netstat` 명령어는 네트워크 문제를 진단하고 해결하는 데 유용하며, 시스템 및 네트워크 관리자가 네트워크 상태를 모니터링하는 데에도 사용됩니다.

---

### <span style="color:#87CEEB">lsof</span>

`lsof`는 "List Open Files"의 약자로, 현재 시스템에서 열려 있는 파일과 네트워크 연결에 대한 정보를 표시하는 명령어입니다. 이 명령어는 다음과 같은 다양한 용도로 사용됩니다:

1. **파일 열린 상태 확인**: 시스템에서 현재 열려 있는 파일 목록을 표시합니다. 이는 파일 디스크립터를 통해 파일을 식별하고 해당 파일을 열고 있는 프로세스에 대한 정보를 제공합니다.
    
2. **네트워크 연결 확인**: 현재 활성화된 네트워크 연결과 관련된 정보를 제공합니다. 이는 네트워크 소켓과 해당 소켓을 열고 있는 프로세스에 대한 정보를 포함합니다.
    
3. **프로세스와 파일 시스템 간의 관계 확인**: 특정 파일을 사용하는 프로세스나 파일 시스템과 관련된 정보를 제공합니다. 이를 통해 파일이나 디렉토리를 열고 있는 프로세스를 확인할 수 있습니다.
    

`lsof` 명령어는 다양한 옵션과 함께 사용될 수 있으며, 시스템 관리, 네트워크 문제 해결, 보안 감사 등 다양한 용도로 활용됩니다. 

`lsof` 명령어를 사용할 때 자주 사용되는 옵션들은 다음과 같습니다:

1. **`-i`**: 네트워크 소켓 정보를 표시합니다.
2. **`-n`**: 주소를 숫자로 표시합니다. 호스트명이나 서비스명 대신 IP 주소나 포트 번호로 표시됩니다.
3. **`-p`**: 지정된 프로세스 ID (PID)에 대한 정보만 표시합니다.
4. **`-u`**: 사용자 이름 대신 UID를 표시합니다.
5. **`-c`**: 지정된 프로세스 이름에 대한 정보만 표시합니다.
6. **`-t`**: 지정된 파일 시스템 태그에 대한 정보만 표시합니다.
7. **`-d`**: 지정된 파일 디스크립터에 대한 정보만 표시합니다.
8. **`-a`**: 모든 파일 시스템에 대한 정보를 표시합니다.
9. **`-b`**: 특정 파일 시스템에 대한 정보만 표시합니다.
10. **`-T`**: 파일 유형에 따라 필터링하여 표시합니다.

이러한 옵션들은 `lsof` 명령어를 사용하여 특정한 정보를 필터링하거나 조회할 때 유용하게 사용됩니다. 필요에 따라 이러한 옵션들을 조합하여 원하는 정보를 얻을 수 있습니다.

---

