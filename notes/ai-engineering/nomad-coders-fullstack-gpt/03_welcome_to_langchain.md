---
tags:
  - nomad-coder
  - langchain
  - python
  - litellm
---
## LLMs and Chat Models

* LLM: Large Language Model
* OpenAI 를 이용 -> 나는 LiteLLM + Z.ai 이용

### LiteLLM을 설정해보자.
.env
```env
ZAI_API_KEY="<Z.AI API KEY>"
# LiteLLM 마스터 키
OPENAI_API_KEY=sk-litellm-local-secret
# LiteLLM URL
OPENAI_API_BASE=http://127.0.0.1:4000/v1
# LiteLLM Model
OPENAI_MODEL=gpt-3.5-turbo
```

docker-compose.yaml
```yaml
services:
    litellm:
      image: docker.litellm.ai/berriai/litellm:main-latest
      command:
        - --config
        - /app/litellm_config.yaml
        - --port
        - "4000"

      ports:
        - "127.0.0.1:4000:4000"

      volumes:
        - ./litellm_config.yaml:/app/litellm_config.yaml:ro

	  # .env에 등록된 환경 변수를 로드한다.
      env_file:
        - ../.env

      restart: unless-stopped
```

litellm_config.yaml
```yaml

  litellm_settings:
    # OpenAI -> Z.ai 시 맞지 않는 파람들은 드랍한다.
    drop_params: true


  general_settings:
    master_key: os.environ/OPENAI_API_KEY

  model_list:
    - model_name: text-davinci-003
      litellm_params:
        model: zai/glm-5.3
        api_key: os.environ/ZAI_API_KEY

    - model_name: gpt-3.5-turbo
      litellm_params:
        model: zai/glm-5.3
        api_key: os.environ/ZAI_API_KEY

    - model_name: gpt-4
      litellm_params:
        model: zai/glm-5.3
        api_key: os.environ/ZAI_API_KEY
```

LiteLLM 연동 확인
```bash
curl http://127.0.0.1:4000/v1/completions \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer sk-litellm-local-secret" \
    -d '{
      "model": "text-davinci-003",
      "prompt": "How many planets are there?"
    }'
```

## ChatModels
* ChatModel은 메세지 묶을 보내줄 수 있다.
* 생성자 파라미터
	* temperature: 높을수록 랜덤한 결과
	* max_token: 토큰 최대 수
* 메세지 3종류 (`langchain.schema`)
	* SystemMessage: 시스템 프롬프트에 해당. AI에게 페르소나 지정
	* AIMessage: AI에게 보내는 메세지
	* HumanMessage: 인간이 입력하는 프롬프트
* 메세지 전달하기
	```python
	from langchain.chat_models import ChatOpenAI
	from langchain.schema import HumanMessage, AIMessage, SystemMessage

	chat = ChatOpenAI(temperature=0.1)
	messages = [
		SystemMessage(
			content="You are a geography expert, And you only reply in Korean"
		),
		AIMessage(
			content="Hi your name is Hyunmoo"
		),
		HumanMessage(
			content="What is distance between Korean and England. Also What is your name?"
		),
	]
	result = chat.predict_messages(messages)
	print(result)
	```
## Prompt Templates
* 프롬프트 템플릿 기능을 이용하여 동적으로 메세지를 전달할 수 있다
* `langchain.prompts` 모듈의 PromptTemplate, ChatPromptTemplate 이용
* PromptTemplate
	```python
	from langchain.prompts import PromptTemplate
	template = PromptTemplate.from_template(
		"What is distance between {country_src} and {country_dst}"
	)
	template.format(country_src="Korean", country_dst="England")
	```
	* 만약 format 메소드에서 파라미터 (country_a, country_b)들을 누락 혹은 오타가 나면 에러가 발생함.
* ChatPromptTemplate (messages로 전달했던 SystemMessage, AIMessage, HumanMessage를 대체할 수 있음)
	```python
	from langchain.chat_models import ChatOpenAI
	from langchain.prompts import ChatPromptTemplate

	chat = ChatOpenAI(temperature=0.1)
	template = ChatPromptTemplate.from_messages(
		[
			("system", "You are a geography expert, And you only reply in {language}"),
			("ai", "Hi your name is {name}"),
			("human", "What is distance between {country_src} and {country_dst}. Also What is your name?"),
		]
	)
	prompt = template.format_messages(
		language="Korean",
		name="Hyunmoo",
		country_src="Korean",
		country_dst="England"
	)
	result = chat.predict_messages(prompt)
	print(result)
	```
## OutputParser and LCEL


## Chaining Chains

## Learn
nvim
* 코드 선언부 가기: gd
* 코드 정의부 가기: gD
* 이전 위치 돌아가기: ctrl + o
* 다시 앞으로 이동: ctrl + i
* 트리에서 폴더 접기: enter, o, backspace


## Troubleshooting

LiteLLM + Z.ai 연동 후 OpenAI 코드 호출 시 잘 안될 때:
```
원인은 정확히 잡혔어. 강의의 구형 OpenAI()가 presence_penalty, frequency_penalty, logit_bias를 기본 전달하는데 Z.AI GLM은 이 파라미터들을 받지 않아 LiteLLM이 차단한 거야.

오류 진단 절차를 적용해 LiteLLM에서 지원하지 않는 파라미터만 제거하도록 설정하면, Python 코드는 그대로 둘 수 있어.
```

litellm_config.yaml에 다음 추가
```yml
 litellm_settings:
    # OpenAI -> Z.ai 시 맞지 않는 파람들은 드랍한다.
    drop_params: true
```