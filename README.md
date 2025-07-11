# 📚 AI 기반 시간표 자동 생성기 (Schedule AI Generator)

본 프로젝트는 Anthropic의 Claude 3.5 Sonnet 모델을 활용하여 사용자가 입력한 강의 목록을 기반으로 최적화된 시간표를 자동으로 생성해주는 AI 어시스턴트입니다. </br>
과목의 종류, 학점, 요일, 시간 등의 제약 조건을 고려하여 충돌 없는 다양한 시간표를 제안하고, 각 시간표에 대한 상세 분석 및 설명을 제공합니다.

## ✨ 주요 기능

* **지능형 시간표 생성:** Claude 3.5 Sonnet 모델을 사용하여 복잡한 강의 제약 조건(시간 중복, 카테고리, 학점 등)을 해결하고 최적의 시간표를 생성합니다.
* **다양한 스케줄 케이스 제안:** 시간 충돌이 발생하거나 유연성이 필요한 경우, 여러 가지 가능한 시간표 케이스를 자동으로 생성하여 사용자에게 선택의 폭을 넓혀줍니다.
* **상세 분석 및 설명:** 생성된 각 시간표에 대한 총 학점, 과목 배치 이유, 충돌 해결 방식, 최적화 방안 등 상세한 한글 설명을 제공합니다.
* **명확한 시간 표현:** 수업 시간을 1-9까지의 숫자와 A(15분), B(30분) 접미사를 사용하여 명확하게 표현합니다 (예: 9:00-10:00 -> 1, 10:30-12:00 -> 2B-3).
* **구조화된 출력:** 생성된 시간표는 보기 쉬운 테이블 형식으로 제공되며, 분석 및 설명은 `<analysis>`, `<schedule_case>`, `<explanation>` 등 XML/HTML 유사 태그로 구조화되어 파싱 및 활용이 용이합니다.

## ⚙️ 설치 및 설정

1.  **Anthropic 라이브러리 설치:**
    ```bash
    pip install anthropic
    ```
2.  **Claude API 키 설정:**
    Anthropic에서 발급받은 API 키를 `schedule_ai.ipynb` 파일 내 `<enter-your-api-key>` 부분에 입력합니다.
    ```python
    from anthropic import Anthropic

    MODEL_NAME="claude-3-5-sonnet-20241022"
    client = Anthropic(
        api_key="<enter-your-api-key>", # 여기에 실제 API 키 입력
    )
    ```

## 📝 사용 방법

1.  **Jupyter Notebook 실행:** `schedule_ai.ipynb` 파일을 Jupyter 환경에서 엽니다.
2.  **클래스 목록 입력:** `input_list` 변수에 스케줄을 생성하고자 하는 강의 목록을 다음 형식에 맞춰 입력합니다.
    * 각 강의는 쉼표로 구분되며, 순서는 `title, professor, day of week, time, category, credit` 입니다.
    * `day of week`: 요일 (예: 월요일, 화요일)
    * `time`: 수업 시간 (예: 2-3, 5B-6, 1-2A & 목요일 8B-9)
    * `category`: 강의 유형 (예: 전공기초, 전공핵심, 핵심교양, 기초교양, 심화교양)
    * `credit`: 학점

    **예시 입력 형식:**
    ```python
    input_list = """
    <class_list>
    컴퓨터공학개론, 전혜경, 월요일 2-3, 전공기초, 2
    정보보안개론, 민병준, 월요일 5-6, 전공기초, 2
    자료구조, 박성호, 화요일 1-2A & 목요일 8B-9, 전공핵심, 3
    ...
    </class_list>
    """
    ```
3.  **스케줄 생성 함수 호출:** `get_schedule(input_list)` 함수를 호출하여 시간표를 생성합니다.
    ```python
    get_schedule(input_list)
    ```
4.  **결과 확인:** 실행 결과로 AI가 분석한 내용, 제안된 시간표 케이스(테이블 형식), 그리고 상세 설명이 출력됩니다.

## 📊 출력 형식 (예시)

```text
<analysis>
1. 수업 카테고리:
   ...
2. 과목 수:
   ...
3. 시간 충돌:
   ...
4. 계획 접근방식:
   ...
</analysis>

<schedule_case>
<case_number>1</case_number>
<timetable>
| 시간 | 월요일 | 화요일 | 수요일 | 목요일 | 금요일 |
|------|-----|------|-------|------|------|
| 1    |     |자료구조|       | 데이터 |이산수학|
|      |     |박성호 |       | 이장호 |김지범  |
| ...
</timetable>
<total_score>X</total_score>
</schedule_case>

<schedule_case>
<case_number>2</case_number>
<timetable>
...
</timetable>
<total_score>Y</total_score>
</schedule_case>

<explanation>
스케줄 구성에 대한 설명:
...
</explanation>
