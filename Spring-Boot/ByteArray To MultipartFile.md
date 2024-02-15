FeignClient 로 wav 파일을 받아오는 과정
```java
@FeignClient(value = "pbxfile-server", url = "${kr.co.exam.pbxfile.url}", configuration = {PbxfileHeaderConfig.class})
public interface PbxfileBinder {
    @RequestMapping(value = "/public/{filePath}", 
    method = RequestMethod.GET, consumes = MediaType.APPLICATION_JSON_VALUE)  
    @Headers("Content-Type: multipart/form-data")  
    byte[] getVoiceRecodeFile(@PathVariable String filePath);  
}
```
