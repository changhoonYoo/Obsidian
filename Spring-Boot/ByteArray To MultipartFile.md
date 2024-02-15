FeignClient 를 통해 byte\[] 배열로 wav 파일을 받아오는 과정

```java
@FeignClient(value = "pbxfile-server", url = "${kr.co.exam.pbxfile.url}", configuration = {PbxfileHeaderConfig.class})
public interface PbxfileBinder {
    @RequestMapping(value = "/public/{filePath}", 
    method = RequestMethod.GET, consumes = MediaType.APPLICATION_JSON_VALUE)  
    @Headers("Content-Type: multipart/form-data")  
    byte[] getVoiceRecodeFile(@PathVariable String filePath);  
}
```

byte\[] 배열로 받아온 wav 파일을 MultipartFile로 변환하는 과정

- pom.xml

```xml
<dependency>    <!-- MockMultipartFile -->  
   <groupId>org.springframework</groupId>  
   <artifactId>spring-test</artifactId>  
</dependency>  
<dependency>   <!-- FileUtils -->  
   <groupId>commons-io</groupId>  
   <artifactId>commons-io</artifactId>  
   <version>2.11.0</version>  
</dependency>
```

	spring-test 라이브러리로 MockMultipartFile 을 사용 가능
	commons-io 를 통해 FileUtils 사용 가능

- service

```java
public MultipartFile convertByteArrayToMultipartFile(byte[] bytes) throws IOException {  
    if (bytes == null || bytes.length == 0) {  
        return null;  
    }  
    // 임시파일 생성  
    File tempFile = File.createTempFile("vms", ".wav");  
    FileUtils.writeByteArrayToFile(tempFile, bytes);  
  
    // 임시파일 MultipartFile 변환  
    return new MockMultipartFile("file", tempFile.getName(), "audio/wav", FileUtils.openInputStream(tempFile));  
}
```

```java
FileUtils.writeByteArrayToFile(tempFile, bytes);
```
코드는 `bytes` 배열의 내용을 임시 파일 `tempFile`에 기록하는 역할을 합니다. 즉, `bytes` 배열에 있는 데이터를 `tempFile`에 쓰는 것입니다. 이 코드는 Apache Commons IO 라이브러리의 `FileUtils` 클래스를 사용하여 바이트 배열을 파일로 쓰는 편리한 메서드입니다. 이렇게 하면 임시 파일에 바이너리 데이터를 저장할 수 있습니다.


```java
return new MockMultipartFile("file", tempFile.getName(), "audio/wav", FileUtils.openInputStream(tempFile));
```

위 코드에서 MockMultipartFile 생성자의 각 전달인자는 다음과 같은 역할을 합니다:

"file": MultipartFile의 이름을 나타냅니다. 이것은 파일 필드의 이름을 나타냅니다. 여기서는 "file"이라는 이름으로 파일을 전송하는 것으로 가정합니다.

tempFile.getName(): 업로드된 파일의 원래 파일 이름을 나타냅니다. 이 값은 클라이언트에서 업로드한 파일의 이름과 일치해야 합니다.

"audio/wav": 파일의 콘텐츠 타입을 나타냅니다. 이것은 업로드된 파일의 MIME 타입을 나타내며, 여기서는 WAV 오디오 파일을 나타내기 위해 "audio/wav"로 설정되었습니다.

FileUtils.openInputStream(tempFile): 업로드된 파일의 내용을 나타냅니다. 이 값은 파일의 입력 스트림을 제공하는데, MockMultipartFile 생성자는 파일의 내용을 읽어서 MultipartFile 객체로 만들기 위해 이 입력 스트림을 사용합니다. 여기서는 FileUtils.openInputStream() 메서드를 사용하여 임시 파일의 입력 스트림을 엽니다.