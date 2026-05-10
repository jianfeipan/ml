LLM Inference Stack



---

## 1. The Hardware Layer (Physical Infrastructure)
At the base are the accelerators. While NVIDIA GPUs (H200, B200) remain dominant, the 2026 landscape is increasingly heterogeneous.
* **Accelerators:** NVIDIA GPUs, Google TPUs, AWS Trainium/Inferentia, and Apple Silicon (for edge).
* **Interconnects:** NVLink and Ultra Accelerator Link (UALink) are critical. Because LLMs are often too large for one chip, the speed at which GPUs "talk" to each other often dictates the generation speed.
* **Memory:** High Bandwidth Memory (HBM3e/4) is the most precious resource, as it stores the model weights and the **KV Cache**.

## 2. The Kernel & Runtime Layer (Hardware Abstraction)
This layer translates mathematical operations (like matrix multiplication) into code the hardware understands.
* **Optimized Kernels:** Tools like **FlashAttention-3** and **PagedAttention** minimize memory access.
* **Runtimes:** * **NVIDIA TensorRT-LLM:** The gold standard for squeezing max performance out of NVIDIA hardware.
    * **ONNX Runtime:** Used for cross-platform portability.
    * **vLLM:** A popular open-source engine that treats GPU memory like an Operating System treats RAM (using "paging" to prevent fragmentation).



## 3. The Inference Engine (The Brain)
This is where the magic of "Continuous Batching" and "Speculative Decoding" happens.
* **Continuous Batching:** Unlike traditional batching, which waits for all requests to finish, continuous batching lets new requests join the queue the moment a previous one finishes a token.
* **Speculative Decoding:** A small, fast "draft" model guesses the next tokens, and the large "target" model verifies them in one go. This can speed up inference by 2x–3x.
* **Disaggregated Prefill/Decode:** A 2026 standard practice where one set of GPUs handles the initial "reading" of your prompt (Prefill) and another set handles the "writing" of the answer (Decode), optimizing for different compute needs.

## 4. The Orchestration Layer (Serving)
This layer handles traffic, scaling, and reliability.
* **Frameworks:** **Ray Serve**, **Triton Inference Server**, and **llm-d** (a CNCF project).
* **Key Tasks:** * **Auto-scaling:** Adding more GPUs when traffic spikes.
    * **LoRA Exchange:** Swapping out small "adapter" weights (LoRAs) on the fly so one base model can act like many different specialized models (e.g., one for coding, one for legal).

## 5. The Application/Agent Layer (User Interface)
The top of the stack where the model connects to your data.
* **RAG (Retrieval-Augmented Generation):** Connecting the model to a vector database (like Pinecone or Milvus) to provide real-time facts.
* **Function Calling/Agents:** Allowing the model to "talk" to APIs (e.g., "Check the weather" or "Update the CRM").

