# :seedling: Spring #5

### File Upload

**Setting**

* **pom.xml** 

```html
<!-- 파일업로드를 위해서... -->
<dependency>
	<groupId>commons-fileupload</groupId>
	<artifactId>commons-fileupload</artifactId>
	<version>1.4</version>
</dependency>
```

Apache commons FileUpload 의존성 추가



* **servlet-context.xml**

```html
<beans:bean class="org.springframework.web.multipart.commons.CommonsMultipartResolver" 
		id="multipartResolver">
	<beans:property name="defaultEncoding" value="UTF-8"/>
	<!-- 용량 작성 단위는 바이트 단위를 하겠다. -->
	<beans:property name="maxUploadSize" value="10485760"/> 
</beans:bean>
```

multipartResolver를 bean으로 등록



**CommonsMultipartResolver 속성**

* **defaultEncoding(String)** : 요청에서 사용할 기본 문자 인코딩 방식을 설정합니다. (기본값 : ISO-8859-1)
* **maxUploadSize(long)** : 업로드 가능 최대 크기(바이트)를 설정합니다. (-1은 무제한)
* **maxInMemorySize(int)** : 디스크에 쓰기 전에 메모리에 허용되는 최대 크기(바이트)를 설정합니다.
* **maxUploadSizePerFile(long)** : 파일 당 최대 크기를 설정합니다.





### Form

:open_file_folder: **views**

└ :page_facing_up: **regist.jsp**

```html
<body>
	<h2>파일 업로드</h2>
	<form action="upload" method="POST" enctype="multipart/form-data">
		<input type="file" name="upload_file">
		<input type="submit" value="파일등록">
	</form>
	
	<!-- 여러개의 파일을 동시에 올려보자!!! -->
	<form action="upload2" method="POST" enctype="multipart/form-data">
		<input type="file" name="upload_files" multiple="multiple">
		<input type="submit" value="파일등록">
	</form>
</body>
```

:package: **com.ssafy.mvc.controller**

└ :page_facing_up: **MainController.java**

```java
@Autowired
private ServletContext servletContext;

@PostMapping("upload")
public String upload(MultipartFile upload_file, Model model) {
	// 실제 저장될 위치 가져와
	String uploadPath = servletContext.getRealPath("/upload");
	// 만약에 등록하려고 하는 upload 폴더가 없을 수도 있다.
	File folder = new File(uploadPath);
	if (!folder.exists())
		folder.mkdir(); // 폴더 없으면 만들어
	// 실제 파일이름 가져와
	String fileName = upload_file.getOriginalFilename();
	File target = new File(uploadPath, fileName);
    
	// 파일을 해당 폴더에 저장을 하자.
	// 저장방법은 크게 2가지
	// 1.FileCopyUtiles
	// 2.File인스턴스를 이용
	try {
		FileCopyUtils.copy(upload_file.getBytes(), target);
	} catch (IOException e) {
		e.printStackTrace();
	}
		model.addAttribute("fileName", fileName);
	return "result";
}

@PostMapping("upload2")
public String upload2(MultipartFile[] upload_files, Model model) throws IOException {
	// 파일들의 이름을 저장할 리스트를 생성하자 (임시)
	List<String> list = new ArrayList<>();
	// 널이 아니면 이렇게 사전에 작업을 해주는게 사실 조금더 안전함.
	if (upload_files != null) {
		Resource res = resLoader.getResource("resources/upload");
		if (!res.getFile().exists())
			res.getFile().mkdir();

		// 지금은 단순히 upload라고 하는 폴더에 파일을 그대로 저장하고 있다.
		// 폴더 구조 2023/04/18 구조를 잡고 저장하는 것이 조금더 있어보인다.
		// 파일 이름도 실제로 저장할 때는 OriginName / 저장할 이름 두가지로 나누어서 저장을 하는것도 하나의 바업ㅂ이다.
		// 같은 이름을 사용하면 덮어버리기 때문에...

		for (MultipartFile mfile : upload_files) {
			// 조금 더 커팅해보면
			if (mfile.getSize() > 0) { // 파일이 있으면...
				mfile.transferTo(new File(res.getFile(), mfile.getOriginalFilename()));
				list.add(mfile.getOriginalFilename());
			}
		}
	}

	model.addAttribute("list", list);
	return "result";
}
```



### File DownLoad

**setting**

* **servlet-context.xml**

```html
<beans:bean class="com.ssafy.mvc.view.FileDownLoadView" id="fileDownLoadView"/>
<beans:bean class="org.springframework.web.servlet.view.BeanNameViewResolver">
	<beans:property name="order" value="0"/>
</beans:bean>
```

FileDownLoadView를 bean으로 등록

Bean이름으로 뷰를 찾기 위해 BeanNameViewResolver 등록



### **Form**

:package: **com.ssafy.mvc.view**

└ :page_facing_up: **FileDownLoadView.java**

```java
public class FileDownLoadView extends AbstractView {

	public FileDownLoadView() {
		setContentType("application/download; charset=UTF-8");
	}
	
	@Override
	protected void renderMergedOutputModel(Map<String, Object> model, HttpServletRequest request, HttpServletResponse response) throws Exception {
		//////////////////////////////////////////////////////////////////////
		ServletContext ctx = getServletContext();
		String realPath = ctx.getRealPath("/upload");
		
		//우리가 다운로드 받을 파일의 경로가 다르면 위의 코드도 달라져야 함.
		// 전송받은 모델(파일 정보)
		Map<String, Object> fileInfo = (Map<String, Object>) model.get("downloadFile"); 
        String fileName = (String) fileInfo.get("fileName");    // 파일 경로
        
        System.out.println(fileName);
        File file = new File(realPath, fileName);
		/////////////////////////////////////////////////////////////////////
        
        response.setContentType(getContentType());
        response.setContentLength((int) file.length());
        
        String header = request.getHeader("User-Agent");
        boolean isIE = header.indexOf("MSIE") > -1 || header.indexOf("Trident") > -1;
        String filename = null;
        // IE는 다르게 처리
        if (isIE) {
        	filename = URLEncoder.encode(fileName, "UTF-8").replaceAll("\\+", "%20");
        } else {
            filename = new String(fileName.getBytes("UTF-8"), "ISO-8859-1");
        }
        response.setHeader("Content-Disposition", "attachment; filename=\"" + filename + "\";");
        response.setHeader("Content-Transfer-Encoding", "binary");
        
        OutputStream out = response.getOutputStream();
        FileInputStream fis = null;
        try {
            fis = new FileInputStream(file);
            FileCopyUtils.copy(fis, out);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if(fis != null) {
                try { 
                    fis.close(); 
                }catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        out.flush();
    }
}
```

:open_file_folder: **views**

└ :page_facing_up: **result.jsp**

```html
<body>
	<a href="/mvc/upload/${fileName}">${fileName}</a>
	<!-- 컨택스트 루트를 직접 적는건 굉장히 별로.... -->
	<img src="${pageContext.request.contextPath}/upload/${fileName}"/>
	<a href="download?fileName=${fileName}">${fileName}다운로드</a>
	
	
	<c:forEach items="${list}" var="fileName">
		${fileName} <br>
	</c:forEach>
</body>
```

:package: **com.ssafy.mvc.controller**

└ :page_facing_up: **MainController.java**

```java
// resource 경로를 가져오기 위해서 사용한다.
@Autowired
private ResourceLoader resLoader;

@GetMapping("download")
public String download(Model model, String fileName) {
	Map<String, Object> fileInfo = new HashMap<>();
	fileInfo.put("fileName", fileName);
	// 폴더 구조이름
	// 원래 파일 이름
	// 지금 파일 이름 .... 등등을 잔뜩 넣어서 downLoadView에서 컨츄롤을 해라
	
	model.addAttribute("downloadFile", fileInfo);
	return "fileDownLoadView";
}
```





