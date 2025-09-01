# Convert model
```console
python convert_hf_to_gguf.py models-custom/Qwen2-VL-2B-Instruct --outtype f32
```

# Convert model + quantize
```console
python convert_hf_to_gguf.py models-custom/Arch-Agent-3B --outfile models-custom/Arch-Agent-3B-F16.gguf --outtype f16
./build/bin/llama-quantize models-custom/Arch-Agent-3B-F16.gguf models-custom/Arch-Agent-3B-Q4_K_M.gguf Q4_K_M
./build/bin/llama-quantize models-custom/Arch-Agent-3B-F16.gguf models-custom/Arch-Agent-3B-Q4_K_S.gguf Q4_K_S
```

```console
python convert_hf_to_gguf.py models-custom/Arch-Agent-1.5B --outfile models-custom/Arch-Agent-1.5B-F16.gguf --outtype f16
./build/bin/llama-quantize models-custom/Arch-Agent-1.5B-F16.gguf models-custom/Arch-Agent-1.5B-Q4_K_M.gguf Q4_K_M
./build/bin/llama-quantize models-custom/Arch-Agent-1.5B-F16.gguf models-custom/Arch-Agent-1.5B-Q4_K_S.gguf Q4_K_S
```

```console
python convert_hf_to_gguf.py models-custom/xLAM-2-3b-fc-r --outfile models-custom/xLAM-2-3b-fc-r-F16.gguf --outtype f16
./build/bin/llama-quantize models-custom/xLAM-2-3b-fc-r-F16.gguf models-custom/xLAM-2-3b-fc-r-Q4_K_S.gguf Q4_K_S
```

# Run model
```console
llama-cli -m ./models-custom/Qwen2-VL-2B-Instruct-F16.gguf -cnv -p "You are a helpful assistant"
```

# Run multimodal model

```console
llama-mtmd-cli -m ./models-custom/Qwen2-VL-2B-Instruct-F16.gguf --mmproj ./models-custom/mmproj-Qwen2-VL-2B-Instruct-f16.gguf -p 'Describe this image.' --image './models-custom/cat_angry.jpg'
llama-qwen2vl-cli -m ./models-custom/Qwen2-VL-2B-Instruct-F16.gguf --mmproj ./models-custom/mmproj-Qwen2-VL-2B-Instruct-f16.gguf -p 'tell me a joke'
```

## Run Gemma 3

Source: https://huggingface.co/google/gemma-3-12b-it-qat-q4_0-gguf

```console
./build/bin/llama-mtmd-cli -m models-custom/gemma-3-12b-it-q4_0.gguf --mmproj models-custom/mmproj-model-f16-12B.gguf
```

```console
./build/bin/llama-cli -m models-custom/gemma-3-270m-it-F16.gguf -p "<|im_start|>system\nyou are a helpful assistant to tell the intension from the users, when the user asks for something, list the user's intensions with bullet points, make sure it shows 'which person', wants to do 'what' and 'how', if possible<|im_end|>\n<|im_start|>user\nI want to check the weather in seattle tomorrow<|im_end|>"
./build/bin/llama-cli -m models-custom/gemma-3-270m-it-Q4_K_S.gguf -p "tell me a joke"
```

With image

```console
./build/bin/llama-mtmd-cli -m models-custom/gemma-3-12b-it-q4_0.gguf --mmproj models-custom/mmproj-model-f16-12B.gguf --image ./surprise.png
```

## Run embedding model (Not working)
./build/bin/llama-embedding -m models-custom/jina-embeddings-v2-base-code-Q6_K.gguf -e -p "Hello world" --verbose-prompt -ngl 99


# Run model with tools

Model options:
* Arch-Agent-1.5B-Q4_K_S.gguf
* Arch-Agent-3B-Q4_K_S.gguf
* Arch-Function-3B-Q4_K_S.gguf
* xLAM-2-1B-fc-r-Q4_K_S.gguf
* xLAM-2-3b-fc-r-Q4_K_S.gguf

Sample with tool definition in JSON:

```console
llama-cli -m models-custom/xLAM-2-1B-fc-r-Q4_K_S.gguf \
--color \
-c 4096 \
--temp 0.7 \
--repeat_penalty 1.1 \
-n -1 \
-p "You are a helpful assistant with access to the following tools:

tools:
- name: get_current_weather
  description: Get the current weather in a given location.
  parameters:
    location: (string) The city and state, e.g., San Francisco, CA.
    unit: (string, optional) The unit of temperature, either \"celsius\" or \"fahrenheit\".

You can use these tools to answer user questions. When you need to use a tool, respond with a JSON object in the following format:
{
  \"tool_name\": \"name of the tool\",
  \"tool_args\": { \"argument1\": \"value1\", \"argument2\": \"value2\" }
}
Do not use any other format. If you can answer without a tool, respond directly to the user.

User: what is the weather in Seattle? Answer with unit of fahrenheit
Assistant: "
```

Sample with tool definition in XML:

```console
llama-cli -m models-custom/Arch-Agent-1.5B-Q4_K_S.gguf \
--color \
-c 4096 \
--temp 0.7 \
--repeat_penalty 1.1 \
-n -1 \
-p "You are a helpful assistant.

# Tools

You may call one or more functions to assist with the user query.

You are provided with function signatures within <tools></tools> XML tags:
<tools>
{\"type\": \"function\", \"function\": {\"name\": \"get_weather\", \"description\": \"Get the current weather for a location\", \"parameters\": {\"type\": \"object\", \"properties\": {\"location\": {\"type\": \"str\", \"description\": \"The city and state, e.g. San Francisco, New York\"}, \"unit\": {\"type\": \"str\", \"enum\": [\"celsius\", \"fahrenheit\"], \"description\": \"The unit of temperature to return\"}}, \"required\": [\"location\"]}}}
</tools>

For each function call, return a json object with function name and arguments within <tool_call></tool_call> XML tags:
<tool_call>
{\"name\": <function-name>, \"arguments\": <args-json-object>}
</tool_call>

user:
what is the weather in Seattle?"
```
