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
