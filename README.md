# Docker_Athens_Meetup_2025_May
Docker Athens Meetup Presentation on docker runners embedded to VSCode Continue Extension

What are Docker Model Runners, so how they run as containers, what images are currently available and after that show the PoC with Continue extension and model running on top to create a Cursor like IDE.  

## Continue promt example: 

- Create me inside @test.py a fastapi hello world example. Can you also add tags for each endpoint?



## Graph on how Model Runner Feature works:

```mermaid
flowchart LR
  subgraph Developer Environment
    DevCLI[Developer CLI<br/>docker model ...]
    DevApp[Developer App<br/>e.g. LangChain]
  end

  subgraph subGraph1["Docker Desktop"]
    CLIPlugin[Docker CLI Plugin<br/>docker-model]
    Dashboard[Docker Desktop Dashboard]
    InferenceSvc[Inference Engine<br/>llama.cpp]
    Cache[Local Model Cache<br/>OCI artifacts]
  end

  subgraph Docker Hub
    Hub[Docker Hub<br/>ai/… namespace]
  end

  subgraph Host Resources
    CPU[CPU]
    GPU[GPU]
    RAM[System RAM]
  end

  %% Pulling models
  DevCLI -->|model pull| CLIPlugin
  CLIPlugin -->|fetch OCI artifact| Hub
  Hub -->|OCI artifact| Cache

  %% Running inference
  DevCLI -->|model run| CLIPlugin
  CLIPlugin -->|invoke| InferenceSvc
  InferenceSvc -->|load model<br/>from cache| Cache
  InferenceSvc -->|use| CPU
  InferenceSvc -->|use if available| GPU
  InferenceSvc -->|allocate| RAM

  %% API endpoints
  InferenceSvc -->|serve OpenAI-compatible API| DevApp
  InferenceSvc -->|expose in UI| Dashboard

  %% Dashboard interactions
  Dashboard -->|pull/run/rm| CLIPlugin
  Dashboard -->|chat UI| InferenceSvc
	style subGraph3 color:#000000,fill:#A6A6A6
	style subGraph0 color:#000000,fill:#FFBD59
	style subGraph1 color:#000000,fill:#38B6FF,stroke:#FF3131,stroke-width:2px

```


## Presentation flow:

Enable Docker model runners
In the Continue desktop app, turn on the “Docker model runners” feature.

(Optional) Restart the engine
– Not needed in our setup, but generally you’d restart the engine after enabling new features.

Install the Continue extension
Download and install the “Continue” extension for your IDE or editor.

Create your config.yaml
Path: 

```bash
~/​.continue/config.yaml (on macOS: /Users/<USER_NAME>/.continue/config.yaml)
```

Pull a model via Docker CLI

```bash
docker model pull <model>
– Browse available models on Docker Hub: https://hub.docker.com/u/ai
```

Example:

```bash
docker model pull ai/mistral
```
(You can also pull Hugging Face models if you prefer.)

Register the model in config.yaml
Under models:, add:

models:
  - name: docker_athens_qwen
    provider: openai
    model: ai/llama3.2:1B-Q8_0
    apiBase: http://localhost:12434/engines/llama.cpp/v1
    roles:
      - chat
      - edit
      - apply
Add the model to Continue
In the Continue UI, click the three-dots menu → box icon, then fill in the form fields with your model’s details.

Create a FastAPI starter (main.py)

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "hello docker athens"}
```

Run your FastAPI app

```bash
uvicorn main:app --host 0.0.0.0 --port 8000
```