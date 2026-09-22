---
tags:
  - nomad-coder
  - python
  - langchain
  - model-io
---
## Introduction
* Model I/O
	* prompts
	* language models
	* output parsers
* Retrieval
	* 외부 데이터 접근해서 가져오는 것
* Chains
* Agents
* Memory: 중간에 알 수 있도록.. 컨텍스트 저장소??
* Callbacks: model 이 무엇을 하는지 중간에 알 수 있게 하는 것.
## FewShotPromptTemplate
LLM이 응답할 결과의 형식을 미리 정해놓는것.
```python
from langchain.chat_models import ChatOpenAI
from langchain.prompts import PromptTemplate
from langchain.prompts.few_shot import FewShotPromptTemplate
from langchain.callbacks import StreamingStdOutCallbackHandler

chat = ChatOpenAI(
    temperature=0.1,
    streaming=True,
    callbacks=[
        StreamingStdOutCallbackHandler(),
    ],
)

examples = [
    {
        "country": "France",
        "answer": """
                Here is what I know:
                Capital: Paris
                Language: French
                Food: Wine and Cheese
                Currency: Euro
                """,
    },
    {
        "country": "Italy",
        "answer": """
                    I know this:
                    Capital: Rome
                    Language: Italian
                    Food: Pizza and Pasta
                    Currency: Euro
                """,
    },
    {
        "country": "Greece",
        "answer": """
                I know this:
                Capital: Athens
                Language: Greek
                Food: Souvlaki and Feta Cheese
                Currency: Euro
                """,
    },
]

example_template = """
    Human: What do you know about {country}?
    AI: {answer}
"""

example_prompt = PromptTemplate.from_template(example_template)
prompt = FewShotPromptTemplate(
    example_prompt=example_prompt,
    examples=examples,
    suffix="Human: What do you know about {country}",
    input_variables=["country"],
)

chain = prompt | chat
print(
    chain.invoke(
        {
            "country": "Korea",
        }
    )
)
```
## FewShotChatMessagPromptTemplate
## LengthBasedExampleSelector
## Serialization and Compositon
## Caching
## Serialization
