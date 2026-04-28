# Convert model + quantize
```console
# Create and enter folder `models-custom`
git clone https://huggingface.co/google/gemma-4-E2B-it
```

```console
python convert_hf_to_gguf.py models-custom/gemma-4-E2B-it --outfile models-custom/gemma-4-E2B-it-F16.gguf --outtype f16
```

```console
./build/bin/llama-quantize models-custom/gemma-4-E2B-it-F16.gguf models-custom/gemma-4-E2B-it-Q4_K_M.gguf Q4_K_M
```

# Run model
```console
./build/bin/llama-cli -m ./models-custom/gemma-4-E2B-it-Q4_K_M.gguf -cnv -p "What is in the image" --reasoning off --flash-attn on
```

Run server:
```console
./build/bin/llama-server -m ./models-custom/gemma-4-E2B-it-Q4_K_M.gguf --port 8080
```

Test:
```console
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      {"role": "system", "content": "You are a helpful assistant."},
      {"role": "user", "content": "Generate JSXGraph code, it will create a sin graph"}
    ]
  }'
```


# Run multimodal model

```console
./build/bin/llama-mtmd-cli -m ./models-custom/Qwen2-VL-2B-Instruct-F16.gguf --mmproj ./models-custom/mmproj-Qwen2-VL-2B-Instruct-f16.gguf -p 'Describe this image.' --image './models-custom/cat_angry.jpg'
```

# Tutorials
## tutorial : compute embeddings using llama.cpp
https://github.com/ggml-org/llama.cpp/discussions/7712

