## Homework: Open-Source LLMs

In this homework, we'll experiment more with Ollama

> It's possible that your answers won't match exactly. If it's the case, select the closest one.

Solution: https://www.loom.com/share/f04a63aaf0db4bf58194ba425f1fcffa

## Q1. Running Ollama with Docker

Let's run ollama with Docker. We will need to execute the 
same command as in the lectures:

```bash
docker run -it \
    --rm \ # 运行结束后删除
    -v ollama:/root/.ollama \ # 本地ollama 挂载容器路径
    -p 11434:11434 \
    --name ollama \
    ollama/ollama

docker exec -it ollama bash
```


What's the version of ollama client? 

To find out, enter the container and execute `ollama` with the `-v` flag.


## Q2. Downloading an LLM 

We will donwload a smaller LLM - gemma:2b. 

Again let's enter the container and pull the model:

```bash
ollama pull gemma:2b
```

In docker, it saved the results into `/root/.ollama`

We're interested in the metadata about this model. You can find
it in `models/manifests/registry.ollama.ai/library`

What's the content of the file related to gemma?

## Q3. Running the LLM

```
ollama run gemma:2b

ctrl + d to exit
```

Test the following prompt: "10 * 10". What's the answer?

## Q4. Donwloading the weights 

We don't want to pull the weights every time we run
a docker container. Let's do it once and have them available
every time we start a container.

First, we will need to change how we run the container.

Instead of mapping the `/root/.ollama` folder to a named volume,
let's map it to a local directory:

```bash
mkdir ollama_files # in the working dir

docker run -it \
    --rm \
    -v ./ollama_files:/root/.ollama \
    -p 11434:11434 \
    --name ollama \
    ollama/ollama
```

Now pull the model:

```bash
docker exec -it ollama ollama pull gemma:2b 
```

What's the size of the `ollama_files/models` folder? 

* 0.6G
* 1.2G
* 1.7G
* 2.2G

Hint: on linux, you can use `du -h` for that.

## Q5. Adding the weights 

Let's now stop the container and add the weights 
to a new image

For that, let's create a `Dockerfile`:

```dockerfile
FROM ollama/ollama

COPY ./ollama_files /root/.ollama
```

What do you put after `COPY`?

## Q6. Serving it 

Let's build it:

```bash
docker build -t ollama-gemma2b .
```

And run it:

```bash
docker run -it --rm -p 11434:11434 --name ollama ollama-gemma2b

docker exec -it ollama bash
```

We can connect to it using the OpenAI client

Let's test it with the following prompt:

```python

ipython

prompt = "What's the formula for energy?"
```

Also, to make results reproducible, set the `temperature` parameter to 0:

```bash
from openai import OpenAI

client = OpenAI(
    base_url='http://localhost:11434/v1/',
    api_key='ollama',
)

response = client.chat.completions.create(
    model='gemma:2b',
    temperature=0.0,
    messages=[{"role": "user", "content": prompt}]
)

response.choices[0].message.content
```

How many completion tokens did you get in response?

* 304
* 604
* 904
* 1204

## Submit the results

* Submit your results here: https://courses.datatalks.club/llm-zoomcamp-2024/homework/hw2
* It's possible that your answers won't match exactly. If it's the case, select the closest one.
