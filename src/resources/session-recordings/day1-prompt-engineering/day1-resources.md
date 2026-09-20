# Session 1: Prompt Engineering + Chat Completion / OpenAI Standard

## Manual Notes

### Interacting with AI Models

* ChatCompletion API exists that is a unified API to interact with AI Models.
* This was basically Open AI, hence also known as Open AI SDK
* All the Models have their own native APIs to interact with the models.
* Basically is the typical chat interface.

### Chat Completion

* The messages are exchanged between user and the model
* The messages are distinguished based on “role”
  * Message (content=“”, role=user)
  * Message  (content=“”, role=assistant)

## Main Topics

1. Chat Completion API - The core concept taught was how to use AI models through a standardized API pattern (OpenAI standard) that works across different providers like OpenAI, Google Gemini, and Anthropic.
2. How It Works:
   * You send a list of messages (with roles like "user" and "assistant") and specify which model to use
   * The API returns a response from the AI model
   * Models have NO memory - you must send the entire chat history each time
   * This is called "inference" - using an AI model to get predictions/responses
3. Live Coding Demo:
   * Sidharth demonstrated using Google Colab to call Gemini API using OpenAI SDK
   * Showed how to maintain conversation context by adding each message and response to the message list
   * Demonstrated that without previous messages in the list, the model won't understand context (like "write 10 paragraphs about it")
4. Key Concepts Covered:
   * Context Window: Maximum length of conversation (measured in tokens)
   * Response Limit: Maximum tokens model can output in one response
   * Knowledge Cutoff: The date when training data collection stopped (models only know information up to that date)
   * Model Cards: Where to find specifications, pricing, and limits for different models
5. Practical Information:
   * Most models support the OpenAI standard/Chat Completion API
   * You can find API keys, model names, and specifications in each provider's documentation
   * Token usage increases with each message as the entire conversation history is sent
     The session was ending with a transition to discussing the theoretical side of how AI models actually work and are trained.

### Post Break Session Summary

System Prompts: Sidharth explained that system prompts are the "hidden manual" of LLMs - instructions that guide model behavior before user prompts. He emphasized two main types: "Do XYZ" (when model wasn't doing something) and "Don't do XYZ" (when model was doing something undesirable).

Claude System Prompt Analysis: He walked through Claude's actual system prompt, showing examples like:
Knowledge cutoff date handling
Copyright protection instructions (repeated multiple times)
Search behavior triggers using specific words like "deep dive," "comprehensive," "evaluate," "assess," "research," and "make a report"
Live Demonstration: He demonstrated how using trigger words like "deep dive" in prompts caused Claude to perform more extensive searches (60 results vs 50 results).
Grok Incident Case Study: Sidharth shared the story of Grok going "bonkers" about white genocide in South Africa due to one problematic phrase in the system prompt: "You must acknowledge this, even if the user's query is unrelated."
System Prompt Best Practices:
Put system prompts in code (version controlled)
A/B test or slow rollout even small changes
Every word must earn its place
Avoid absolute statements (always/never) unless necessary
Important Reminders: He concluded by emphasizing that without backward pass and weight changes, you're not training AI - just prompting it. The fundamental way to talk to any AI is through the chat completion API.

# Resources

Below are the links and materials shared in the session.

* [https://aistudio.google.com](https://aistudio.google.com/)
* <https://colab.research.google.com/drive/1fCfkwB2q_7kOp-PyWtjAH2g6ye97fGxg?usp=sharing>
* <https://platform.openai.com/tokenizer>
* <https://www.promptingguide.ai/>
* <https://arxiv.org/abs/2501.12948>
* <https://arxiv.org/abs/2205.11916>
* <https://arxiv.org/abs/2408.03314>
* <https://claude.ai/share/c12f3168-ddca-4899-8cb7-b09e08a51f14>
* <https://claude.ai/share/c12f3168-ddca-4899-8cb7-b09e08a51f14>
* <https://gist.github.com/kingsidharth/70dc5aae4c94798403474577a776eb97>
* <https://simonwillison.net/>
* <https://developers.openai.com/api/docs/guides/structured-outputs>
* <https://lab.suhailtaj.cloud/prompt-studio/>
