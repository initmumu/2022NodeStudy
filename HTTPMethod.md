# HTTP Method 란? (GET, POST, PUT, DELETE)

### HTTP Method

> 조회: GET<br>
> 등록: POST<br>
> 수정: PUT<br>
> 삭제: DELETE<br>

1. GET<br>

**정의**<br>
* GET 메소드는 주로 데이터를 읽거나(Read) 검색(Retrieve)할 때 사용되는 메소드
* GET 요청이 성공적으로 이루어진다면 XML이나 JSON과 함께 200(OK) HTTP 응답 코드를 반환
* 에러가 발생하면 주로 404(Not found) 에러나 400(Bad request) 에러가 발생 <br>

**예시**<br>
[2021-2-OSS-Project/router/chat/chat.js](https://github.com/initmumu/2021-2-OSS-Project/blob/master/router/chat/chat.js)
```javascript
router.get('/', function(req, res){
    var ip = requestIp.getClientIp(req);
    var id = req.user;
    if(!id){
        console.log(logString+'익명 유저의 채팅 접근을 거부했습니다.('+ip+')')
        res.sendFile(path.join(__dirname, "../../public/login.html"))
    }
    if(id){
        var nickname = req.user.nickname
        console.log(logString+req.user.ID+'('+nickname+') 유저가 채팅 중입니다.('+ip+')')
        res.render('chat.ejs', {'nickname':nickname})
    }
});
```

<br>
2. POST<br>

**정의**<br>

* POST 메소드는 주로 새로운 리소스를 생성(create)할 때 사용
* 조금 더 구체적으로 POST는 하위 리소스(부모 리소스의 하위 리소스)들을 생성하는데 사용
* 성공적으로 creation을 완료하면 201(Created) HTTP 응답을 반환 <br>

**예시**<br>
[2021-2-OSS-Project/router/profile/index.js](https://github.com/initmumu/2021-2-OSS-Project/blob/master/router/profile/index.js)
```javascript
router.post('/upload', upload.single('userfile'), function(req,res){
    var ip = requestIp.getClientIp(req);
    try{
        sharp(req.file.path)  // 압축할 이미지 경로
        .resize({width:500, height:500, position:"left top"})
        .withMetadata()	// 이미지의 exif데이터 유지
        .toBuffer((err, buffer) => {
          if (err) throw err;
          // 압축된 파일 새로 저장(덮어씌우기)
          fs.writeFile(req.file.path, buffer, (err) => {
            if (err) throw err;
          });
        });
        var id = req.user.ID;
        var ip = requestIp.getClientIp(req);
        var profilepic = req.file.filename;
        var datas = [profilepic, id]

        var picName = profilepic.substr(15)
        var sql = "update userdb set profilepic =? where id =?"
        myinfo.query(sql,datas,function(err,result){
            if(err) console.error(err)
            console.log(logString+req.user.ID+'('+req.user.nickname+') 유저가 프로필 사진을 업로드했습니다.(파일명: '+picName+' // '+ip+')')
            res.send("<script>alert('업로드가 완료되었습니다.');window.close();</script>");
        })
    }
    catch{
        if(!id){
            console.log(logString+'익명 유저의 프로필 사진 업로드 시도를 거부했습니다.('+ip+')')
            res.send("<script>alert('로그인이 필요합니다.');opener.location.href='/login';window.close();</script>");
        }
        else{
            console.log(logString+req.user.ID+'('+req.user.nickname+') 유저가 파일 업로드 없이 업로드를 시도했습니다.('+ip+')')
            res.send("<script>alert('파일을 업로드 해주세요.');history.back();</script>");
        }
    }
}
```
[2021-2-OSS-Project/views/uploadprof.ejs](https://github.com/initmumu/2021-2-OSS-Project/blob/master/views/uploadprof.ejs)
```HTML
    <form action = 'upload' method = 'post' enctype="multipart/form-data">
        <input type='file' name='userfile'>
        <input type='submit'>
    </form>
```
<br>
3. PUT<br>

**정의**<br>
* PUT는 리소스를 생성 / 업데이트하기 위해 서버로 데이터를 보내는 데 사용 <br>

4. DELETE<br>

**정의**<br>
* DELETE 메서드는 지정된 리소스를 삭제 <br>

