---
tags:
  - nomad-coder
  - langchain
  - python
---

## Requirements
강의를 듣기 위해 필요한 것들
* 선수 지식: python

## What Are We Using
우리가 사용할 것들
* Langchain: Multi LLM Provider / memory 등을 를 지원하는 Framework
* Streamlit: Python UI Framework
* Pinecone: Vector DB
* HuggingFace: 여러 LLM 모델이 등록된 일종의 AI용 Github
* FastAPI: Python Web Framework

## OpenAI Requirements
* 유료 구독자일 것
	* Plugins store 사용
* platform.openai.com 카드 등록
	* API 키 생성
	* 하드/소프트 리밋 꼭 걸어둘 것
* Disclaimer
	* **하드/소프트 리밋 꼭 걸어둘 것**
	* Langchain이 많이 달라졌을 수 있으니 버전 설치 유의할 것

## Virtual Environment
* 3.11.6
	* 나는 uv로 한다
		```bash
		# uv를 이용해서 파이썬 3.11.6 설치
		$ uv init --python 3.11.6
		$ uv python pin 3.11.6
		$ uv venv --python 3.11

		# 가상환경 활성화:
		$ source .venv/bin/activate

		# 버전 확인:
		$ python --version
		Python 3.11.6
		```
	* nvim-tree에서 숨김 파일 보는 법
		```bash
		H  → dotfile 표시/숨김
		I  → Git ignored 파일 표시/숨김
		```
	* requirements.txt 저장소: [링크](https://github.com/nomadcoders/fullstack-gpt/blob/master/requirements.txt)

## jupyter notebook
나는 안쓸거임

## Learn
nvim 키보드 매핑
* tab / shift tab : 탭(버퍼) 이동
* 버퍼 닫기: space + x
* 폴더에서 파일 만들기: 폴더로 커서 이동 + a -> 파일 이름 작성 후 enter

## TroubleShooting

**싱크가 잘 안될 때**
```bash
$ uv pip sync requirements.txt
  × No solution found when resolving dependencies:
  ╰─▶ Because gpt4all==2.0.2 has no wheels with a matching platform tag (e.g., `manylinux_2_43_aarch64`) and you
      require gpt4all==2.0.2, we can conclude that your requirements are unsatisfiable.

      hint: Wheels are available for `gpt4all` (v2.0.2) on the following platforms: `manylinux1_x86_64`,
      `macosx_10_15_universal2`, `win_amd64`
```

`gpt4all`은 적혀 있으나 실제 사용되지 않음. `requirements.txt`에서 다음과 같이 수정하면 됨.
```
# 기존
...
gpt4all==2.0.2
...

# 수정
...
gpt4all==2.0.2; sys_platform != "linux" or platform_machine == "x86_64"
...
```

그럼 깔끔하게 설치됨.