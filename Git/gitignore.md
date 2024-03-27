
`.gitignore` 파일은 Git으로 추적하지 않을 파일이나 폴더를 지정하는 데 사용됩니다. 이 파일은 프로젝트 루트 디렉토리에 위치하며, Git이 해당 파일에 명시된 패턴에 일치하는 파일을 무시하도록 지시합니다.

```bash
# 이 파일은 주석으로 사용되며 무시됩니다. 

# 특정 파일 무시 
filename.txt 

# 특정 확장자 무시 
*.log 

# 특정 디렉토리 무시 
/logs/ 

# 모든 디렉토리의 .tmp 확장자 파일 무시
/*.tmp 

# 특정 디렉토리 하위의 특정 파일 무시 
dir/subdir/file.txt 

# 특정 패턴을 무시하지 않도록 설정 
!important.txt
```


[gitignore.io](https://www.toptal.com/developers/gitignore)
