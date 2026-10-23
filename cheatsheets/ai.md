# AI / LLM Engineering Cheatsheet

> **Study log — 23 October 2026**
>
> Notes from today's learning session. This sheet covers the core concepts needed to build a structured-output AI application with Python, a provider SDK, Pydantic, and the Python standard library.

## Contents

1. [LLM application flow](#1-llm-application-flow)
2. [Tokens](#2-tokens)
3. [Context windows](#3-context-windows)
4. [Inference](#4-inference)
5. [Prompts and message roles](#5-prompts-and-message-roles)
6. [Model parameters](#6-model-parameters)
7. [Structured outputs and Pydantic](#7-structured-outputs-and-pydantic)
8. [Provider SDK and a structured-output example](#8-provider-sdk-and-a-structured-output-example)
9. [Streaming](#9-streaming)
10. [API errors and retries](#10-api-errors-and-retries)
11. [Useful Python standard-library modules](#11-useful-python-standard-library-modules)
12. [Key distinctions and interview recap](#12-key-distinctions-and-interview-recap)

---

## 1. LLM application flow

A typical LLM application follows this path:

```text
User input
   -> Python application builds a request
   -> Provider SDK sends the API request
   -> Model processes context and performs inference
   -> Model generates output
   -> Application parses and validates the response
   -> Application returns or stores the result
```

The **LLM** provides the generation capability. The **application** handles input validation, API calls, response parsing, business logic, errors, and presentation.

Example project for this sheet: an AI study assistant that accepts a question and returns a topic, difficulty, explanation, and revision questions.

## 2. Tokens

A **token** is a unit of text processed by a language model. Depending on the tokenizer, a token may be a complete word, part of a word, punctuation, or another text fragment. A token is not necessarily one word or one character.

Example (illustrative only):

```text
"The model is learning"
    -> ["The", " model", " is", " learning"]
    -> token IDs
```

Why tokens matter:

- **Cost:** many API providers price requests using input and output token usage.
- **Context:** model requests have token limits.
- **Latency:** generating more tokens generally takes longer.

For accurate counts, use the tokenizer appropriate to the model or inspect the provider's usage metadata. Do not assume one word equals one token.

Example using `tiktoken` and the `cl100k_base` encoding:

```python
import tiktoken

text = "The model is learning"
encoding = tiktoken.get_encoding("cl100k_base")
token_ids = encoding.encode(text)

print("Token IDs:", token_ids)
print("Token count:", len(token_ids))
print("Token pieces:", [encoding.decode([t]) for t in token_ids])
```

Install with `pip install tiktoken`. Note: `cl100k_base` is an example encoding; it is not the tokenizer for every model.

## 3. Context windows

A **context window** is the amount of tokenized information a model can process for a request. Depending on the model and API, it may include:

- System/developer instructions and user messages.
- Conversation history included in the request.
- Retrieved documents or other supplied context.
- Space used or reserved for generated output.

Illustrative budget (assume a model/request limit of 8,000 tokens):

| Component | Tokens |
| --- | ---: |
| Instructions | 500 |
| Conversation history | 2,000 |
| Retrieved documents | 3,000 |
| Current question | 500 |
| Output allocation | 1,000 |
| Total | 7,000 |

This leaves about 1,000 tokens of headroom, assuming these components count toward the same limit.

A model does not automatically have unlimited access to every past conversation. An application generally needs to send relevant history or retrieve relevant information.

Ways to manage context:

- Keep only relevant messages.
- Summarize older conversation history.
- Retrieve relevant document chunks instead of sending an entire knowledge base (RAG).
- Limit input and output sizes.

**Remember:** context window is processing capacity for supplied context, not permanent memory.

## 4. Inference

**Training** adjusts a model's learned parameters using data and an optimization process. **Inference** uses the trained model to produce a prediction or response for an input.

During simplified LLM inference:

1. The request's text is tokenized.
2. The model processes the input using learned parameters.
3. The model computes a probability distribution for the next token.
4. A decoding strategy selects a token.
5. The process repeats until a stopping condition or output limit is reached.

For example, given `The capital of France is`, a model may assign a high probability to `Paris`.

An ordinary LLM API call is usually **inference**, not training. The model generates from learned patterns and supplied context; it does not necessarily search a database for each answer. Retrieval and tool use can be added around the model.

## 5. Prompts and message roles

A **prompt** is the input provided to the model. It can contain instructions, questions, examples, data, and conversation history.

Weak prompt:

```text
Explain AI.
```

More specific prompt:

```text
Explain artificial intelligence to a third-year computer engineering student.
Use simple language, give one practical example, explain the working process,
and end with three revision questions.
```

Specific instructions make the task and expected response clearer, but prompting alone cannot guarantee correctness.

Common chat message roles (availability and precedence vary by provider/API):

| Role | Purpose |
| --- | --- |
| `system` | High-level behavioral instructions, where supported |
| `developer` | Application-level instructions, where supported |
| `user` | User request or supplied information |
| `assistant` | Model messages |
| `tool` | Tool results |

Example:

```python
messages = [
    {
        "role": "system",
        "content": "You are a technical tutor. Explain concepts accurately and simply.",
    },
    {"role": "user", "content": "Explain supervised learning."},
]
```

If a follow-up says "Give me an example", the model can interpret it using earlier messages only if the relevant context is included in the request or otherwise made available.

## 6. Model parameters

The word *parameters* can refer to two different things.

### Learned model parameters

These are numerical weights and other learned values inside a neural network. Training adjusts them. They generally are not changed by an ordinary inference API request.

### Inference/generation settings

These control aspects of generation. Names and availability differ between APIs.

| Setting | Meaning |
| --- | --- |
| `temperature` | Controls randomness in sampling; lower values generally favor more predictable output |
| `max_output_tokens` | Limits the length of generated output |
| `top_p` | Restricts sampling to a cumulative probability mass |
| `stop` | Configures stopping sequences, where supported |

Temperature does not directly measure intelligence or factual accuracy. Higher temperature does not automatically improve creativity, and lower temperature does not guarantee truth. Some constrained-output endpoints limit which sampling settings can be used.

## 7. Structured outputs and Pydantic

**Structured output** means asking a model to return data in a known shape, often JSON, rather than only free-form prose. This makes results easier to validate, store, display, or pass to another program.

Example expected data:

```json
{
  "topic": "Neural Networks",
  "difficulty": "beginner",
  "explanation": "A neural network learns patterns from data.",
  "revision_questions": [
    "What is a neuron?",
    "What are weights?",
    "What is an activation function?"
  ]
}
```

**Pydantic** validates Python data against a declared model, including field types and constraints.

Install: `pip install pydantic`

```python
from typing import Literal
from pydantic import BaseModel, Field

class StudyAnswer(BaseModel):
    topic: str
    difficulty: Literal["beginner", "intermediate", "advanced"]
    explanation: str
    revision_questions: list[str] = Field(min_length=3, max_length=5)
```

Validate a Python dictionary:

```python
from pydantic import ValidationError

data = {
    "topic": "Neural Networks",
    "difficulty": "beginner",
    "explanation": "A neural network learns patterns from data.",
    "revision_questions": [
        "What is a neuron?",
        "What are weights?",
        "What is an activation function?",
    ],
}

try:
    answer = StudyAnswer.model_validate(data)
    print(answer.model_dump_json(indent=2))
except ValidationError as exc:
    print("Invalid data:", exc)
```

If `difficulty` is `"expert"`, validation fails because it is not an allowed literal.

Two separate layers:

1. **Provider-side structured generation:** the API guides or constrains the model to a schema.
2. **Application-side validation:** Pydantic checks the received data against application requirements.

Still handle refusals, incomplete responses, API failures, and semantically incorrect content. Valid structure does not prove factual correctness.

## 8. Provider SDK and a structured-output example

A **provider SDK** is a library that makes it easier to call a model provider's API from code. It handles request construction and response objects, and often provides typed interfaces and error classes.

Example using the OpenAI Python SDK's Chat Completions parsing interface. Model availability and API support can change; use a model that supports structured outputs in your account.

Install:

```bash
pip install openai pydantic
```

Set `OPENAI_API_KEY` in your environment; do not hardcode secrets in source code.

```python
import os
from typing import Literal

from openai import OpenAI
from pydantic import BaseModel, Field


class StudyAnswer(BaseModel):
    topic: str
    difficulty: Literal["beginner", "intermediate", "advanced"]
    explanation: str
    revision_questions: list[str] = Field(min_length=3, max_length=5)


def main() -> None:
    if not os.getenv("OPENAI_API_KEY"):
        raise RuntimeError("OPENAI_API_KEY is not configured")

    question = input("Ask your AI tutor: ").strip()
    if not question:
        print("Please enter a question.")
        return

    client = OpenAI()
    response = client.chat.completions.parse(
        model="gpt-4o-2024-08-06",
        messages=[
            {
                "role": "system",
                "content": (
                    "You are a technical study assistant. Explain concepts accurately "
                    "and simply. Provide 3 to 5 revision questions."
                ),
            },
            {"role": "user", "content": question},
        ],
        response_format=StudyAnswer,
    )

    answer = response.choices[0].message.parsed
    if answer is None:
        print("No structured answer was returned.")
        return

    print(answer.model_dump_json(indent=2))


if __name__ == "__main__":
    main()
```

Flow:

1. Read and validate the user's input.
2. Define the expected output contract with Pydantic.
3. Send instructions and the question through the provider SDK.
4. Request a schema-constrained response.
5. Read the parsed Pydantic object.
6. Use the validated data in the application.

The code demonstrates the main flow; production code must additionally handle provider exceptions, refusals, incomplete responses, and timeouts.

## 9. Streaming

Without streaming, the application generally receives the completed response once it is ready. With **streaming**, the API sends response events or chunks as generation progresses, allowing the UI to show partial output.

Streaming improves perceived responsiveness for long responses, but does not necessarily reduce the total computation required. Chunks are not guaranteed to align with whole words.

Example using the OpenAI SDK:

```python
from openai import OpenAI

client = OpenAI()
stream = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "user", "content": "Explain inference in simple terms."}
    ],
    stream=True,
)

for chunk in stream:
    if chunk.choices:
        text = chunk.choices[0].delta.content
        if text:
            print(text, end="", flush=True)

print()
```

For structured output, do not assume each chunk is valid JSON. Accumulate and process events according to the provider's streaming protocol, then parse/validate the complete result before relying on it.

## 10. API errors and retries

An API call can fail even when the application code is correct.

| Failure | Typical meaning | Handling |
| --- | --- | --- |
| Authentication error | Missing/invalid credentials | Fix credentials; do not blindly retry |
| `429` | Rate limit | Respect retry guidance; back off when appropriate |
| `5xx` | Provider/server failure | Retry eligible transient errors |
| Timeout/connection error | Response did not arrive in time | Retry selectively |
| `400` | Invalid request or parameters | Correct the request |
| Pydantic validation error | Data does not satisfy the schema | Handle, reject, or repair the data |
| Refusal/incomplete output | No usable completed answer | Handle as a distinct outcome |

**Retry** means sending a failed request again. Do not retry every error: an invalid key will remain invalid.

**Exponential backoff** increases the delay between attempts (for example, 1 second, 2 seconds, then 4 seconds). **Jitter** adds a small random delay to reduce synchronized retries across clients.

Illustrative retry helper for selected transient failures:

```python
import random
import time

from openai import OpenAI, APIConnectionError, APIStatusError

client = OpenAI(max_retries=0)


def call_model_with_retry():
    max_attempts = 3

    for attempt in range(max_attempts):
        try:
            return client.chat.completions.create(
                model="gpt-4o-mini",
                messages=[
                    {"role": "user", "content": "Explain machine learning."}
                ],
            )
        except APIConnectionError:
            retryable = True
        except APIStatusError as exc:
            retryable = exc.status_code == 429 or exc.status_code >= 500
            if not retryable:
                raise

        if attempt == max_attempts - 1:
            raise RuntimeError("API call failed after retries")

        delay = min(2 ** attempt, 8) + random.uniform(0, 0.5)
        time.sleep(delay)


response = call_model_with_retry()
print(response.choices[0].message.content)
```

This is a teaching example, not a complete production retry system. It does not implement `Retry-After` handling or detailed logging. Follow provider guidance and avoid stacking custom retries on top of SDK retries.

A timeout can leave uncertainty about whether the provider processed the request. For operations with side effects, use idempotency mechanisms where supported. Repeated model requests can also incur additional charges.

## 11. Useful Python standard-library modules

| Module | Use |
| --- | --- |
| `os` | Read environment variables and configuration |
| `json` | Encode/decode JSON |
| `time` | Wait between retry attempts |
| `random` | Add retry jitter |
| `logging` | Record application events and errors |
| `typing` | Type hints and interfaces |
| `dataclasses` | Simple data containers |
| `unittest` | Automated tests |

Pydantic and provider SDKs are external dependencies; the modules above are included with Python.

Example logging setup:

```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

logger.info("Starting AI study assistant")
logger.warning("The response was incomplete")
```

Do not log API keys, sensitive prompts, or private user data indiscriminately.

## 12. Key distinctions and interview recap

- **Token:** a tokenizer's unit of text processed by a model.
- **Context window:** the token capacity available for the request's context and output, according to the model/API limits.
- **Training:** learning/updating model parameters from data.
- **Inference:** using a trained model to generate a prediction or response.
- **Prompt:** instructions, questions, and context supplied to the model.
- **Learned parameters:** numerical values learned during training.
- **Generation settings:** controls such as temperature and output-token limits.
- **Structured output:** generating data in a predefined format/schema.
- **Pydantic:** validates Python data against declared types and constraints.
- **Provider SDK:** a programming interface for calling a model provider's API.
- **Streaming:** receiving response chunks progressively.
- **Retry with backoff:** retrying eligible transient failures after increasing delays.
- **Important:** valid JSON/schema does not guarantee factual accuracy; robust applications need error handling and tests.

### One-line system flow

```text
Prompt -> Tokenization -> Context processing -> Inference -> Output
       -> Parsing/validation -> Application logic -> User
```

### Next topics to study

- Unit testing the AI client and mocking provider responses.
- Semantic validation and safe handling of refusals/incomplete output.
- Tool/function calling and retrieval-augmented generation (RAG).
- Logging, timeouts, usage tracking, and production configuration.
