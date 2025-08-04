# Convert model
```console
python convert_hf_to_gguf.py models-custom/Qwen2-VL-2B-Instruct --outtype f32
```

# Run model
```console
llama-cli -m ./models-custom/Qwen2-VL-2B-Instruct-F16.gguf -cnv -p "You are a helpful assistant"
```

# Run multimodal model

```console
llama-qwen2vl-cli -m ./models-custom/Qwen2-VL-2B-Instruct-F16.gguf --mmproj ./models-custom/mmproj-Qwen2-VL-2B-Instruct-f16.gguf -p 'Describe this image.' --image './models-custom/angry_cat.jpg'
llama-qwen2vl-cli -m ./models-custom/Qwen2-VL-2B-Instruct-F16.gguf --mmproj ./models-custom/mmproj-Qwen2-VL-2B-Instruct-f16.gguf -p 'tell me a joke'
```

## Run Gemma 3

Source: https://huggingface.co/google/gemma-3-12b-it-qat-q4_0-gguf

```console
./build/bin/llama-mtmd-cli -m models-custom/gemma-3-12b-it-q4_0.gguf --mmproj models-custom/mmproj-model-f16-12B.gguf
```

With image

```console
./build/bin/llama-mtmd-cli -m models-custom/gemma-3-12b-it-q4_0.gguf --mmproj models-custom/mmproj-model-f16-12B.gguf --image ./surprise.png
```

# Run model with tools
```console
llama-cli -m models-custom/Arch-Function-3B-Q8_0.gguf \
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
</tool_call>"
```
