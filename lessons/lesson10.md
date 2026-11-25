# 10강 · 파일과 CSV

**목표:** 파일을 안전하게 열고 닫으며, 텍스트 파일과 CSV 데이터를 읽고, 누락·잘못된 행을 처리해 요약 결과를 만든다.

이번 강의는 파일 경로, 인코딩, `with`, CSV의 헤더와 행을 다룬다. 원본 데이터를 보존하면서 읽기·검증·계산·결과 저장을 분리하는 것이 핵심이다.

## 0. 파일 작업의 흐름

파일을 다룰 때는 다음 순서로 생각한다.

1. 파일 경로와 인코딩을 정한다.
2. 파일을 연다.
3. 내용을 읽거나 쓴다.
4. 파일을 닫는다.
5. 빈 파일과 잘못된 내용을 확인한다.

파이썬에서는 `with open(...)`을 사용하면 블록이 끝날 때 파일이 안전하게 닫힌다.

## 1. 텍스트 파일 쓰기

```python
with open("memo.txt", "w", encoding="utf-8") as file:
    file.write("첫 번째 줄\n")
    file.write("두 번째 줄\n")
```

주요 모드:

- `"r"`: 읽기
- `"w"`: 새로 쓰기. 기존 내용이 있으면 덮어씀
- `"a"`: 기존 내용 뒤에 추가

`encoding="utf-8"`을 지정하면 한글 파일을 다룰 때 인코딩 문제를 줄일 수 있다.

## 2. 텍스트 파일 읽기

```python
with open("memo.txt", "r", encoding="utf-8") as file:
    content = file.read()

print(content)
```

파일을 한 줄씩 처리할 수도 있다.

```python
with open("memo.txt", "r", encoding="utf-8") as file:
    for line in file:
        print(line.strip())
```

파일의 줄에는 보통 줄바꿈 문자인 `\n`이 포함될 수 있다. 출력이나 비교 전에 `strip()`으로 양쪽 공백과 줄바꿈을 정리할 수 있다.

## 3. 파일 경로와 오류

파일이 존재하지 않으면 `FileNotFoundError`가 발생한다.

```python
with open("없는파일.txt", "r", encoding="utf-8") as file:
    content = file.read()
```

실제 프로그램에서는 경로가 맞는지 확인하거나, 오류를 잡아 사용자에게 안내할 수 있다.

```python
try:
    with open("memo.txt", "r", encoding="utf-8") as file:
        content = file.read()
except FileNotFoundError:
    print("파일을 찾을 수 없습니다.")
```

## 4. CSV란?

CSV는 표 형태의 데이터를 쉼표로 구분해 저장하는 텍스트 형식이다.

예시:

```text
name,score
민수,80
지영,95
철수,70
```

첫 번째 줄은 보통 헤더다. 헤더는 각 열의 이름을 알려준다.

- `name`: 이름 열
- `score`: 점수 열

CSV를 직접 `split(",")`로 나눌 수도 있지만, 값 안에 쉼표나 따옴표가 들어가면 문제가 생길 수 있다. 파이썬의 `csv` 모듈을 사용하는 것이 안전하다.

## 5. `csv.reader`로 읽기

```python
import csv

with open("scores.csv", "r", encoding="utf-8", newline="") as file:
    reader = csv.reader(file)

    for row in reader:
        print(row)
```

각 행은 리스트로 읽힌다.

```python
["name", "score"]
["민수", "80"]
```

CSV에서 읽은 값은 기본적으로 문자열이다. 계산하려면 숫자로 변환해야 한다.

```python
score = int(row[1])
```

## 6. `csv.DictReader`로 읽기

헤더를 키로 사용하고 싶다면 `DictReader`가 편리하다.

```python
import csv

with open("scores.csv", "r", encoding="utf-8", newline="") as file:
    reader = csv.DictReader(file)

    for row in reader:
        name = row["name"]
        score = int(row["score"])
        print(f"{name}: {score}")
```

각 행은 딕셔너리처럼 사용할 수 있다.

```python
{"name": "민수", "score": "80"}
```

## 7. CSV 값 검증

CSV는 사람이 직접 수정할 수 있으므로 빈 값이나 잘못된 값이 들어올 수 있다.

```text
name,score
민수,80
지영,
철수,abc
,70
```

다음 상황을 확인해야 한다.

- 이름이 비어 있음
- 점수가 비어 있음
- 점수가 숫자로 변환되지 않음
- 필요한 열이 없음

숫자 변환은 `try`와 `except`로 처리할 수 있다.

```python
try:
    score = int(row["score"])
except ValueError:
    print("점수가 숫자가 아닙니다.")
```

오류 행을 바로 중단할지, 건너뛰고 기록할지 규칙을 먼저 정한다.

## 8. CSV 요약 흐름

CSV 요약 프로그램은 보통 다음 순서로 만든다.

1. 입력 파일을 연다.
2. 헤더를 읽는다.
3. 각 행의 필수 값을 확인한다.
4. 숫자 값을 변환한다.
5. 행 수·합계·최댓값을 누적한다.
6. 잘못된 행을 별도로 기록한다.
7. 결과를 출력하거나 결과 파일에 저장한다.

계산과 출력은 함수로 분리하면 테스트하기 쉽다.

```python
def summarize_scores(rows):
    count = 0
    total = 0
    maximum = None

    for row in rows:
        score = row["score"]
        count += 1
        total += score

        if maximum is None or score > maximum:
            maximum = score

    return count, total, maximum
```

함수 밖에서 결과를 보기 좋은 문장으로 출력할 수 있다.

## 9. 결과 파일 만들기

결과를 다른 CSV 파일에 저장할 때는 `csv.writer`를 사용할 수 있다.

```python
import csv

with open("summary.csv", "w", encoding="utf-8", newline="") as file:
    writer = csv.writer(file)
    writer.writerow(["항목", "값"])
    writer.writerow(["행 수", 3])
```

원본 파일을 `"w"` 모드로 열면 내용이 덮어써질 수 있으므로, 결과 파일과 원본 파일의 경로를 분리한다.

## 10. 자주 하는 실수

### 파일을 닫지 않음

가능하면 `with open(...)`을 사용한다.

### 인코딩을 지정하지 않음

한글 파일에서는 `encoding="utf-8"`을 명시한다.

### CSV 숫자를 바로 계산함

CSV에서 읽은 값은 문자열이므로 `int()`나 `float()` 변환이 필요하다.

### 헤더를 데이터 행으로 계산함

`reader`를 직접 사용할 때 헤더를 먼저 읽거나, `DictReader`를 사용한다.

### 잘못된 행에서 전체 프로그램이 종료됨

빈 값·변환 오류를 확인하고, 건너뛸지 오류 목록에 저장할지 정한다.

### 원본 파일을 덮어씀

입력 파일은 읽기 전용으로 사용하고, 결과는 별도의 파일에 저장한다.

## 11. 직접 작성하는 연습 문제

문제는 파일을 직접 만들고 실행하는 방식으로 진행한다. 각 문제에서 빈 파일·한 행·잘못된 행을 확인한다.

### 문제 1 · 텍스트 파일 쓰기

`study_memo.txt` 파일을 만들고 오늘 배운 내용을 두 줄 이상 저장한다. 파일을 다시 읽어 저장한 내용이 그대로 출력되는지 확인한다.

### 문제 2 · 줄 수 세기

텍스트 파일을 한 줄씩 읽어 빈 줄을 제외한 줄의 개수를 반환하는 함수를 만든다. 파일이 비어 있을 때 결과가 0인지 확인한다.

### 문제 3 · CSV 만들기

이름과 점수 열을 가진 `scores.csv` 파일을 직접 만든다. `csv.reader`로 읽고 각 행의 이름과 점수를 출력한다.

### 문제 4 · 점수 합계와 최댓값

CSV의 점수 열을 정수로 변환해 행 수, 합계, 최댓값을 계산한다. 점수가 하나도 없을 때 최댓값을 `None`으로 처리한다.

### 문제 5 · `DictReader` 사용

`csv.DictReader`로 CSV를 읽어 이름별 점수를 딕셔너리로 만든다. 헤더 이름을 잘못 입력했을 때 어떤 오류가 발생하는지도 확인한다.

### 문제 6 · 잘못된 행 건너뛰기

이름이 비어 있거나 점수가 숫자가 아닌 행이 섞인 CSV를 만든다. 정상 행만 계산하고, 잘못된 행의 번호를 별도로 출력한다.

### 문제 7 · 요약 결과 저장

CSV를 읽어 계산한 행 수·합계·최댓값을 `summary.csv`에 저장한다. 원본 CSV의 내용이 바뀌지 않았는지 확인한다.

### 문제 8 · 오류 처리

존재하지 않는 파일을 열어 `FileNotFoundError`를 확인한다. `try`와 `except`를 사용해 오류 메시지를 출력하고 프로그램이 정상적으로 끝나도록 수정한다.

## 12. 오늘 배운 내용 확인

1. `with open(...)`을 사용하는 이유는 무엇인가?
2. `"r"`, `"w"`, `"a"` 모드는 각각 어떤 의미인가?
3. CSV에서 읽은 숫자를 바로 더할 수 없는 이유는 무엇인가?
4. `csv.reader`와 `csv.DictReader`의 차이는 무엇인가?
5. 빈 값과 잘못된 숫자를 만났을 때 어떤 처리 규칙을 정할 수 있는가?
6. 원본 CSV와 결과 CSV를 분리해야 하는 이유는 무엇인가?

## 통과 기준

- `with open()`으로 텍스트 파일을 읽고 쓴다.
- UTF-8 인코딩과 파일 모드를 설명한다.
- CSV 헤더와 행을 읽고 숫자 문자열을 변환한다.
- 빈 행·잘못된 행·빈 파일을 처리한다.
- 행 수·합계·최댓값을 계산하고 결과 파일을 만든다.
- 원본 파일을 보존하고 실행 방법을 설명한다.

## 나에게 보낼 것

문제 1~8 중 직접 작성한 코드와 실행 결과를 보내고, 잘못된 행을 어떻게 처리했는지 설명한다. 통과 기준을 확인하면 다음 강의인 모듈과 실행 환경 교재를 만든다.
