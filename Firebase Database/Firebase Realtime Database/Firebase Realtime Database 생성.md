### Firebase Realtime Database 생성
  - 1. 데이터베이스 만들기
  <img width="1369" alt="스크린샷 2022-02-19 오후 4 38 07" src="https://user-images.githubusercontent.com/67041069/154792487-2e33bda7-2897-4f03-ab20-87bcc4f0669c.png">
  
  - 2. Cloud Firestore의 보안 규칙 설정
    - 테스트 모드 : 모든 사용자가 모든 데이터를 읽고 쓸 수 있음. 체험 기간 내에 기본값 공개 읽기와 쓰기 규칙을 변경하지 않을 경우 데이터베이스 규칙이 모든 요청을 거부 함
    - 잠금 모드 : 인증된 사용자를 제외한 모든 사용자의 읽고 쓰기가 거부
    - 학습용이기 때문에 테스트 모드를 선택
  <img width="1331" alt="스크린샷 2022-02-19 오후 4 38 41" src="https://user-images.githubusercontent.com/67041069/154792492-0dd62604-a4f6-4632-a278-6b96e5bb257d.png">
  
  - 3. Cloud Firestore의 위치 설정
  <img width="1366" alt="스크린샷 2022-02-19 오후 5 02 26" src="https://user-images.githubusercontent.com/67041069/154792496-3c26067c-1544-45e2-9577-d4352ed41ba6.png">

  - 4. Realtime Database 시작
    - JSON 가져오기를 통해 database에 data 추가 가능
    - JSON 내보내기를 통해 database에 data를 다운 가능
  <img width="1371" alt="스크린샷 2022-02-19 오후 5 10 00" src="https://user-images.githubusercontent.com/67041069/154792713-fe38c5f6-c574-4695-a8c7-c680c6a1cd2f.png">
