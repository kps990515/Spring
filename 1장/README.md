## 1장 - Get/Post/Put/Delete

Json으로 받는 경우
req > Object Mapper(json to Object) > Object > 
method > Object > ObejectMapper(Object to json) > response

### Get
```java
    @Getmapping(path = "/hello")
    public String hello(){
        return "hello";
    }    

    @RequestMapping(path = "/hi", method = RequestMethod.GET)
    public String hello(){
        return "hello";
    }

    //https://naver.com/{id}
    @Getmapping("/path-variables/{id}")
    public String hello(@PathVariable(name = "id") String pathName){
        return pathName;
    }  
    
    //https://naver.com/name=abc&email=ddd@naver.com
    @Getmapping(path = "query-param")
    public String hello(@RequestParam Map<String, String> queryParam){
        queryParam.entrySet().foreach(...);
    }  

    @Getmapping("query-param2")
    public String hello(@RequestParam String name, @RequestParam String email){
        return name + email;
    }     
```

### POST
```java
    @Postmapping("/post")
    public void post(@ReqeustBody Map<String, Object> reqData){
        reqData.forEach((key, value) -> {
            ...
        });
    }    

    @Postmapping("/post")
    public void post(@ReqeustBody RequestVO requestVO){
        return requestVO;
    }   
```

### PUT
```java
    @Postmapping("/put")
    public void put(@ReqeustBody RequestVO requestVO){
        return requestVO;
    }   
```

### DELETE
```java
    @Deletemapping("/delete/{userId}")
    public String delete(@PathVariable String userId, @RequestParam String account){
        return pathName;
    }    
```

### ObjectMapper
```java
    var objectMapper = new ObjectMApper();
    
    // object > String
    var user = new User("Steve", 10);
    var text = objectMapper.writeValueAsString(user);
    
    // text > Object
    // objectMapper는 default생성자 필요
    var objectUser = objectMapper.readValue(text, User.class);
```
