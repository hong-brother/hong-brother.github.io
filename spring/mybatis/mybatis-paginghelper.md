---
description: Paging Helper Plugin
---

# Mybatis-PagingHelper\(작성중\)

Back-end 개발을 많이 해봤지만 그동안 페이징 처리 해본적이 몇번 없었던거 같다. 그 것도 아마 신입일때만 처리해 보고 나머지는 지도 개발 아니면 Paging과 관련 없는 개발을 많이 해왔다. ㅠ 개발 짬밥을 거꾸로 먹은 느낌이랄까? 😰 간만에 페이징 로직을 다시 확인해봤는데 휴 너무나도 귀찮고 힘겨운 일이다. 그래서 이미 누군가 구현해 만들어 놓았다. 별로 어렵지 않아 따라하면 순삭일듯. 하지만 별로 추천하지 않는다.... 이방법은 아닌듯 단순 참고

### Add Dependency

For Maven : 

```markup
 <dependency>
  <groupId>com.github.pagehelper</groupId>
  <artifactId>pagehelper-spring-boot-starter</artifactId>
  <version>1.2.3</version>
</dependency>
```

For Gradle : 

```groovy
'com.github.pagehelper:pagehelper-spring-boot-starter:jar:1.2.3'
```

### Configuration PageHelper

```yaml
pagehelper.helper-dialect=sqlite
pagehelper.reasonable=true
pagehelper.support-methods-arguments=true
```

**Controller**

```java
@Autowired
private CityService cityService;

@RequestMapping(value = "/city", method = RequestMethod.GET)
public PageInfo<CityModel> selectCityList(@RequestParam(defaultValue = "1") Integer pageNum,
                                      @RequestParam(defaultValue = "3") Integer pageSize){
    try{
        PageHelper.startPage(pageNum, pageSize);
        List<CityModel> cityList = cityService.selectCityList();
        log.info("pageNum = {}, pageSize={}", pageNum, pageSize);

        PageInfo<CityModel> cityModelPageInfo = new PageInfo<CityModel>(cityList);
        log.info("City List size = {}", cityModelPageInfo.getList().size());
        return cityModelPageInfo;
    }catch (Exception e){
        log.info(e.getMessage());
        return null;
    }
}
```

**Service**

```java
@Autowired
private CityDao cityDao;


public List<CityModel> selectCityList(){
    return cityDao.selectCityList();
}
```

**Dao**

```java
    private String namespace="com.hsnam.spring.paging";

    @Autowired
    private SqlSession sqlSession;

    public List<CityModel> selectCityList(){
        return sqlSession.selectList(namespace+".selectCityList");
    }
```

**SQL**

```markup
<mapper namespace="com.hsnam.spring.paging">
    <select id="selectCityList" resultType="CityModel">
        SELECT ID, NAME, COUNTRY, POPULATION
        FROM CITY
    </select>
</mapper>{
```

{% api-method method="get" host="http://localhost:8080/city?pageNum=1&pageSize=4" path="" %}
{% api-method-summary %}
HTTP Test
{% endapi-method-summary %}

{% api-method-description %}
mybatis Paging-Helper Test
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="pageSize" type="integer" required=false %}
4
{% endapi-method-parameter %}

{% api-method-parameter name="pageNum" type="integer" required=false %}
1
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
{
  "pageNum": 1,
  "pageSize": 4,
  "size": 4,
  "startRow": 1,
  "endRow": 4,
  "total": 15,
  "pages": 4,
  "list": [
    {
      "id": 1,
      "name": "San Francisco",
      "country": "US",
      "population": 10000
    },
    {
      "id": 2,
      "name": "서울",
      "country": "KR",
      "population": 20000
    },
    {
      "id": 3,
      "name": "일본",
      "country": "JP",
      "population": 30000
    },
    {
      "id": 4,
      "name": "부산",
      "country": "KR",
      "population": 40000
    }
  ],
  "prePage": 0,
  "nextPage": 2,
  "isFirstPage": true,
  "isLastPage": false,
  "hasPreviousPage": false,
  "hasNextPage": true,
  "navigatePages": 8,
  "navigatepageNums": [
    1,
    2,
    3,
    4
  ],
  "navigateFirstPage": 1,
  "navigateLastPage": 4,
  "firstPage": 1,
  "lastPage": 4
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% hint style="success" %}
Mybatis Paging Helper 라이브러리 테스트 한결과 페이징에 계산에 필요한 내용들이 PageInfo Class에 담겨 있었다. 하지만 문제가 있다 페이징 이라는 것은 전통적으로 쿼리로 시작하여 성능을 끌어 올리는 것인데 이 것은 무슨 통으로 조회한 List를 PageHelper를 통하여 SQL이 아닌 JAVA진영에서 필터링 하는 단점이 있다 지금이야 List양이 적은 서비스나 테이블에서는 상관이 없지만 만건이상에서는 다음과 같은 방법은 반드시 성능에 문제가 될 소지가 있다. JPA에서는 기본으로 페이징 클래스가 들어가 있는거 같은데 역시 JPA를 공부해야 하나보다. 이상끝. ☺ 
{% endhint %}

\[[Mabatis-PagineHelper\] :](https://github.com/hong-brother/mybatis-paging-helper) Github

