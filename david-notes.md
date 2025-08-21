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