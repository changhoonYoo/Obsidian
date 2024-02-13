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

### <span style="color:#87CEEB">tail</span>

기본적으로 `tail` 명령어는 지정된 파일의 마지막 10줄을 출력합니다. 그러나 옵션을 사용하여 출력하는 줄 수나 다른 동작을 변경할 수 있습니다.

가장 일반적으로 사용되는 옵션은 다음과 같습니다:

- `-n <숫자>`: 마지막에서 `<숫자>` 줄을 출력합니다.
- `-f`: 파일의 내용을 실시간으로 모니터링하며, 새로운 줄이 추가될 때마다 계속 출력합니다.
- `-c <숫자>`: 파일의 마지막에서 `<숫자>` 바이트를 출력합니다.
- `-q` 또는 `--quiet`: 파일 이름을 표시하지 않습니다.

예를 들어, 파일 "example.log"의 마지막 20줄을 출력하려면 다음과 같이 명령어를 사용합니다:

`tail -n 20 example.log`

또는 파일의 변경 사항을 실시간으로 모니터링하려면 다음과 같이 `-f` 옵션을 사용합니다:

`tail -f example.log`

`tail` 명령어는 로그 파일을 모니터링하거나, 파일이 계속해서 쓰여지는 경우 그 내용을 실시간으로 확인하는 등의 작업에 유용합니다.

- - - 

### <span style="color:#87CEEB">df</span>
