# AI training and inference architecture

Training: Data preparation -> Pre-training (base model) -> Post-training (Fine-tuning, Alignment) -> Evaluation & compression 

Inference: Pre-processing -> Prefill(KV Cache, TTFT) -> Decoding -> Sampling

## Training

### Data Preparation

- Common crawl (books, Wikipedia, etc.), cleaning & filtering.
- Tokenization: breaking text into tokens.

### Pretraining

- Model Architecture: 
  - Encoder-decoder (e.g., T5): processes input and generates output simultaneously.
  - Encoder-only (e.g., BERT): focuses on understanding input, not generating output.
  - Decoder-only (e.g., GPT): focuses on generating output based on input.
- Objective:
    - Masked language modeling (MLM): predict masked tokens in the input.
    - Causal language modeling (CLM): predict the next token in a sequence.
- output: model weights for **base model**. (base model can only predict the next token)

### post-training

- SFT(Supervised Fine-Tuning): fine-tune on specific tasks (e.g., question answering, summarization) using labeled datasets.
- Alignment/RLHF(Reinforcement Learning with Human Feedback): fine-tune using human feedback to align model behavior with human preferences
    - reward model: trained to predict human preferences.
    - reinforcement learning: optimize the model to maximize the reward signal from the reward model.

### Evaluation & compression

- Evaluation: benchmark on tasks like language understanding, generation, reasoning.
    - MMLU(Massive Multitask Language Understanding)
    - GSM8K (grade school math problems)
    - HumanEval (code generation tasks)
    - Red teaming: adversarial testing to identify model weaknesses and biases.
- Compression: 
    - Quantization: reduce precision of model weights (e.g., from 16-bit to 8-bit) to save memory and speed up inference.
    - Pruning: remove less important weights or neurons to reduce model size and improve efficiency.

## Inference

### Pre-processing

- Tokenization
- Embedding

### Prefill

- The model reads the entire prompt in one shot, processing every token in parallel.
- Builds the internal state: the KV Cache.
- Calculate the first token of the response. (TTFT: time to first token)

### Decoding
Autoregressive: generate one token at a time, feeding the output back into the model as input for the next token.

- Paged attention: only attends to the most recent tokens, not the entire history.
- Logits: the raw output logits for next token.

### Sampling
- Softmax: converts logits into probabilities.
- Top-k sampling: restricts the next token choices to the top k most probable tokens.
- Temperature: controls randomness in sampling. Higher temperature = more random.
- Put the newly generated token back into the model as input for the next token generation.

