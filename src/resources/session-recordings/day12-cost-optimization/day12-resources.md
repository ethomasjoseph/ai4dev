# Cost Optimization

## Resources & Links

- [Google Colab Notebook 1](https://colab.research.google.com/drive/1XEgofNi_Tj9axBAdUQMR6YDyZgegQHBw?usp=sharing)
- [Google Colab Notebook 2](https://colab.research.google.com/drive/1ADQws6Mt7Kzwp1ewmaKuBps-UGhRU1Xr?usp=sharing)
- [OpenAI Cookbook: Batch Processing](https://developers.openai.com/cookbook/examples/batch_processing)
- [Gemini API Documentation: Long Context](https://ai.google.dev/gemini-api/docs/long-context)
- [fal.ai Documentation](https://fal.ai/docs/documentation)
- [Together AI](https://www.together.ai/)
- [Labelbox Guide: Needle in a Haystack Test for LLM Evaluation](https://labelbox.com/guides/unlocking-precision-the-needle-in-a-haystack-test-for-llm-evaluation/)
- [Hugging Face: InternVL 3.5 Flash Collection](https://huggingface.co/collections/OpenGVLab/internvl35-flash)
- [Hugging Face: InternVL 3.5 Collection](https://huggingface.co/collections/OpenGVLab/internvl35)
- [Microsoft Presidio Documentation](https://microsoft.github.io/presidio/)
- [Hugging Face: OpenAI Privacy Filter](https://huggingface.co/openai/privacy-filter)
- [Polo Club: Transformer Explainer](https://poloclub.github.io/transformer-explainer/)
- [Post Read Session Summary](https://docs.google.com/document/d/1smJxpVvL-LJhZ9FHGtavs_dI84JaMyGLNyWYwU2MOaM/edit?tab=t.yfpm0hciba95)

---

## Session Notes

### Part 1: API Cost and Pricing for Generative AI (Pre-Break)

Based on the session before the break, here is a summary of what Sid covered:

**Main Topic: API Cost and Pricing for Generative AI**  
Sid explained how costs work when building enterprise AI applications using APIs (not consumer subscriptions like ChatGPT or Claude Code). Key points included:

#### 1. Basic Cost Structure

- Pricing is based on tokens (input and output).
- Prices are published as dollars per million tokens (`MTOK`).
- Output tokens are generally more expensive than input tokens.
- **Formula:** `Cost = Price × Usage`

#### 2. Multi-turn Conversations

Using a simple example (asking about France's capital and writing paragraphs), Sid demonstrated:

- Each turn sends the entire conversation history as input.
- System prompts are included in every request.
- Token costs accumulate quickly with longer conversations.
- Input costs are typically 3–5x higher than output in regular models.

#### 3. Model Types

- **Regular models:** Input cost dominates.
- **Reasoning/Thinking models:** Output cost dominates (2–4x more expensive overall).
- Thinking models generate extra tokens for reasoning before answering.

#### 4. Multimodal Pricing

- **Images:** Converted to patches (16×16 or 32×32 pixels), charged per patch with possible base charges and multipliers.
- **Audio:** Charged per second (e.g., Gemini charges 32 tokens/second), typically mono channel at 16kHz.
- **Video:** Combination of audio + 1 frame per second as images.
- **Embeddings:** Charged only for input tokens, not output dimensions.

#### 5. Additional Pricing Factors

- **Usage-based pricing:** Higher rates after 200K tokens context.
- **Priority tiers:** Faster responses at higher cost.
- **Different labs pricing:** Different labs have different pricing (Gemini currently cheapest for flagship models).

*Sid also recommended specific models like Qwen 3 for embeddings and Scribe V2 for Indic language transcription, and briefly showed LM Studio for running local models.*

---

### Part 2: Cost Saving and Optimization (Post-Break)

#### Summary Overview 1

Based on the meeting transcript after the break, here is a summary of the key topics covered:

##### Cost Saving and Optimization

1. **Caching** (Primary cost-saving method discussed):
   - Labs cache calculations from previous tokens to avoid reprocessing.
   - Provides **10x discount** on input tokens (e.g., $5 becomes $0.50 per million tokens).
   - OpenAI, Gemini, and Grok do this automatically.
   - Anthropic requires manual setup and charges extra for cache storage.
   - Cache matches token-by-token from the start; if the first token mismatches, the entire cache is invalidated.
   - Minimum token length required (typically 1,024 tokens for OpenAI).

2. **Batch API** (Second major cost-saving option):
   - **50% discount** if you can wait up to 24 hours for results.
   - Great for bulk processing (embeddings, image captioning, etc.).
   - **Process:** Create batch file (`JSONL`) → Upload → Create batch → Poll for results → Download results.
   - Can combine with caching for maximum savings.
   - Not available on OpenRouter.

##### Additional Topics

1. **Platform Recommendations:**
   - **OpenAI Cookbook:** Excellent production-ready code examples.
   - **Gemini Cookbook:** Also good with Colab notebooks.
   - **Voyage AI** (now MongoDB): Specialized embedding models for finance, law, code.
   - **fal.ai** and **Together.ai:** For image/video generation.

2. **Local LLM Options:**
   - **LM Studio:** Recommended over Ollama (faster, better UI).
   - **Gemma 4** (2B and 4B): Highly recommended for basic tasks.
   - **Qwen 3:** For embeddings.
   - **DeepSeek OCR:** For handwriting recognition.
   - **Quantization** (`Q4`, `Q6`, `Q8`): Reduces VRAM requirements.

3. **Audio Processing Tip:**
   - Speed up human speech audio by 1.1x–1.5x before transcription to save 30–50% on costs.

> The session emphasized that caching and batching are the two biggest levers for cost savings.

---

#### Summary Overview 2

Based on the meeting transcript after the break, here is a summary of what was covered:

##### Cost Saving Methods

1. **Caching** (Primary cost-saving technique discussed):
   - Labs cache calculations done on conversation history to avoid reprocessing.
   - Can offer 10% to 10x discount on input tokens.
   - Most labs (OpenAI, Gemini, Grok) do this automatically.
   - Anthropic is the exception – requires manual setup and charges extra.
   - Cache hits occur when token sequences match from the beginning.
   - Typical discount is 10x (e.g., $5 becomes $0.50 per million tokens).
   - Minimum token requirements apply (usually 1,024+ tokens).

2. **Batch API** (Second major cost-saving option):
   - For bulk processing where you can wait up to 24 hours.
   - Offers 50% discount in most cases.
   - Great for large datasets like generating embeddings or captioning images.
   - **Process:** Create batch file (`JSONL`) → Upload → Create batch → Poll for results → Download results.
   - Not available on OpenRouter; must use official APIs.
   - Can be combined with caching for maximum savings.

##### Additional Topics Covered

- **Audio Optimization Tip:** Speed up audio 1.1x to 1.5x before transcription to save costs (works because humans can understand faster speech).
- **Platform Recommendations:**
  - `fal.ai` and `Together.ai` for multimedia generation (image, video, audio) with better pricing than standard APIs.
- **Local Model Recommendations:**
  - Demonstrated LM Studio for running models locally.
  - Highlighted Gemma 4 (especially 2B parameter model) as impressive.
  - Discussed quantization (`Q4`, `Q6`, `Q8`) to reduce VRAM requirements.
  - Mentioned Qwen 3 embedding models as better alternatives to OpenAI.
- **Specialized Tools Mentioned:**
  - **Microsoft Presidio:** For PII reduction.
  - **DeepSeek OCR:** For handwriting recognition.
  - **OpenAI Cookbook:** Excellent resource for production-ready code examples.

---

### Part 3: Q&A Summary

Based on the Q&A throughout the session, here is a summary of key questions and answers:

#### Summary 1: High-Level Q&A

##### Technical Questions

- **Caching:** OpenAI automatically caches input tokens (10x discount), but Nano models don't support caching. Mini and larger models do. Anthropic charges separately for cache writes and reads.
- **Local Models:** LM Studio recommended for running local models on Mac/Windows. Gemma 4 (even 2B parameter) is highly impressive for basic tasks. For coding, no open source model matches Claude's level yet.
- **Context Windows:** Even with 1 million token context windows, RAG is still necessary because models don't give equal attention to all tokens. Gemini has the best context window performance.
- **Embeddings:** For open source, use Qwen 3 (0.6B or 8B parameter models). For premium, use Gemini Embedding 2.0. Avoid OpenAI's embedding models as they are not the best or cheapest.
- **Transcription:** For Indic languages, use Scribe V2 by ElevenLabs (cheaper and better than Whisper). For English/European languages, Whisper or Foro Mini work well.

##### Cost Optimization

- **Audio Tip:** Speed up audio by 1.25x–1.5x before transcription to save 25–50% on costs.
- **Batch API:** Offers 50% discount if you can wait up to 24 hours.
- **Combination:** Caching and batching can be combined for maximum savings.

##### Platform Recommendations

- **OpenRouter:** For general LLM routing.
- **Together.ai & fal.ai:** For image/video generation.
- **OpenCode:** For coding (works with various models).
- **Microsoft Presidio:** For PII reduction.
- **DeepSeek OCR:** For handwriting recognition.

##### Resources Mentioned

- **OpenAI Cookbook:** Highly recommended, production-ready examples.
- **Gemini Cookbook:** Decent but not as comprehensive.
- **MLX Community on Hugging Face:** For Apple Silicon optimized models.
- **GGUF format:** For Windows/NVIDIA optimized models.

---

#### Summary 2: Detailed Q&A Breakdown

##### Technical Questions

1. **Local LLM Usage:** Multiple participants asked about running models locally. Sidharth demonstrated LM Studio for Mac (16GB M1) and mentioned Ollama as alternatives. He recommended Gemma 4 (even the 2B parameter model) and Qwen 3 for local inference.
2. **Caching:** Questions about how caching works were addressed – it's done by the lab running the model, cached from the first token onwards, and provides 10x discounts typically. OpenAI does it automatically, but Anthropic requires explicit configuration.
3. **Model Recommendations:**
   - **Transcription (especially Indic languages):** Scribe V2 by ElevenLabs.
   - **Embeddings:** Gemini Embedding 2.0 (premium) or Qwen 3 (budget-friendly).
   - **Coding:** No open source model matches Claude/Cursor level; GLM 5.1 is closest but requires 700–800GB VRAM.
   - **OCR / Handwriting:** DeepSeek OCR.
   - **Image Captioning:** InternVL 3.5.
4. **Context Window:** Questions about whether long context windows eliminate the need for RAG – Sidharth clarified that RAG is still necessary because models don't give equal attention to all tokens in long contexts, referencing the "Needle in the Haystack" benchmark.
5. **Quantization:** Questions about model sizes and VRAM requirements – Sidharth explained FP8, FP16, Q4, Q6, Q8 quantization options that reduce memory usage but increase computation time.

##### Platform Questions

1. **OpenAI Cookbook:** Sidharth strongly recommended it as having production-ready code examples, better than Gemini's or Anthropic's cookbooks.
2. **OpenRouter:** Confirmed it supports caching but not batch APIs or inference discounts.
3. **Alternative Platforms:** Recommended `fal.ai` and `Together.ai` for image/media generation with better pricing.

---

### Part 4: Conclusion & Learning Journey Overview

Based on the context, after the Q&A session, Sidharth provided a comprehensive summary covering several extra topics:

1. **Overview of Learning Journey:** He created a visual map connecting all the concepts learned – starting from chat completion API, prompt engineering, system prompts, and structured responses, moving through context windows, embeddings, vector databases, RAG (Retrieval Augmented Generation), memory systems, and finally agents with tool calling.
2. **Model Types:** He explained the different types of models covered – LLMs (Large Language Models), VLMs (Vision Language Models that can process images), SLMs (Small Language Models with around 2 billion parameters or less), embedding models, and multimodal models.
3. **Practical Applications:** He emphasized that with these fundamentals, participants can build various applications like:
   - Customer support chatbots
   - Custom GPTs with RAG
   - Personal assistants (like Jarvis)
   - Tools similar to Cursor or other AI coding assistants
4. **Key Principles:** He simplified the core concepts – prompting gets responses, adding additional data (RAG) customizes responses without fine-tuning, and agents automate the process with actions through tool calling.
5. **Live Demo:** He showed a personal app he built using these techniques – a meeting recording agent that responds to voice commands, demonstrating how the learned concepts can be practically implemented.

*He encouraged participants to build their own projects to truly connect all the learned concepts rather than just replicating existing tools.*
