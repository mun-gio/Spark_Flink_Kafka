# Spark_Flink_Kafka
# Part 2. Apache Spark

## 1. Apache Spark

- 데이터 센터나 클라우드에서 대규모 분산 데이터 처리를 하기 위해 설계된 통합형 엔진.
1. 속도
    - 디스크 I/O를 주로 사용하는 하둡 맵리듀스 처리 엔진과 달리, `중간 결과를 메모리에 유지`하기 때문에 훨씬 더 빠른 속도로 같은 작업을 수행 가능.
    - 질의 연산을 방향성 비순환 그래프 (DAG-Directed acyclic graph)로 구성.
        - DAG의 스케줄러와 질의 최적화 모듈은, 효율적인 연산 그래프를 만들고 각각의 테스크로 분해하여, 클러스터의 워커 노드 위에서 병렬 실행될 수 있도록 함.
        - 물리적 실행 엔진인 텅스텐은 전체 코드 생성 기법을 사용해, 실행을 위한 간결한 코드를 생성.
2. 사용 편리성
    - 클라이언트 입장에서, 스파크 코드가 단일 PC, 혹은 분산 환경에서 실행되는지 구별하기 어려울 정도로 추상화가 잘 되어 있음.
    - Dataframe, Dataset과 같은 고수준 데이터 추상화 계층 아래에, RDD (Resilient distributed dataset)라 불리는 단순한 자료구조를 구축해 단순성을 실현.
    - 연산의 종류로, 트랜스포메이션 (transformation)과 액션 (action)의 집합과 단순한 프로그래밍 모델을 제공.
    - 여러 프로그래밍 언어를 제공하여, 사용자들이 각자 편한 언어로 스파크 애플리케이션을 작성할 수 있음.
3. 모듈성
    - 스파크에 내장된 다양한 컴포넌트 (SparkSQL, Structured Streaming, MLlib 등)들을 사용해, 다양한 타입의 워크로드에 적용이 가능함.
    - 특정 워크로드를 처리하기 위해 하나의 통합된 처리 엔진을 가짐.
        - 하둡맵리듀스의 경우 배치 워크로드에서 적합하나 SQL 질의, 스트리밍, 머신러닝 등 다른 워크로드와 연계해 사용하기는 어려움이 존재.
        - 이런 워크로드 를 다루기 위해 엔지니어들은 하둡과 함께, Apache Hive (SQL 질의), Storm(스트리밍), Mahout(머신러닝) 등 다른 시스템과 의 연동이 필요.
        - 이들은 각각 자신만의 API, 설정 방식 등을 가지고 있기 때문에, 모듈성이 떨어지고 개발자가 배우기 어려움
4. 확장성
    - 저장과 연산을 모두 포함하는 하둡과는 달리, 스파크는 빠른 병렬 연산에 초점.
    - 수많은 데이터 소스 (하둡, 카산드라, Hbase, 몽고DB, RDBMS, AWS S3등) 로부터 데이터를 읽어 들일수 있음.
    - 여러 파일 포맷(txt, csv, parquet, roc, hdfs 등)과 호환 가능.
    - 이외에 많은 서드파티 패키지 목록 사용 가능.

## 2. 로컬 환경에 스파크 설치 및 워드 카운트 예제 실행

1. 로컬 스파크 구축 
    1. Java 설치
        - Mac
            
            ```bash
            brew update
            brew tap adoptopenjdk/openjdk # adoptopenjdk/openjdk 레포지토리 추가
            brew search jdk # 다운로드 가능한 jdk 버전 확인
            brew install --cask adoptopenjdk11 # java 11 다운로드
            java --version # 자바 버전 확인
            ```
            
        - ubuntu
            
            ```bash
            sudo apt update
            sudo apt install openjdk-11-jdk
            java -version
            sudo update-alternatives --config java
            ```
            
    2. Python 가상환경 구성, pyspark 라이브러리 설치
        - Python 가상환경 구성
            - 필수 패키지 설치
                
                ```bash
                sudo apt update
                sudo apt install -y make build-essential libssl-dev zlib1g-dev \
                libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
                libncurses5-dev libncursesw5-dev xz-utils tk-dev libffi-dev \
                liblzma-dev git
                
                pip install pyopenssl
                ```
                
            - `pyenv` 설치
                
                ```bash
                curl https://pyenv.run | bash
                ```
                
            - 환경 설정 파일 업데이트
                
                ```bash
                export PATH="$HOME/.pyenv/bin:$PATH"
                eval "$(pyenv init --path)"
                eval "$(pyenv init -)"
                ```
                
                ```bash
                source ~/.zshrc
                # or source ~/.bashrc
                ```
                
            - `pyenv` 버전 확인
                
                ```bash
                pyenv --version
                ```
                
            - Python 버전 설치 및 사용
                
                ```bash
                pyenv install <python-version>
                # pyenv install 3.9.1
                pyenv install 3.11.9
                ```
                
                - 글로벌 Python 버전 설정
                
                ```bash
                pyenv global 3.11.9
                
                ```
                
            - 환경 변수 경고 해결
                
                ```bash
                export PATH="$HOME/.pyenv/versions/3.11.9/bin:$PATH"
                
                source ~/.zshrc
                ```
                
            - Python 버전 확인
                
                ```bash
                python --version
                ```
                
        - pyspark 라이브러리 설치
            
            ```bash
            pip install pyspark
            ```
            
            - 정상 작동 확인
            
            ```bash
            pyspark
            ```
            
    3. 워드 카운트 예제
        - https://github.com/startFromBottom/fc-spark-streaming/tree/main/part02/ch01_intro
    - ubuntu vscode 설치
        
        ```bash
        sudo apt install snapd
        sudo snap install code --classic
        ```
        
        - VScode 실행
        
        ```bash
        code
        ```
        
- 실습
    - tree
        
        ```bash
        .
        └── part02
            └── ch01
                ├── data
                │   └── words.txt
                ├── wordcount_python_ver.py
                └── wordcount_rdd_ver.py
        ```
        
    - words.txt
        
        ```
        This blog is exclusive for all the people who are interested in learning one of the trending in current IT industry.
        This course is suitable for students, teacher, IT engineer freshers etc..
        It talks about the spark with pyhton (pyspark) from beginner till experts.
        ```
        
    - wordcount_python_ver.py
        
        ```python
        from collections import defaultdict
        
        if __name__ == '__main__':
            words_count: dict[str, int] = defaultdict(int)
            with open('/home/mungio/Spark_Flink_Kafka/data/words.txt', 'r') as file:
                for _, line in enumerate(file):
                    words_each_line = line.strip().split(" ")
        
                    for _, word in enumerate(words_each_line):
                        words_count[word] += 1
            for word, count in words_count.items():
                print(f"{word}: {count}")
        ```
        
    - wordcount_rdd_ver.py
        
        ```python
        from pyspark import SparkContext, RDD
        from pyspark.sql import SparkSession
        
        if __name__ == "__main__":
            ss: SparkSession = SparkSession.builder \
                .master("local") \
                .appName("wordCount RDD ver") \
                .getOrCreate()
            sc: SparkContext = ss.sparkContext
        
            # load data
            text_file: RDD[str] = sc.textFile("data/words.txt")
        
            # transformations
            counts = text_file\
                .flatMap(lambda line: line.split(" ")) \
                .map(lambda word: (word, 1)) \
                .reduceByKey(lambda count1, count2: count1 + count2)
            
            # action
            output = counts.collect()
        
            for (word, count) in output:
                print(f"{word} : {count}")
        ```
        

# Part 3.

# Part 4.

# Part 5.

# Part 6. 스트림 프로세싱 프로젝트

# 1. 프로젝트 개요

- 실시간 웹 사이트들의 도서 정보 → 가격의 최대값, 최소값 등 통계치 계산
- Kafka + Spark
- Python 사용
- kafka message format으로 protocol buffer 사용
    - (https://protobuf.dev/)
    - Json과 달리 스키마를 정의할 수 있고, 용량도 작으며 성능 우수

# 2. 시스템 구성

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/ae28cad0-9ec5-4d41-85c6-1fe0b6b8eaf7/523c84ec-8f9a-4e2a-be6f-a3ed88915327/image.png)

# 3. 실습 링크

• code : https://github.com/startFromBottom/fc-kafka-spark-practice

# 4. naver, kakao api 링크

- naver 책 검색 api
    - [https://developers.naver.com/docs/serviceapi/search/book/book.md#개요](https://developers.naver.com/docs/serviceapi/search/book/book.md#%EA%B0%9C%EC%9A%94)
- kakao 책 검색 하기
    - https://developers.kakao.com/docs/latest/ko/daum-search/dev-guide#search-book