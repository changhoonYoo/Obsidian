```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.io.Resource;
import org.springframework.core.io.UrlResource;
import org.springframework.http.HttpHeaders;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import java.io.IOException;
import java.nio.file.Path;
import java.nio.file.Paths;

@RestController
@RequestMapping("/api/files")
public class FileUploadController {

    @Value("${upload.directory}")
    private String uploadDirectory;

    @PostMapping("/upload")
    public ResponseEntity<String> handleFileUpload(@RequestParam("file") MultipartFile file) {
        if (file.isEmpty()) {
            return ResponseEntity.badRequest().body("업로드된 파일이 비어있습니다.");
        }

        try {
            // 업로드된 파일을 지정된 디렉터리에 저장
            String fileName = file.getOriginalFilename();
            Path filePath = Paths.get(uploadDirectory, fileName);
            file.transferTo(filePath.toFile());

            return ResponseEntity.ok("파일 업로드 성공: " + fileName);
        } catch (IOException e) {
            return ResponseEntity.badRequest().body("파일 업로드 실패: " + e.getMessage());
        }
    }

    @GetMapping("/download/{fileName}")
    public ResponseEntity<Resource> downloadFile(@PathVariable String fileName) {
        // 업로드된 파일의 경로를 구성
        Path filePath = Paths.get(uploadDirectory, fileName);
        Resource resource;

        try {
            // 업로드된 파일을 읽어와 Resource 객체 생성
            resource = new UrlResource(filePath.toUri());
        } catch (IOException e) {
            return ResponseEntity.badRequest().body(null);
        }

        // 파일 다운로드를 위한 응답 헤더 설정
        return ResponseEntity.ok()
                .header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=\"" + resource.getFilename() + "\"")
                .contentType(MediaType.APPLICATION_OCTET_STREAM)
                .body(resource);
    }
}
```