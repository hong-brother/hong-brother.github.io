---
description: Spring boot 페이징 관련내용
---

# Pagination

## PaginationModel 클래스 생성

* 페이징에 필요한 요소들을 Model 클래스로 만든다.

```java
@Data
public class PageDto {
    @Builder.Default
    private int listSize = 10 //한 페이지당 보여줄 목록 리스트 개수
    @Builder.Default
    private int rangeSize = 10; //페이지 범위에 보여질 페이지 번의 개수 
    private int page; //현재 목록의 페이지 번호
    private int range; //각 페이지 범위 시작 번
    private int listCnt; // 전체 개시물의 개수
    private int pageCnt; //전체 페이지 범위의 개수
    private int startPage; //각 페이지 범위 시작 번호
    private int startList;
    private int endPage; //각 페이지 범위 끝 번호
    private boolean isPrev; // 이전 페이지 여부
    private boolean isNext; //다음 페이지 여 
}
```

* 페이징 계산 함수 

```java
/**
* page 현재 목록의 페이지 번호
* range 각 페이지 범위 시작 번호
* listCnt 전체 게시물의 개
*
**/
public void pageInfo(int page, int range, int listCnt){
    //전체 페이지 수 = ex)  100개의 게시물 / 한페이지당 10개씩 보여줄 리스트 = 10 페이
    this.pageCnt = (int) Math.ceil(listCnt/listSize);
    
    //시작 페이지 = ex) (1페이지 -1) * 10개의 페이지 번호를 표줄 +1 =1 페이지
    this.startPage = (range -1) * rangeSize + 1; 
    
    //끝 페이지 = ex) 1페이지 * 10개의 페이지 번호 = 10;
    this.endPage = range * rangeSize;
    
    //게시판 시작번호 = ex) 
    this.startList = (page -1) * listSize;
    
    //이전 버튼 상태
    this.pre = range == 1?false : true;
    
    //다음 버튼 상태
    this.next = endPage > pageCnt ? false :true;
    if(this.endPage > this.pageCnt){
        this.endPage = this.pageCnt;
        this.next = false;
    }
}
```

