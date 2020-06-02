---
description: Spring boot를 이용한 파일 업로드 및 파일 다운로드
---

# File Upload & File Download

Spring boot 를 이용한 HTTP File Download, File Upload 기술 구현 

파일 업로드는 Multipart를 이용하여 구현한다 여기서 Multipart란 HTTP를 통해 File을 Server로 전송하기 위한 Content-type이다  클라이언트에서 서버로 보낼때는 Multipart/form-data 형식으로 전달해야 한다.

## 환경설

```text
spring.servlet.multipart.maxFileSize=100MB
spring.servlet.multipart.maxRequestSize=100MB
spring.servlet.multipart.file-size-threshold=100MB
spring.servlet.multipart.location=${user.home}/.project/temp

file.upload=${user.home}/.project/file
```

servlet의 multipartd의 파일크기, 요청받는 파일 크기 등 요청받았을때 임시 폴더 등을 설정한다. 그리고 파일 업로드가 될때 저장될 디렉토리도 application.yml파일에 정의 한다.

```text
@RequestMapping(value = "/{scope}/upload/" , method = RequestMethod.POST)
public ResponseEntity addploadFile(@RequestBody MultipartFile file, @PathVariable("scope") String scope){
    try{
        log.info(file.getName());
        FileModel fileModel = fileService.store(file, scope);
        return ResponseEntity.ok(fileModel);
    }catch (Exception e){
        log.info(e.getMessage());
        return new ResponseEntity(HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

