This guide organizes your notes into a structured curriculum covering advanced AI orchestration, from prompt engineering to Graph-based memory and the Model Context Protocol (MCP).

---

## 🏗️ 1. Prompting Architectures

Modern LLMs respond differently based on the formatting of the input.

* **Alpaca Style:** Uses clear headers for `Instruction`, `Input`, and `Response`.
* **ChatML (OpenAI Standard):** Uses a list of JSON objects with `role` (system, user, assistant) and `content`.
* **Inst Prompting (Mistral/Meta):** Uses tags like `[INST]` or `<|system|>` to wrap logic.

---

## 🤖 2. Agentic AI & Systems Integration

An **Agent** is an LLM (the brain) attached to APIs/Services (the hands and legs).

### The Architecture

* **Services:** Order, Auth, Shipping, and Payment services.
* **Databases:** MongoDB (Payments) and PostgreSQL (Shipping).
* **The Goal:** A "Support Agent" that isn't just a chatbot, but can query the DB and call the Shipping service to cancel an order.

### Asynchronous Tasks with Redis Queue (RQ)

When an agent performs a heavy task (like generating a report), it shouldn't block the API.

* **Producer:** FastAPI sends a message to the queue.
* **Redis/Valkey:** Acts as the message broker.
* **Worker/Consumer:** A background process picks up the task and saves the result to the DB.

---

## 📈 3. LangGraph & Graph-Based Memory

While standard chains are linear, **LangGraph** allows for complex, loopy workflows.

### LangGraph Components

* **State:** A shared object (e.g., `{"input": str, "output": str}`) passed between nodes.
* **Nodes:** Functions that perform work and update the state.
* **Edges:** Define the flow (e.g., if the LLM says "Wait," route back to a tool node).
* **Checkpoints:** Snapshots of the thread state at a specific time, allowing for "human-in-the-loop" approvals.

### Knowledge Graphs (Neo4j)

Vector DBs are great for similarity, but they struggle with **relationships**.

* **Vector DB:** "Who is John?" -> Finds text about John.
* **Knowledge Graph:** "Who is John's manager at Company X?" -> Follows the `WORKS_AT` and `REPORTS_TO` edges.
* **Cypher Query Example:**
```cypher
MERGE (u:User {name: "Piyush"})
MERGE (c:Company {name: "AWS"})
MERGE (u)-[:WORKS_AT]->(c)
RETURN u, c

```



---

## 🎙️ 4. Conversational AI (Voice Agents)

There are two primary ways to build voice-capable agents:

| Feature | Chained Architecture (STT -> LLM -> TTS) | Speech-to-Speech (S2S) |
| --- | --- | --- |
| **Logic** | Transcribe ➔ Process ➔ Synthesize | Native audio processing |
| **Latency** | Higher (Multiple steps) | Low (Real-time) |
| **Intelligence** | High (Uses full LLM power) | Lower (Current models) |
| **Nuance** | Loses tone/emotion in text | Captures intent/emotion |

---

## 🔌 5. Model Context Protocol (MCP)

MCP is the **"USB-C for AI."** It is an open-source standard that allows developers to connect AI models to data sources (Google Drive, Slack, Databases) using a universal interface.

* **MCP Host:** The app (e.g., VS Code or Claude Desktop).
* **MCP Client:** The bridge inside the host.
* **MCP Server:** The program providing the data (e.g., a Postgres MCP server).

---

## 📊 6. Database Appendix: SQL Reference

Commonly used queries for technical interviews and backend data retrieval.

### Nth Highest Salary

**Option 1: Using Dense Rank**

```sql
WITH RankedSalaries AS (
    SELECT salary, DENSE_RANK() OVER(ORDER BY salary DESC) as r
    FROM Employee
)
SELECT salary FROM RankedSalaries WHERE r = :N;

```

**Option 2: Using Limit/Offset**

```sql
SELECT DISTINCT Salary 
FROM Employee 
ORDER BY Salary DESC 
LIMIT 1 OFFSET M; -- Where M = N-1

```



--------------------------------------------
## OLLAMA

These are platform depndent 

#### DOCKER : 
    docker desktop :: 
    docker --version 
    docker pull busybox
    docker run busybox ls
    docker container ps -a
    docker container rm c_id

#### Running OLLAma model with docker runner 
ollama github docker image 

docker run ollama 
open web ui  

#### Configuring open web UI for OLLAMA backend 
search open web  ui docker 

pull the image of open web ui 
run the container 
     signup     
    admin panel

    pull a model 
    ollama run gamma : 2b 

### 131. FastAPI Environment Setup & Dependencies
pip install "fastapi[standard]"


## !32 132. Integrating Ollama with FastAPI & Python APIs


## 133. Hugging Face Model Deployment – Section Intro
     - The github of llm models  where you can push your LLM model 
    Fine Tunning a model or building a model from ground up ie gpt3 gemini, so this is a place where u can upload ur model and other community can use it . ie github for LLM model 



## 134. Configuring and Securing Hugging Face Account

## 135. Accessing Instruct-Tuned Models (Google Gemma)
Its a gated model (need permissions)


## 136. Installing and Using Hugging Face CLI Tools

# Section 20
### Agentic AI fundamentals  section Intro


SYSTEM_PROMPT = """
For every tool call wait for the observe step which is the output fom the called tool. 

"""

"""
You are a expert AI Assistant in resolving user queries using chain of thought.
You work on start, plan and output steps.
You need to first plan what needs to done. The plan can be multiple steps.
Once you thin enough PLAN has been done, finally you can give an output.
You can also call a tool if required from the list of available tools 
for every tool call wait for the observe step which is the output from the called tool.


Rules : 
- Strictly follow the given JSON output format 
- Only run one step at a time .
- The sequence of steps is START (whaere user gives an input ), PLAN (That can be multiple times)


Output JSON Format : 
{ "step" : "START" | "PLAN" | "OUTPUT" ,"content": "string" , "tool":"string", "input":"string" }


Available Tools : 
- get_weather(city:str): Takes cityname as an input string and return the weather info about city.

Example 2:
START : What is the weather of Delhi ?
PLAN : {"step":"PLAN", "content" : "Seems like a user is interested in getting a weather of Delhi in India."}
PLAN : {"step":"PLAN", "content" : "Lets see if we have any available tool from the list of tools "}
PLAN : {"step":"PLAN", "content" : "Great, we have get_weather tool availablefor this query. "}
PLAN : {"step":"PLAN", "content" : "I need to call get weather tool for delhi as input for city."}
PLAN :{ "step":"TOOL", "tool":"get_weather","input" : " delhi", "output":"the temperature of Delhi was cloudy with 20 C." }
PLAN : {"step":"PLAN", "content" : "Great, I got weather Info about Delhi"}
OUTPUT: {"step":"OUTPUT", "content" : "the current weather in delhi is 20 C with some cloudy sky." }


"""
### 143. Intro to RAG & LangChain – Section Overview

    RAG :==:
    bussiness :: lot of textual data and hard to read them 
    sol : build some ai agent using LLM.
    it read the files and  can able to search relevant content and reply in better format .
    hence, it can give u a proper answer about what u have ask , and give relevant page.

    Limitations :
    1. LLM  doesnt have context about your private data in your files
    2.  you cant give 1000 files, because of context window("feed limited context window ") if u does this , it will work same for every query, (1M token Window)
    3. "COST" 





### 144. Defining the Core Problem in RAG Systems

RAG :
    1. Indexing Phase ---> User provide the data 
    2. Retrival Phase ---> User chatting with the data 

    1 indexing Phase : 
         few documents on that start indexing 
         process : 
         1. chunking (based on para or page)
         2. check what kind of data it holds.
         3. give every chunk to an embedding model and create an "vector embedding" for every chunk.
            use loop that give all chunk to vector model and create vector embedding 
         4. Use "vector DB" to hold vector Embeddings 
            what u can store in Vector DB :

             I .actual content
                chunk-A : vector, metadata: {}
                chunk-B : vector, metadata: {}
                chunk-C : vector,  metadata: {} .....

             II. Metadata :
             
                page No , paragraph belongs to which document.

        Q. How can we use these indexing in retrival phase with  your 1000 files 

        
### 147. RAG Pipeline – Retrieval Mechanism in Depth

    2. Retrival Phase : 
    Q. How the retrival happens ::
    - user provide a query ––> 
    - Convert these into vector Embedding using Embedding Model --> 
    - Vector Embedding(query)
    - throw Query (Vector Simi Search) to Vector DB -->
    - hence vector DB  return only relevant chunks. 
    - drop vector , only get actual chunk 
    - use chat model like gpt3 and give extracted data
    - Also Pass user original query to gpt3 


**(we pass relevant data and user query to the LLM  so it can reply back to the user.)**

### 148. Local Vector DB Setup with Docker Compose

 - Configure vectorDB (QdrantDB)
 - setup qdrant DB on cloud or locally using docker.
 - 

### 149. LangChain Installation & Setup
    LangChain : Provides you utility tools out of the box 
    gives prebuilt fuctions, utilities 
    1. to read the document from the file , 
    2. code to convert these document into chunk , langchain have built in function 
    3. chunk need to send to openai and langchain have fun 
    4. collect qdrant db    

SetUP langchain : use document 
    Load an PDF use pypdf 
    pip install langchain pypdf langchain-community

python -m pip freeze > requirements.txt

### 150. LangChain Document Loaders for PDF


### 151. LangChain Document Chunking & Splitting

 search langchain chunk splitter

 ### 152 
 search langchain for openai embeddings 





It looks like you’ve compiled a comprehensive set of study notes or a curriculum outline for building modern LLM applications. I have organized and formatted these into a clean, logical structure using a professional technical hierarchy to make them easier to reference.

---

## 🏗️ LLM Infrastructure & Deployment

### 1. Local LLM Hosting with Ollama

Ollama allows you to run LLMs locally. Setup is platform-dependent.

* **Docker Integration:**
* **Verify Installation:** `docker --version`
* **Test Environment:** `docker pull busybox` -> `docker run busybox ls`
* **Cleanup:** `docker container ps -a` (view all) -> `docker container rm <c_id>` (remove)


* **Running Models:**
* Pull the official Ollama image from GitHub/Docker Hub.
* Run the container and access the Open WebUI for a ChatGPT-like interface.
* **CLI Run:** `ollama run gemma:2b`



### 2. FastAPI Integration

To expose your models via an API:

* **Installation:** `pip install "fastapi[standard]"`
* **Purpose:** Seamlessly bridge Python logic/Ollama backends with front-end applications.

### 3. Hugging Face Ecosystem

The "GitHub of LLMs"—a central hub for model sharing and deployment.

* **Model Hosting:** Push fine-tuned or ground-up models (like GPT-3 or Gemma) for community use.
* **Gated Models:** Certain models (e.g., Google Gemma) require account permissions and a Hugging Face token for access.
* **Tooling:** Use `huggingface-cli` for local model management and uploads.

---

## 🤖 Section 20: Agentic AI Fundamentals

Agentic AI relies on **Chain of Thought (CoT)** reasoning, where the AI plans before acting.

### The Reasoning Loop (START ➔ PLAN ➔ OUTPUT)

The agent follows a strict system prompt to handle tool calls and observation steps.

**System Prompt Logic:**

1. **START:** Receive user input.
2. **PLAN:** Break down the query into logical steps.
3. **TOOL/OBSERVE:** Call external tools (e.g., `get_weather`) and wait for the output.
4. **OUTPUT:** Provide the final synthesized response in a structured JSON format.

> **Output Format:**
> `{ "step" : "START" | "PLAN" | "OUTPUT", "content": "string", "tool":"string", "input":"string" }`

---

## 📚 Retrieval-Augmented Generation (RAG) & LangChain

RAG solves the "knowledge cutoff" and "private data" limitations of LLMs.

### The Core Problem

* **Private Data:** LLMs aren't trained on your internal files.
* **Context Windows:** You can't feed 1,000 files into a single prompt (too expensive and noisy).
* **Solution:** RAG only feeds the *most relevant* snippets to the LLM.

### The Two-Phase Pipeline

| Phase | Steps |
| --- | --- |
| **1. Indexing** | Document ➔ Chunking ➔ Embedding Model ➔ Vector DB (Store content + Metadata). |
| **2. Retrieval** | User Query ➔ Embedding ➔ Vector Similarity Search ➔ Extract Chunks ➔ LLM Response. |

### LangChain Implementation

LangChain provides the "glue" code to automate the pipeline:

1. **Document Loaders:** Use `pypdf` to read PDF files.
2. **Text Splitters:** Divide documents into manageable chunks.
3. **Embeddings:** Send chunks to OpenAI or Hugging Face embedding models.
4. **Vector Store:** Connect to **QdrantDB** (running locally via Docker) to store and search vectors.

**Installation:**

```bash
pip install langchain pypdf langchain-community
python -m pip freeze > requirements.txt

```

----------------------------------------------------------------







## Alpaca Prompt:: 

### Instructions : <SYSTEM_PROMPT>
### Input : <USER_QUERY>

### Response : \n




# ChatML
{
    "role" : "system" | "user"| assistant",
    "content" : "string"
}
<!-- openai use chatML prompting style -->



# INST Prompting 

[INTS] what is the time now ? [/INTS]

<|system|>
 instructions  here,
</|system|>

OLLAMA Overview ::  

An open source model which you can run your model offline 

--> it needs good amount of hardware 

Q. How can we run these model offline in our machine   
#### needs Docker and OLLAMA 




 ### 132 Integrating OLLAMA with Fastapi 

agentic ai
    order svc 

    auth svc 

    shipping svc 


    mongodb :- payment service and , order service interacting with db 
    
    postgress db : shipping and auth service interact with 
    
    to add some support system (handle manually )

    the agent have access to 
        shipping svc, and payment to support better

    
    **support agent** - sitting idle: whenever they get a query , it access our private info, to support ypu better, 
        it cancel your order using these services.

**gpt and gemini**: are LLm model : text to text model :
    it will take input token and predict next set of output tokens.

task : convert these LLM to an Agent 
Agent have capability to access the servers , authservice , lookup in db , talk to  user 

How ?
add some additional layers 
NLP ::  

e.g.,
brain + hands + legs =ready to perform an action. called agent

## 140 How to build the body and attatch to it LLM:
1. build weather agent 
hey tell me weather of goa LLM:
<make an api call> weather api , llm respond 


### 146 Rag pipeline :: 
1. indexing 
2. retrival 



## 158 

Queue in system design
    server handle handle 1 req at a time others stored in queue , 
    processor pick up the task done it and send res . 
    called async queue

   server -- processor/consumer 
   processor pick up a task at a time process it , stored result in db ,
   - server just read the  ready response, give ti user 

## 159 redis setup 
 - valkey (drop in replacement for redis )

*What is RQ * ? 
Redis queue package lib 
- pip install rq 
python -m pip freeze > requirements.txt

- 1. Producers produces msg into the queue
2. 

## 160 Asynchronous Message Api 


## 163 what is multi model ai   ?
It should take input not only as text but also audio, video, images


## Langgraph ::
workflow --


user_query  --  planning  yes/No 
no -- LLM call for response  
avoiding nested conditions and loops, messy code 

- we can able to structure your code like a graph 

node , edges , functions 

State {
    input : str
    output: str
}

every node return a new state 

graph invocation  

for chatbot maintain a state     


## Implementing checkpoint in langgraph 
Checkpoints : The state of thread at a point in time is called  a checkpoint .
It a snapshot of agraph state saved at each super step and represented by object with the following key properties.

### Graph Memory and Knowledge graph in ai agents 
what is graph memory , neo4j ??
what is Knowledge graph ? 


the directed graph and undirected graph 
directed graph a one way relation 
eg.
A --> parent --> B

undirected graph ==> a 2 way relation

c <---> friend <---> d


Q. why graph memory is needed ?
how graph can helps up to build better memory ?

memory storage vectorDB      
vector embedding cant store relations ,
hence we use graphs 
eg


alex owns company X 
 company X have emp john
 company X have emp max 
 here the knowledge graph show sementic r   ealations between entities 
- it gives information about relaionships. 


*cypher Query*:
how to spin up the cloud instance of neo4j 

### Basics of cypher query ::

create a node of type user with name piyush 

CREATE (u:User {name:"Piyush"})
RETURN u;

CREATE (u:Company { name:"AWS"})
RETURN u;

MATCH (n) RETURN n


create a relation between user and company

MERGE (u:User {name: "Piyush"})
MERGE (c:Company {name: "R"})
MERGE (u): [:WORKS_AT] ->(c)
RETURN u,c

(u:User {name:"ABC"}):[Employee] ->(c:Company {name:"BBB"})


MATCH p=() - [EMPLOYEE] -> () RETURN p LIMIT 25

MATCH   (u:User {name:"ioio"})
MATCH (c:Company {name: "klkdjf"})

MERGE (u)-[:Employee] -> (c)

!!! Delete the Node by id
MATCH (n)
WHERE elementId(n) = '4:fefdfsdfdfsff:10'


## 195. Adding Graph Database Support for Memory Agent

## 196 Tessting graph memory implementation in Agent. 


#  Section : 28 
## 197 : Section Intro to Conversational Agentic AI

-> what is voice agent , what is chain architecture , voice to voice 

-> text tokens : as input   
instead of text , u can feed voice audio
-- till date there is no model which can take voice as input . YPU CANT input voice in llm 
why ?
--> Transformer architecture (powers all these agents ) 
number as input  --> predict next set of tokens 

voice == wave == spectrum 

There are two patterns to builds voice agents
Speech to Speech  (S2S)
Chained Architecture(conversational ai)

**what is s2s ?**
    Native audio handling by the model using runtime api 
    user voice as input and feed to the model and u get a speech .
    - a multimodel s2s arch directly processes audio inputs and op , handling speech in real time in a single multimodel model, gpt-40 is preview   .
    - the model thinking and response in speech.
    - It doesnt relay on transcripts of the user inputs, it here emotions and intent , filters out noise and responds directly in speech , Use these approach for highly interactive low latency conversational use cases 

    - uoy can build a specific agent for customer supports , customer call , it is not structured conversation  low latency converasation .
    catch :: 
    - very expensive architecture 
    - it bind with specific model.
    - low intelligence


## Understanding chain model/pattern 
**what is Chained Architecture ?**
--> speech to text(stt) (transcribing ) ---- gpt --- text -- voice response (tts)

catch 
 - low latency (need to perform multiple steps) little time to respond back 
 - 


## Setup stt 
 - Speech to text 


##  OpenAI GPT Completions for Chained Agent

##  Setup TTS conversation  
-- search for openai tts 



## Model context protocol ::
- -> MCP what it is ? 
    LLM component is constant
    what tool , system prompt you provide , which make it unique for other company , how combine them important part 

    -- > mcp standardize these things 
    USB-C Universal cable [charge + data transfer] 
    - where u bring  your tool and connect to your llm using likeusbc interface 
    eg. google build  40-50 tools 
        - read email 
        - send email 
    Tweeter:
     - post tweet
     - repost tweet 
     - reply_tweet
    build theses tools On the basis of MCP Layer/protocol.

    the llm agent like gpt-4.1  -- using MCP --to connect these tools 
    - A mcp is a common interface for connecting these kind of tools.
    i can leverage prebuild tools to connect to my model in a standardize way.

    - because of mcp other model (gemini) can also connect these tools , usb-c is common. 



MCP Standard Docs ::
--> A new standard way for connecting 
AI assistant to the systems where data lives , including content repository, business tools , and developements envs. 
--> it aims is to help frontier models produce more better , more relevant responses.

--> MCP is used to get data postgress , MongoDb, Google search MCP , Snow fleak kafka 
-- > data source requires it own custom implementation, making truely conected system   difficult to scale 
__ MCP provide openstandard for connection ai system with data sources replacing fragmented interaction with a single protocol

*MCP is an open source protocol that standardizes how application provide the context to LLM *
- THink of MCP is like a USBC port for application 
- A standardize way to connect AI Model with different data sources.

There are mainly 3 three components : 
    - MCP Host : the application that coordinate and manage one or multiple MCP clients
    - MCP Cilent : A component that maintains  connection to an MCP server and obtain context 
                from an MCP Server   for MCP host to use .
    - MCP  Server : A program that provide context to MCP clients    

The MCP client is running inside MCP host , eg., vs code 

--> problem behind MCP:















**nthHighest salary**
select * from ifnull((
    select name , salary DENSE_RANK() OVER(ORDER BY salary DESC) as r 
    from EMP
), null) as subquery
where r = 3 ;

select * from (
    select * from emp 
    ORDER BY salary DESC
    limit 6;
 ) as T 
ORDER BY T.salary asc
limit 1



select * from emp
where sal = (
    select min(sal) from emp
    where sal in (
        select distinct sal 
        from emp
        order by sal desc
        limit 4;
    )
 );


 CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  RETURN (
      # Write your MySQL query statement below.
   with rankedSalaries as (
    select salary,
    dense_rank() over(order by salary dsec) as RankNo
    from Employee
   ) 
   select disinct salary,
   from RankedSalaries where 
   RankNo = N

  );
END


 declare M INT;
  set M = N - 1;
  RETURN (
    # Write your MySQL query statement below.
    select distinct Salary
    from Employee
    order by Salary desc
    limit 1 offset M);


178. Rank Scores









leverage -- using (something) to maximum advantage 