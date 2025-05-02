---
title: "Structuring LLM Outputs"
date: 2025-05-01
excerpt: ""
collection: miniblogs
---

## Getting Structured Output from LLMs using LangChain + Bedrock


So the other day I was trying to get a **Python object as output from an LLM**. Let's assume, I wanted it to give me a `Person(name, age, email)` object instead of plain text.
I started with a simple technique to achieve this using Langchain's tool calling API.


## Function Calling

First, we define the function that will receive structured arguments:

```python
from langchain_core.tools import tool

class Person:
    def __init__(self, name, age, email):
        self.name = name
        self.age = age
        self.email = email

@tool
def create_person(name: str, age: int, email: str) -> str:
    person = Person(name=name, age=age, email=email)
    return f"Created person object: {person}"
```

Wire it up to AWS Bedrock:

```python
from langchain_aws import ChatBedrockConverse
from langchain_core.messages import HumanMessage, FunctionMessage

llm = ChatBedrockConverse(
    model="anthropic.claude-3-5-sonnet-20240620-v1:0",
    temperature=0,
    max_tokens=None,
    region_name='us-east-1'
)

messages = [HumanMessage(content="Create a person with name Alice, age 30, email alice@example.com")]
response = llm.invoke(messages, tools=[create_person], tool_choice="auto")

if response.tool_calls:
    for tool_call in response.tool_calls:
        tool_output = create_person.invoke(tool_call["args"])
        messages.append(FunctionMessage(name=tool_call["name"], content=tool_output))
        final_response = llm.invoke(messages)
        print(final_response.content)
```

Output:
```
Created person object: Person(name='Alice', age=30, email='alice@example.com')
```

It worked. But, this is just an example. The actual object I had in mind was way more complex and had a nested structure. It felt like a lot of work for something this simple.
Turns out, LangChain provides us with a nifty trick to do this. The best part about this approach is that you can enforce types using [Pydantic](https://docs.pydantic.dev/latest/) in this method using a wrapper that was built on top of it.

---

## Structured Output

LangChain has a method called: `with_structured_output()`. You can just define a class that extends the basemodel of the Langchain Pydantic module.

Define the expected structure:

```python
from langchain_core.pydantic_v1 import BaseModel, Field

class Person(BaseModel):
    name: str = Field(description="Name of the person")
    age: int = Field(description="Age of the person")
    email: str = Field(description="Email address")
```

Use structured output:

```python
llm = ChatBedrockConverse(
    model="anthropic.claude-3-5-sonnet-20240620-v1:0",
    temperature=0,
    max_tokens=None,
    region_name='us-east-1'
)
structured_llm = llm.with_structured_output(Person, include_raw=True)

response = structured_llm.invoke("Create a person with name Alice, age 30, and email alice@example.com")

print(response.get('parsed'))
```

And it's as simple as that!
```
Person(name='Alice', age=30, email='alice@example.com')
```

---

If you check the model invocation logs, you'll see that the tool calling was done internally to achieve this. I assume it's somewhat similar but a more sophisticated version of what I tried to do initially.


Here is the link to the Documentation: [Link](https://python.langchain.com/docs/concepts/structured_outputs/)

## When Should You Use What?
If you need the model to actually trigger real logic (like saving to a database or calling an API), go with function calling.
If you just want structured data, function calling is probably overkill — structured output is way cleaner.