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
13. [Tool / function calling](#13-tool--function-calling)
    - [Tool schemas](#131-tool-schema)
    - [Model-selected tool calls](#132-model-selected-tool-calls)
    - [Tool execution loop](#133-tool-execution-loop)
    - [Gemini SDK function calling example](#134-gemini-function-calling-with-the-google-gen-ai-python-sdk)
    - [Function calling vs. structured output](#135-function-calling-is-not-the-same-as-structured-output)
14. [Python async/await and the event loop](#14-python-asyncawait-coroutines-and-the-event-loop)
    - [Detailed Python async/await guide](https://github.com/ndk123-web/cheatsheets/blob/main/Languages/python.md#practical-asyncawait-execution-coroutines-event-loops-and-concurrency)
15. [Model Context Protocol (MCP)](#15-model-context-protocol-mcp)
    - [Python import-shadowing debugging note](https://github.com/ndk123-web/cheatsheets/blob/main/Languages/python.md#practical-import-debugging-local-modules-can-shadow-packages)

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

---

## 13. Tool / Function Calling

**Tool calling** lets a model request that the application execute a function. The model does not ordinarily execute the application's Python code itself. The application or provider SDK executes the selected function and returns its result to the model when another model turn is needed.

### 13.1 Tool schema

A **tool schema** describes the function to the model: its name, purpose, input fields, types, and sometimes required fields and constraints. Schemas help the model form a well-shaped call; application-side validation and authorization are still necessary.

Example Python functions:

```python
def add(a: int, b: int) -> int:
    """Add two integers."""
    return a + b


def multiply(a: int, b: int) -> int:
    """Multiply two integers."""
    return a * b
```

Conceptual schema for `add` (the exact schema format depends on the provider):

```json
{
  "name": "add",
  "description": "Add two integers.",
  "parameters": {
    "type": "object",
    "properties": {
      "a": {"type": "integer"},
      "b": {"type": "integer"}
    },
    "required": ["a", "b"],
    "additionalProperties": false
  }
}
```

A schema is a description/contract. Registering a Python function and supplying its schema are separate implementation details unless an SDK combines them for you.

### 13.2 Model-selected tool calls

The typical lifecycle is:

1. The application sends the user request and available tool definitions to the model.
2. The model returns either a normal answer or a tool-call request containing a tool name and arguments.
3. The application validates the tool name and arguments, then invokes the corresponding function.
4. The application sends the tool result back to the model in the provider's expected message format.
5. The model can use the result to answer the user or request another tool.

The model **requests** a tool call; the application decides whether it is allowed and actually runs it. Never treat model-generated arguments as trusted authorization.

Example of a manual function registry:

```python
def add(a: int, b: int) -> int:
    return a + b


def multiply(a: int, b: int) -> int:
    return a * b


TOOLS = {
    "add": add,
    "multiply": multiply,
}


def execute_tool(name: str, arguments: dict) -> int:
    function = TOOLS.get(name)
    if function is None:
        raise ValueError(f"Unknown tool: {name}")

    # Production code should validate argument keys, types, bounds,
    # permissions, and side effects before invoking the function.
    return function(**arguments)


print(execute_tool("add", {"a": 1, "b": 2}))  # 3
```

This registry demonstrates dispatching by name. It is not a complete security validator, and a real provider integration must also handle tool-call IDs, response message formats, errors, and multiple calls.

### 13.3 Tool execution loop

A reference implementation may be SDK-managed (such as the Gemini example above) or explicitly managed by your application.

A tool-enabled application often runs a loop until the model returns a final answer or a limit is reached:

```text
Send messages + tool definitions to model
                 |
                 v
       Did model request a tool?
          /              \
        No                Yes
        |                  |
 Return final answer   Validate call
                           |
                      Execute function
                           |
                   Append tool result
                           |
                   Call model again
```

A single tool call does not necessarily finish the conversation. The model may ask for another call after seeing the first result. Apply a maximum number of rounds, timeouts, and error handling to avoid runaway loops.

**Parallelism and dependencies:** calls that are independent may be executed concurrently if the provider/application supports it and side effects are safe. Calls with dependencies must be ordered—for example, search for an order ID before retrieving the order details. Do not assume the model has correctly coordinated dependencies just because it returned several calls.

### 13.4 Gemini function calling with the Google Gen AI Python SDK

Official references:
- [Google Gen AI Python SDK — Function Calling](https://googleapis.github.io/python-genai/#function-calling)
- [Gemini API — Function Calling](https://ai.google.dev/gemini-api/docs/function-calling)

Install the current SDK:

```bash
pip install google-genai
```

Configure `GEMINI_API_KEY` (or the supported `GOOGLE_API_KEY`) in the environment. Do not hardcode API keys.

#### SDK-managed automatic function calling

The Google Gen AI Python SDK can accept Python functions directly. It derives function declarations from the function signature and docstring, detects model function calls, executes the registered Python function, sends its result to Gemini, and returns the final response by default.

```python
import os
from google import genai
from google.genai import types


def multiply(a: int, b: int) -> int:
    """Multiply two integers.

    Args:
        a: The first integer.
        b: The second integer.
    """
    return a * b


def main() -> None:
    if not os.getenv("GEMINI_API_KEY") and not os.getenv("GOOGLE_API_KEY"):
        raise RuntimeError("Set GEMINI_API_KEY or GOOGLE_API_KEY first.")

    client = genai.Client()
    try:
        response = client.models.generate_content(
            model="gemini-flash-latest",  # Choose a supported model for your account.
            contents="What is 7 multiplied by 8?",
            config=types.GenerateContentConfig(tools=[multiply]),
        )
        print(response.text)
    finally:
        client.close()


if __name__ == "__main__":
    main()
```

This illustrates the SDK-managed flow. The model selects whether to request `multiply`, and the SDK runs the function and feeds its return value back to the model. The model does not itself execute Python code. For production use, validate inputs and authorize sensitive operations even when the SDK manages the call cycle.

#### Manual function calling (application controls execution)

Disable automatic calling when you want to inspect the function-call request and decide when/how to run the function yourself:

```python
from google import genai
from google.genai import types

client = genai.Client()

def multiply(a: int, b: int) -> int:
    """Multiply two integers."""
    return a * b


question = "What is 7 multiplied by 8?"
response = client.models.generate_content(
    model="gemini-flash-latest",
    contents=question,
    config=types.GenerateContentConfig(
        tools=[multiply],
        automatic_function_calling=types.AutomaticFunctionCallingConfig(
            disable=True
        ),
    ),
)

function_calls = response.function_calls or []
if not function_calls:
    print(response.text)  # Gemini answered without asking for a tool.
else:
    call = function_calls[0]
    if call.name != "multiply":
        raise ValueError(f"Unexpected tool request: {call.name}")

    args = dict(call.args)
    # Validate argument keys, types, ranges, and permissions before invoking.
    result = multiply(**args)

    function_response = types.Part.from_function_response(
        name=call.name,
        response={"result": result},
    )
    response = client.models.generate_content(
        model="gemini-flash-latest",
        contents=[
            types.Content(role="user", parts=[types.Part.from_text(text=question)]),
            # The model's original function-call turn must also be preserved.
            response.candidates[0].content,
            types.Content(role="tool", parts=[function_response]),
        ],
        config=types.GenerateContentConfig(tools=[multiply]),
    )
    print(response.text)

client.close()
```

This is a compact one-call demonstration. A production implementation should preserve the complete conversation history, process every requested call appropriately, handle function errors, and loop until the model returns a final answer. Do not execute unknown tool names or blindly trust model-supplied arguments.

### 13.5 Function calling is not the same as structured output

| Structured output | Tool/function calling |
| --- | --- |
| Requests data in a known output format | Requests that an application execute a named operation |
| Result might be a JSON object | Result might be a function name plus arguments |
| Used for predictable data extraction/generation | Used to connect model reasoning to application capabilities |
| Does not inherently execute a function | Requires application/SDK execution and result handling |

A tool call's arguments can themselves use a schema. That does not make the tool call and the function execution the same operation.

---

## 14. Python `async` / `await`, coroutines, and the event loop

These are Python language/runtime concepts frequently seen in asynchronous SDKs, network clients, and MCP applications.

### 14.1 What does `async def` mean?

Calling an `async def` function creates a **coroutine object**. It does not run the function to completion like a normal function call.

```python
async def say_hello():
    return "Hello"


coroutine = say_hello()
# The coroutine has been created, but has not been awaited/run.
```

If a coroutine is never awaited or scheduled, Python may emit a "coroutine was never awaited" warning.

### 14.2 What does `await` mean?

Inside an asynchronous function, `await` waits for an awaitable operation. While the operation is waiting, it can suspend the current coroutine and give the event loop an opportunity to run other ready tasks.

```python
import asyncio


async def fetch_example():
    await asyncio.sleep(1)  # Simulate an asynchronous wait.
    return "Done"


async def main():
    result = await fetch_example()
    print(result)


asyncio.run(main())
```

### 14.3 What is the event loop?

The **event loop** schedules and resumes asynchronous tasks/coroutines. In a common single-threaded asyncio program, when one task awaits asynchronous I/O, the event loop can let another ready task make progress.

- `async def`: defines a coroutine function.
- `await`: suspends the current coroutine while an awaitable progresses.
- `asyncio.run(main())`: creates/manages an event loop to run the top-level coroutine, then cleans it up.
- `asyncio.gather(a(), b())`: schedules/awaits multiple awaitables so they can make progress concurrently.
- `async with`: uses an asynchronous context manager for lifecycle operations that may require awaiting setup/cleanup.

`async` and `await` are syntax, not a standalone scheduler. A coroutine must be awaited or scheduled by a running asynchronous runtime. In frameworks such as an ASGI web server, the server commonly manages the event loop; application code normally should not call `asyncio.run()` inside an already running event loop.

### 14.4 Sequential waiting versus concurrency

Sequential example:

```python
import asyncio
import time


async def task(name: str) -> None:
    print(name, "started")
    await asyncio.sleep(2)
    print(name, "finished")


async def sequential():
    await task("A")
    await task("B")


start = time.perf_counter()
asyncio.run(sequential())
print(f"Elapsed: {time.perf_counter() - start:.1f}s")  # About 4 seconds
```

Concurrent example:

```python
import asyncio
import time


async def task(name: str) -> None:
    print(name, "started")
    await asyncio.sleep(2)
    print(name, "finished")


async def concurrent():
    await asyncio.gather(task("A"), task("B"))


start = time.perf_counter()
asyncio.run(concurrent())
print(f"Elapsed: {time.perf_counter() - start:.1f}s")  # About 2 seconds
```

The sleep intervals overlap in the second example. This is **concurrency**, not CPU parallelism. Single-threaded asyncio does not execute two Python instructions at precisely the same instant. It switches between tasks when they suspend/yield. A blocking call such as `time.sleep(2)` inside an async task blocks the event-loop thread and prevents other tasks on that loop from progressing during the block.

**Important distinction:** `asyncio` does not automatically make multiple tasks concurrent. The program must schedule multiple tasks (for example, using `asyncio.gather` or `asyncio.create_task`), and the operations must yield control for other tasks to progress.

### 14.5 Why asynchronous I/O helps servers

Suppose several requests are waiting for network or database responses. An asynchronous server can suspend one request while its I/O is pending and use the event loop to progress other ready requests. This can improve concurrency and resource efficiency for I/O-bound workloads.

It does not mean every operation becomes faster. CPU-heavy work or blocking synchronous libraries can still stall a single event loop. Servers and client libraries must support asynchronous I/O for this model to work effectively.

---

## 15. Model Context Protocol (MCP)

**Model Context Protocol (MCP)** is a protocol for connecting AI applications to external capabilities and context through a standardized client/server interface. The model provider and MCP are different layers: a model may request a tool, while the application uses an MCP client to discover/call tools on an MCP server.

### 15.1 Main participants

- **MCP host:** the AI application that manages the overall experience and connections.
- **MCP client:** the component that communicates with a particular MCP server.
- **MCP server:** exposes capabilities such as tools, resources, and prompts.
- **Tool:** an operation that can be called, such as adding two numbers or searching records.
- **Resource:** data or context that a client can read using a URI.
- **Resource template:** a URI pattern for parameterized resources, such as `greeting://{name}`.

A resource is not the same as a tool: a tool performs an operation; a resource provides content. Actual client behavior and methods depend on the SDK version.

### 15.2 Create an MCP server using the Python SDK

The examples below use the newer Python SDK API with `MCPServer` and `Client`. Install the SDK in your project environment, for example:

```bash
uv add "mcp[cli]"
```

Example server file, `server.py`:

```python
from mcp.server import MCPServer

mcp = MCPServer("Demo MCP")


@mcp.tool()
def add(a: int, b: int) -> int:
    """Add two integers."""
    return a + b


@mcp.tool()
def multiply(a: int, b: int) -> int:
    """Multiply two integers."""
    return a * b


@mcp.resource("greeting://{name}")
def greeting(name: str) -> str:
    """Return a greeting for a name."""
    return f"Hello, {name}!"
```

The decorators register the Python functions as MCP capabilities. The SDK uses function metadata/type annotations to describe tool inputs, according to its supported behavior.

The URI delimiter matters: use `greeting://{name}`, not `greeting//{name}`.

### 15.3 MCP transports: Streamable HTTP versus STDIO

**Streamable HTTP:** the server runs as a separately started service and the client connects to its HTTP endpoint. A typical SDK CLI command is:

```powershell
uv run mcp run server.py --transport streamable-http
```

The client connects to the configured endpoint, commonly something like `http://localhost:8000/mcp` for a local setup.

**STDIO:** the client can start the server as a subprocess and exchange protocol messages over the process's standard input/output pipes. This usually avoids exposing an HTTP port for the local connection.

- `stdin` pipe: client sends protocol input to the child server.
- `stdout` pipe: server sends protocol output to the client.
- `stderr`: appropriate place for diagnostic output; arbitrary prints to `stdout` can corrupt the protocol stream.

The parent/child relationship is an operating-system process-management detail; MCP defines the messages exchanged over the transport.

### 15.4 MCP client with Streamable HTTP

For a server already running over HTTP, the SDK client can connect to its endpoint. Example shape:

```python
import asyncio
from mcp import Client


async def main() -> None:
    async with Client("http://localhost:8000/mcp") as client:
        result = await client.call_tool("add", {"a": 1, "b": 2})
        print(result.structured_content)


asyncio.run(main())
```

What the syntax means:

1. `async def main()` defines a coroutine.
2. `asyncio.run(main())` runs the top-level coroutine using an event loop.
3. `async with Client(...)` manages the client's asynchronous lifecycle and cleanup.
4. `await client.call_tool(...)` sends the tool request and waits for its asynchronous response.
5. `result.structured_content` accesses the structured result when the server/SDK provides it; the example `add` call returns a result equivalent to `{"result": 3}`.

### 15.5 MCP client with STDIO

In this transport, the client configuration describes the command to start rather than an HTTP URL. The client SDK launches/communicates with that server process, subject to the exact API of the installed SDK version.

Example configuration concept:

```python
server = StdioServerParameters(
    command="uv",
    args=["run", "server.py"],
)
```

The server process must start the MCP protocol on STDIO. Use the current SDK documentation for the exact import and client constructor supported by your installed version; avoid mixing examples from older SDK APIs such as `FastMCP`/`ClientSession` with the newer `MCPServer`/`Client` API.

### 15.6 Why might `list_resources()` return `resources=[]`?

A fixed resource and a resource template are different:

```python
@mcp.resource("docs://getting-started")
def get_docs() -> str:
    return "Welcome to the documentation."
```

This is a fixed URI resource, which should appear in the ordinary resource listing if registered and exposed correctly.

```python
@mcp.resource("greeting://{name}")
def greeting(name: str) -> str:
    return f"Hello, {name}!"
```

This is a **resource template**. A client should inspect resource templates using the matching method (often named `list_resource_templates()`) rather than expecting it to appear as a fixed item from `list_resources()`. To read a specific instance, use a URI such as `greeting://World` with the SDK's resource-reading method.

For example, in SDK versions whose API matches this shape:

```python
templates = await client.list_resource_templates()
print([item.uri_template for item in templates.resource_templates])

result = await client.read_resource("greeting://World")
```

Always check the installed SDK version's documentation for exact result fields and method signatures. If no fixed resources are registered, `resources=[]` can be expected even though one or more resource templates exist.

### 15.7 Common MCP debugging issue: a local file shadows the package

Avoid naming your own script `mcp.py` if you need to import the installed `mcp` package. Python may import your local file instead of the installed package, causing errors such as:

```text
ModuleNotFoundError: No module named 'mcp.server'; 'mcp' is not a package
```

Rename the local file (for example, `server.py`), remove a stale `__pycache__` entry if needed, and run from the correct project environment.

### 15.8 How model tool calling and MCP work together

A simplified end-to-end flow can look like this:

```text
User asks a question
       |
       v
Application sends prompt + model tool definitions
       |
       v
Model returns a tool-call request
       |
       v
Application / SDK routes the request to an available capability
       |
       v
MCP client sends the protocol request to an MCP server
       |
       v
MCP server executes the tool and returns a result
       |
       v
Application sends the result back to the model
       |
       v
Model generates a user-facing response (or requests another tool)
```

This diagram is conceptual. Not every SDK directly converts a model tool call into an MCP call automatically; your application may need an adapter that maps model tool names and arguments to MCP tools, handles results/errors, and sends the result back to the model.

### 15.9 Interview recap: tools, resources, and transports

**Related Python reference:** for deeper explanations of coroutine objects, `await`, event loops, sequential versus concurrent tasks, and `async with`, see the [Python Cheatsheet: Practical Async/Await Execution](https://github.com/ndk123-web/cheatsheets/blob/main/Languages/python.md#practical-asyncawait-execution-coroutines-event-loops-and-concurrency). For package import shadowing, see [Practical Import Debugging](https://github.com/ndk123-web/cheatsheets/blob/main/Languages/python.md#practical-import-debugging-local-modules-can-shadow-packages).


| Concept | Remember |
| --- | --- |
| Tool schema | Describes a callable operation and its argument shape |
| Model tool call | A request from the model; the application still executes/authorizes it |
| Tool execution loop | Send tool definitions, receive call, execute, return result, continue model turn |
| MCP client | Connects to an MCP server and makes protocol requests |
| MCP server | Exposes tools, resources, prompts, or other supported capabilities |
| Tool | Performs an operation |
| Resource | Provides data/context at a URI |
| Resource template | URI pattern with parameters |
| Streamable HTTP | Client communicates with an HTTP-based MCP endpoint |
| STDIO | Client and server can communicate through process pipes |
| `asyncio` event loop | Runs and coordinates Python coroutines/tasks |
| `async with` | Manages an asynchronous context/lifecycle |
| `await` | Suspends the current coroutine while an awaitable completes |

**Security reminder:** treat tool arguments and resource contents as untrusted input. Validate arguments, authorize sensitive actions, set timeouts, constrain file/network access, and handle errors explicitly.

### Official references

- [Google Gen AI Python SDK — Function Calling](https://googleapis.github.io/python-genai/#function-calling)
- [Gemini API — Function Calling](https://ai.google.dev/gemini-api/docs/function-calling)
- [MCP Python SDK v2 — Getting Started](https://py.sdk.modelcontextprotocol.io/)
- [MCP Python SDK v2 — Client](https://py.sdk.modelcontextprotocol.io/client/)
- [MCP Python SDK v2 — Client Transports](https://py.sdk.modelcontextprotocol.io/client/transports/)

