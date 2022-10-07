# ADF-Basic-HOL

### * Azure Portal 로그인 및 초기 비밀번호 변경
    - 사용자 계정 할당
    - (주의) 2 factor 인증은 Skip

### * SQL 연결
    - DTU 올리 것 (100 DTU)
    - 방화벽 설정

### * ADLS & ADF 
    (1) ADLS의 Storage browser에서 datalake (컨테이너) -> user0x -> L0 밑에 landing 폴더 생성하기
    
    (2) ADLS의 Storage browser에서 datalake (컨테이너) -> user0x -> L0 밑에 ref_accounting_treatment, reference_loan_stats 폴더 확인
    
    (3) ADF (고객 / 파트너사 각각 다른 ADF)
        (3-1) Dataset 생성하기
            - Datasets에서 user0x 폴더 생성 (개인용 dataset 관리를 위하여)
            - user0x 폴더에서 landing dataset 생성하기 (Azure Data Lake Storage Gen2 & Parquet) 
                --> 이름은 user0x_alds_landing_parquet (이름이 중복되면 에러 발생)
                --> File path 선택 시 Browse를 클릭하여 datalake (컨테이너) -> user0x -> L0 밑에 landing 폴더를 선택
                --> 생성 이후, 마지막 File name 부분에 landing.parquet 입력 (여기서 파일명을 입력하지 않으면 추후 Copy 단계에서 소스명의 이름을 기본값으로 가져와 파일명을 생성)
            - user0x 폴더에서 landing dataset 생성하기 (Azure SQL DB) --> --> 이름은 user0x_sqldb_landing
        (3-2) Pipeline 생성하기
            - Pipelines에서 user0x 폴더 생성 (개인용 dataset 관리를 위하여)
            - user0x 폴더에서 Pipeline 생성하기 --> 이름은 user0x_pl_sqldb2adls_landing 
            - Activities의 Move & transform에서 Copy data를 drag & drop --> 이름은 user0x_copy_sqldb2adls_landing
            - Source 탭에서 소스를 이전 단게에서 생성한 user0x_sqldb_landing 선택 후 Preview data를 클릭하여 정상 조회 확인
            - Sink 탭에서 소스를 이전 단게에서 생성한 user0x_alds_landing_parquet 선택
            - Mapping 탭에서 Import schemas를 클릭하여 조회된 기본 스키마 매핑 정보 확인
            - Validate를 클릭하여 에러 여부 사전 체크
            - Debug를 클릭하여 실행
            - Refresh 버튼을 눌러 In progress 또는 Succeeded Status 확인 후 Detail 아이콘 (안경 모양)을 클릭하여 진행 또는 수행 결과 확인
            - ADLS의 Storage browser에서 datalake (컨테이너) -> user0x -> L0 밑에 landing 폴더 내에 parquet 파일 생성 여부 확인
            
    (4) ADLS (시간 관계 상 나머지 2개 테이블에서 ETL한 parquet 파일은 ADLS 폴더 간 복사)
        (4-1) ADLS의 Storage browser에서 datalake (컨테이너) -> roy -> L0 --> ref_accounting_treatment 밑에 ref_accounting_treatment.parquet을 복사하여 datalake (컨테이너) -> user0x -> L0 --> ref_accounting_treatment 폴더로 복사하기
        (4-2) ADLS의 Storage browser에서 datalake (컨테이너) -> roy -> L0 --> reference_loan_stats 밑에 reference_loan_stats.parquet을 복사하여 datalake (컨테이너) -> user0x -> L0 --> reference_loan_stats 폴더로 복사하기



### * 리소스 삭제 or scale down
    - ADB 클러스터
    - SQL DB
