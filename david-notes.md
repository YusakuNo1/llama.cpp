# How to run in Mac OSX with Silicon
* Compile it in Mac OSX

# How to run the server
* In folder `./build/bin`
* Run `./llama-server -m ../../models/[gguf-model-name].gguf`

# Convert HF to GGUF
## Download HF
Use "git clone" from model in HF

## (Deprecatd) Download HF
Source: https://www.geeksforgeeks.org/how-to-convert-any-huggingface-model-to-gguf-file-format/
```
from huggingface_hub import snapshot_download

# model_id = "microsoft/Phi-3-mini-4k-instruct"  # Replace with the ID of the model you want to download
# snapshot_download(repo_id=model_id, local_dir="phi3")

model_id = "meta-llama/Llama-3.3-70B-Instruct"  # Replace with the ID of the model you want to download
snapshot_download(repo_id=model_id, local_dir="llama3.3_70B")

# model_id = "meta-llama/Llama-3.2-3B-Instruct"
# snapshot_download(repo_id=model_id, local_dir="llama3.2_3B")
```

## Convert
`python ../convert_hf_to_gguf.py ./phi3 --outfile phi3_q8.gguf --outtype q8_0`
`python ../convert_hf_to_gguf.py ./Phi-4-mini-instruct --outfile phi_4_mini_instruct_q8.gguf --outtype q8_0`
`python ../convert_hf_to_gguf.py ./llama3.2_3B --outfile llama3.2_3B_q2.gguf --outtype q2_0`

## Run with server
* In folder `./build/bin`
* Run `./llama-server -m ../../david_tests/phi3_q8.gguf`

# How to build for iOS (Apple)

* Command: `./build-xcframework.sh`
* Draft `build-apple/llama.xcframework` to xCode

# iOS vs llama-cli

## How to build llama-cli with Xcode:
* Create folder `build.my_macos`
* In folder `build.my_macos`, run `cmake -G Xcode ..`

## init from iOS
(Swift) LlamaState::init()
* (Swift) loadDefaultModel
    * (Swift) loadModel
        * (Swift) LlamaContext::create_context
            * (C++) llama_backend_init
                * (C++) ggml_init
            * (C++) llama_model_default_params
            * (C++) llama_model_load_from_file
            * (C++) llama_context_default_params
            * (C++) llama_init_from_model
            * (Swift) LlamaContext::init() 

## chat from iOS
(Swift) LlamaState::complete
* (Swift) llamaContext::completion_init
    * (Swift) llamaContext::tokenize
        * (C++) llama_tokenize
    * (C++) llama_n_ctx
    * (C++) llama_batch_clear
    * (C++) llama_batch_add
    * (C++) llama_decode
* (Swift) llamaContext::completion_loop
    * (C++) llama_sampler_sample
    * (Swift) token_to_piece
        * (C++) llama_token_to_piece
    * (Swift) llama_batch_clear
    * (Swift) llama_batch_add
    * (C++) llama_decode
* (Swift) llamaContext::clear

## init & chat from llama-cli
* (C++) main
    * (C++) common_init -> only for logging
    * (C++) llama_backend_init
        * (C++) ggml_init
    * (C++) llama_numa_init
    * (C++) common_init_from_params
        * (C++) common_model_params_to_llama
        * (C++) llama_model_load_from_file
        * (C++) llama_model_get_vocab
        * (C++) common_context_params_to_llama
        * (C++) llama_init_from_model
    * (C++) llama_get_memory
    * (C++) llama_model_get_vocab
    * (C++) common_chat_templates_init
    * (C++) ggml_backend_dev_by_type

