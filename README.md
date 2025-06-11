## Hi, I'm Blake Murphy

I'm currently a student exploring the intersection of AI, interactivity, and user-centered design — especially in the education space. While I don't have formal industry experience yet, I’ve built and tested real working systems that show what I can do. Below are a few highlights from my codebase that best represent my work.

---

### Study Helper – A Modular AI Tutoring System

This is a custom-built JavaScript/Node.js-based chat system designed to support multiple learning modes. It connects to a lightweight locally hosted LLM (DeepSeek 1.5B) via a streaming API and handles dynamic prompts and context-aware state logic.

**Key features:**

- Prompt templating by mode (`steps`, `example`, `flashcards`, `quizmaster`)
- Streaming response handling using a custom `JSONStreamService`
- Conversational memory
  
- Custom quiz mode with local validation

### `ChatService.js` — Streaming Chat with Mode-Based Prompting

This class powers the core logic for the AI helper in my Study Helper app. It manages AI interaction state, supports multiple learning modes (e.g. steps, flashcards), and handles live streaming of LLM responses.

```js
import axios from "axios";
import JSONStreamService from "./JSONStreamService.js";

export default class ChatService {
  constructor() {
    this.conversationHistory = [];
  }

  clearHistory() {
    this.conversationHistory = [];
  }
```

---

### How It Works

#### Maintains Conversation State
It stores the conversation in `this.conversationHistory` — a persistent array of `{ role, content }` messages that allows for multi-turn conversations with context.

#### Formats the Prompt Based on Learning Mode
The `getChatResponse(prompt, mode)` method adapts the prompt depending on the selected learning goal.

```js
switch (mode) {
  case "steps":
    formattedPrompt = `Explain in a step by step format: ${prompt}`;
    break;
  case "example":
    formattedPrompt = `Explain with a real world example: ${prompt}`;
    break;
  case "flashcards":
    formattedPrompt = `Generate 10 questions and answers on this subject: ${prompt}`;
    break;
  default:
    formattedPrompt = prompt;
}
```

#### Sends the Full Chat History to the LLM
It posts the entire `conversationHistory` to the LLM endpoint (running locally) so the model retains context over multiple messages.

```js
const response = await axios.post("http://bappity.net:11434/v1/chat/completions", {
  model: "deepseek-r1:1.5b",
  messages: this.conversationHistory,
  stream: true,
}, {
  headers: { "Content-Type": "application/json" },
  responseType: "stream"
});
```

#### Streams AI Responses with `JSONStreamService`
Instead of waiting for the full output, it processes the response incrementally using a custom JSON stream parser:

```js
let assistantMessage = "";
jsonStreamService.on("data", (data) => {
  const parsedData = JSON.parse(data.toString().trim());
  if (parsedData.fullContent) {
    assistantMessage = parsedData.fullContent;
  }
});
```

#### Cleans and Stores the Final Answer
Once the response stream ends, the class cleans out internal `<think>` tags and appends the final assistant message to the conversation:

```js
jsonStreamService.on("end", () => {
  assistantMessage = assistantMessage.replace(/<think>[\s\S]*?<\/think>/g, "").trim();
  this.conversationHistory.push({ role: "assistant", content: assistantMessage });
});
```

---

### This class demonstrates:

- Async JavaScript with `axios` and stream handling
- Dynamic prompt construction
- Real-time LLM response parsing via streams
- Clean conversational state management

It’s a foundation for building adaptive learning tools and responsive chat systems with local or remote LLMs.

```js
return jsonStreamService;
```

---



The `quizmaster` mode runs a structured back-and-forth quiz session:
- Generates strict JSON-only Q&A objects from a given topic
- Validates answers locally using partial string matching
- Tracks who answered correctly first, awarding points

---

### Dynamic Form Field Validation Tests

I also built a test framework for form validation logic to ensure high-quality data collection. These tests cover input edge cases like date of birth accuracy, mismatched country codes, and phone number formatting.

**Highlights:**

- Field-by-field validation with contextual presetting
- Tests for invalid characters, structure, and logic errors
- Edge case handling: leap years, name formatting, optional fields

```js
{ input: "John!", expectedValid: false, description: "Contains a special character" }

{ input: { dobDay: "29", dobMonth: "2", dobYear: "2021", age: "3" }, expectedValid: false, description: "Invalid leap year date" }

{ input: { country: "Germany", countryCode: "+44" }, expectedValid: false, description: "Mismatched country and code" }
```

All tests log results clearly to the console using simple `PASS/FAIL` markers, making it easy to detect broken validation logic during development.

---

### Currently Exploring

- WebSocket integration for real-time multiplayer quiz play
- Animated avatar assistant tied to tutoring logic
- Modular LLM prompt chaining based on Universal Design for Learning principles
- https://allendowney.github.io/ThinkComplexity/

---

### Contact Me

I'm open to collaboration, internships, or feedback!  
bmurp001@goldsmiths.ac.uk  

---

Thanks for visiting!
