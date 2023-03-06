# :pushpin: MileEASY
>MBTI 기반 여행지 추천 서비스 
>bit.ly/3ZGbquV

</br>

## 1. 제작 기간 & 참여 인원
- 2022.12.08 ~ 2022.12.29
- 팀 프로젝트(4명)
- 담당 역할 : DB설계, MBTI테스트 , 사용자 여행지 추천(방명록)기능

</br>

## 2. 사용 기술
#### `Back-end`
  - Node.js
  - Express
  - MySQL
#### `Front-end`
  - HTML
  - CSS
  - JavaScript
  - EJS

</br>

## 3. ERD 설계
<img width="579" alt="스크린샷 2023-03-06 오후 4 33 06" src="https://user-images.githubusercontent.com/116782318/223166576-140975a2-c39d-45c1-a546-78131da0900e.png">


## 4. 핵심 기능
이 서비스의 핵심 기능은 MBTI 테스트 기능입니다. 

<details>
<summary><b>핵심 기능 설명 펼치기</b></summary>
<div markdown="1">

### 4.1. MBTI 테스트
![](https://zuminternet.github.io/images/portal/post/2019-04-22-ZUM-Pilot-integer/flow1.png)

### 4.1.1 사용자 요청 

- **질문&답변 체크** :pushpin: [코드 확인](https://github.com/nyondoo/mileeasy/blob/2961f19f5153e97090b62d15a886ee0ad1d3bbfc/views/mbtitest.ejs#L33)
  - EJS 화면단에서, 사용자가 답변을 클릭하면 다음 질문으로 넘어갑니다.
  - 마지막 질문의 답변까지 사용자의 답변 유형을 배열에 저장합니다.

- **Axios 비동기 요청** :pushpin: [코드 확인](https://github.com/nyondoo/mileeasy/blob/2961f19f5153e97090b62d15a886ee0ad1d3bbfc/views/mbtitest.ejs#L40)
  - 사용자가 마지막 질문의 답변을 클릭할 경우, 답변 유형 배열을 담은 POST요청을 비동기로 날립니다.

### 4.1.2 Controller

- **요청 처리** :pushpin: [코드 확인](https://github.com/nyondoo/mileeasy/blob/2961f19f5153e97090b62d15a886ee0ad1d3bbfc/controller/Cmbti_test.js#L24)
  - Controller에서는 화면단에서 넘어온 요청을 받고, MBTI 유형 도출 로직을 처리합니다.
  - 요청으로 넘어온 배열에서 8가지 유형별 수를 구하고, 항목별로 비교해 4자리의 MBTI유형을 도출합니다.
  - DB의 유저 정보에 도출한 MBTI유형을 저장합니다.

- **결과 응답** :pushpin: [코드 확인](https://github.com/nyondoo/mileeasy/blob/2961f19f5153e97090b62d15a886ee0ad1d3bbfc/controller/Cmbti_test.js#L81)
  - 결과 페이지만도 보여줄 수 있도록 DB에서 유저의 MBTI유형을 검색합니다.
  - 해당 유형의 여행지 정보를 DB에서 불러와 화면단에 응답해줍니다.

### 4.2. 방명록 (유저간 여행지 추천)

![](https://zuminternet.github.io/images/portal/post/2019-04-22-ZUM-Pilot-integer/flow_service1.png)

- **방명록 CRUD**
  - **사용자 요청** :pushpin: [코드 확인](https://github.com/nyondoo/mileeasy/blob/5469b15926259eceb3bb6d11e383527dbf45e1ce/views/guesthome.ejs#L131)
  - **결과 응답** :pushpin: [코드 확인](https://github.com/nyondoo/mileeasy/blob/2961f19f5153e97090b62d15a886ee0ad1d3bbfc/controller/Crecommend.js#L15)
  - 사용자 방명록 페이지와 관련된 기능입니다.
  - '나의 방명록 보기' 기능으로 자신이 작성한 방명록을 볼 수 있게 하였고, 여기서 게시글 삭제가 가능합니다.

</div>
</details>

</br>

## 5. 핵심 트러블 슈팅
### 5.1. 컨텐츠 필터와 페이징 처리 문제
- 저는 이 서비스가 페이스북이나 인스타그램 처럼 가볍게, 자주 사용되길 바라는 마음으로 개발했습니다.  
때문에 페이징 처리도 무한 스크롤을 적용했습니다.

- 하지만 [무한스크롤, 페이징 혹은 “더보기” 버튼? 어떤 걸 써야할까](https://cyberx.tistory.com/82) 라는 글을 읽고 무한 스크롤의 단점들을 알게 되었고,  
다양한 기준(카테고리, 사용자, 등록일, 인기도)의 게시물 필터 기능을 넣어서 이를 보완하고자 했습니다.

- 그런데 게시물이 필터링 된 상태에서 무한 스크롤이 동작하면,  
필터링 된 게시물들만 DB에 요청해야 하기 때문에 아래의 **기존 코드** 처럼 각 필터별로 다른 Query를 날려야 했습니다.

<details>
<summary><b>기존 코드</b></summary>
<div markdown="1">

~~~java
/**
 * 게시물 Top10 (기준: 댓글 수 + 좋아요 수)
 * @return 인기순 상위 10개 게시물
 */
public Page<PostResponseDto> listTopTen() {

    PageRequest pageRequest = PageRequest.of(0, 10, Sort.Direction.DESC, "rankPoint", "likeCnt");
    return postRepository.findAll(pageRequest).map(PostResponseDto::new);
}

/**
 * 게시물 필터 (Tag Name)
 * @param tagName 게시물 박스에서 클릭한 태그 이름
 * @param pageable 페이징 처리를 위한 객체
 * @return 해당 태그가 포함된 게시물 목록
 */
public Page<PostResponseDto> listFilteredByTagName(String tagName, Pageable pageable) {

    return postRepository.findAllByTagName(tagName, pageable).map(PostResponseDto::new);
}

// ... 게시물 필터 (Member) 생략 

/**
 * 게시물 필터 (Date)
 * @param createdDate 게시물 박스에서 클릭한 날짜
 * @return 해당 날짜에 등록된 게시물 목록
 */
public List<PostResponseDto> listFilteredByDate(String createdDate) {

    // 등록일 00시부터 24시까지
    LocalDateTime start = LocalDateTime.of(LocalDate.parse(createdDate), LocalTime.MIN);
    LocalDateTime end = LocalDateTime.of(LocalDate.parse(createdDate), LocalTime.MAX);

    return postRepository
                    .findAllByCreatedAtBetween(start, end)
                    .stream()
                    .map(PostResponseDto::new)
                    .collect(Collectors.toList());
    }
~~~

</div>
</details>

- 이 때 카테고리(tag)로 게시물을 필터링 하는 경우,  
각 게시물은 최대 3개까지의 카테고리(tag)를 가질 수 있어 해당 카테고리를 포함하는 모든 게시물을 질의해야 했기 때문에  
- 아래 **개선된 코드**와 같이 QueryDSL을 사용하여 다소 복잡한 Query를 작성하면서도 페이징 처리를 할 수 있었습니다.

<details>
<summary><b>개선된 코드</b></summary>
<div markdown="1">

~~~java
/**
 * 게시물 필터 (Tag Name)
 */
@Override
public Page<Post> findAllByTagName(String tagName, Pageable pageable) {

    QueryResults<Post> results = queryFactory
            .selectFrom(post)
            .innerJoin(postTag)
                .on(post.idx.eq(postTag.post.idx))
            .innerJoin(tag)
                .on(tag.idx.eq(postTag.tag.idx))
            .where(tag.name.eq(tagName))
            .orderBy(post.idx.desc())
                .limit(pageable.getPageSize())
                .offset(pageable.getOffset())
            .fetchResults();

    return new PageImpl<>(results.getResults(), pageable, results.getTotal());
}
~~~

</div>
</details>
    
</br>

## 6. 회고 / 느낀점
>프로젝트 개발 회고 글: https://zuminternet.github.io/ZUM-Pilot-integer/
