# :pushpin: MileEASY
>MBTI 기반 여행지 추천 서비스 

- DEMO : (http://43.201.17.158:8080)
</br>

## 1. 제작 기간 & 참여 인원
>- 2022.12.08 ~ 2022.12.29
>- 팀 프로젝트(4명)
>- 담당 역할 : DB설계, MBTI테스트 , 사용자 여행지 추천(방명록)기능

</br>

## 2. 사용 기술
#### `Back-end`
  <img src="https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=Node.js&logoColor=white"> <img src="https://img.shields.io/badge/Express-000000?style=for-the-badge&logo=Express&logoColor=white"> <img src="https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=MySQL&logoColor=white"> <img src="https://img.shields.io/badge/Sequelize-52B0E7?style=for-the-badge&logo=Sequelize&logoColor=white">
  </br>
#### `AWS`
  <img src="https://img.shields.io/badge/Amazon EC2-FF9900?style=for-the-badge&logo=Amazon EC2&logoColor=white"> <img src="https://img.shields.io/badge/Amazon RDS-527FFF?style=for-the-badge&logo=Amazon RDS&logoColor=white">
  </br>
#### `Front-end`
  <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=JavaScript&logoColor=black"> <img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=HTML5&logoColor=white"> <img src="https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=CSS3&logoColor=white"> `EJS`

</br>

## 3. ERD 설계
<img width="700" alt="스크린샷 2023-03-06 오후 4 33 06" src="https://user-images.githubusercontent.com/116782318/223166576-140975a2-c39d-45c1-a546-78131da0900e.png">

## 4. API 명세서
👉 [API 명세 전체보기](https://drive.google.com/file/d/1RicJI6am5wg86hoJGNi0WcOB9BuIoQn8/view?usp=sharing) 👈

<img width="800" alt="스크린샷 2023-04-01 163346" src="https://user-images.githubusercontent.com/116782318/229272449-5bf31628-0005-4f74-8634-af51d7f14259.png">
<img width="800" alt="스크린샷 2023-03-25 오전 1 07 41" src="https://user-images.githubusercontent.com/116782318/227580257-26818533-df99-477a-b692-a6e2943506fc.png">
<img width="800" alt="스크린샷 2023-03-25 오전 1 07 54" src="https://user-images.githubusercontent.com/116782318/227580272-7a9ac9ae-e72a-4cf1-b90f-4f37cddb3a88.png">
<img width="800" alt="스크린샷 2023-04-01 163355" src="https://user-images.githubusercontent.com/116782318/229272451-38fb7e54-1dee-436b-86dc-93c77f7c8ea2.png">

## 5. 핵심 기능
이 서비스의 핵심 기능은 MBTI 테스트 기능입니다. 

<div markdown="1">

### 5.1. MBTI 테스트
  
<img width="820" alt="스크린샷 2023-03-07 172831" src="https://user-images.githubusercontent.com/116782318/223372844-69bc58b4-1f18-4a64-b54c-a02387ebf61b.png">

### 5.1.1 사용자 요청 

- **질문&답변 체크** :pushpin: [코드 확인](https://github.com/nyondoo/mileeasy/blob/2961f19f5153e97090b62d15a886ee0ad1d3bbfc/views/mbtitest.ejs#L33)
  - EJS 화면단에서, 사용자가 답변을 클릭하면 다음 질문으로 넘어갑니다.
  - 마지막 질문의 답변까지 사용자의 답변 유형을 배열에 저장합니다.

- **Axios 비동기 요청** :pushpin: [코드 확인](https://github.com/nyondoo/mileeasy/blob/2961f19f5153e97090b62d15a886ee0ad1d3bbfc/views/mbtitest.ejs#L40)
  - 사용자가 마지막 질문의 답변을 클릭할 경우, 답변 유형 배열을 담은 POST요청을 비동기로 날립니다.

### 5.1.2 Controller

- **요청 처리** :pushpin: [코드 확인](https://github.com/nyondoo/mileeasy/blob/2961f19f5153e97090b62d15a886ee0ad1d3bbfc/controller/Cmbti_test.js#L24)
  - Controller에서는 화면단에서 넘어온 요청을 받고, MBTI 유형 도출 로직을 처리합니다.
  - 요청으로 넘어온 배열에서 8가지 유형별 수를 구하고, 항목별로 비교해 4자리의 MBTI유형을 도출합니다.
  - DB의 유저 정보에 도출한 MBTI유형을 저장합니다.

- **결과 응답** :pushpin: [코드 확인](https://github.com/nyondoo/mileeasy/blob/2961f19f5153e97090b62d15a886ee0ad1d3bbfc/controller/Cmbti_test.js#L81)
  - 결과 페이지만도 보여줄 수 있도록 DB에서 유저의 MBTI유형을 검색합니다.
  - 해당 유형의 여행지 정보를 DB에서 불러와 화면단에 응답해줍니다.

### 5.2. 방명록 (유저간 여행지 추천)
<img width="389" alt="스크린샷 2023-03-07 172944" src="https://user-images.githubusercontent.com/116782318/223372938-1ee12336-4dbb-482e-a76a-bef0018d670d.png">
<img width="746" alt="image" src="https://user-images.githubusercontent.com/116782318/223373285-2bd36db6-27e5-4801-8011-6329718efa11.png">


- **방명록 CRUD**
  - **사용자 요청** :pushpin: [코드 확인](https://github.com/nyondoo/mileeasy/blob/5469b15926259eceb3bb6d11e383527dbf45e1ce/views/guesthome.ejs#L131)
  - **결과 응답** :pushpin: [코드 확인](https://github.com/nyondoo/mileeasy/blob/2961f19f5153e97090b62d15a886ee0ad1d3bbfc/controller/Crecommend.js#L15)
  - 사용자 방명록 페이지와 관련된 기능입니다.
  - '나의 방명록 보기' 기능으로 자신이 작성한 방명록을 볼 수 있게 하였고, 여기서 게시글 삭제가 가능합니다.

</div>

</br>
