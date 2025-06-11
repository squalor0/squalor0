## 👋 Hi, I'm [Your Name]

I'm currently a student exploring the intersection of AI, interactivity, and user-centered design — especially in the education space. While I don't have formal industry experience yet, I’ve built and tested real working systems that show what I can do. Below are a few highlights from my codebase that best represent my work.

---

### 💡 Study Helper – A Modular AI Tutoring System

This is a custom-built JavaScript/Node.js-based chat system designed to support multiple learning modes. It connects to a lightweight locally hosted LLM (DeepSeek 1.5B) via a streaming API and handles dynamic prompts and context-aware state logic.

**Key features:**

- Prompt templating by mode (`steps`, `example`, `flashcards`, `quizmaster`)
- Streaming response handling using a custom `JSONStreamService`
- Conversation memory and role tracking
- Custom quiz mode with local validation and gamified response scoring

```js
switch (mode) {
  case "steps":
    formattedPrompt = `Explain in a step by step format: ${prompt}`;
    break;
  case "quizmaster":
    formattedPrompt = `Generate exactly one question, with its answer...`;
```

```js
jsonStreamService.on("data", (data) => {
  const textData = data.toString().trim();
  if (textData === "[DONE]") return;
  const parsedData = JSON.parse(textData);
  if (parsedData.fullContent) {
    const newFull = parsedData.fullContent;
    const diff = newFull.substring(previousFullContent.length);
    previousFullContent = newFull;
    assistantMessage = newFull;
  }
});
```

The `quizmaster` mode runs a structured back-and-forth quiz session:
- Generates strict JSON-only Q&A objects from a given topic
- Validates answers locally using partial string matching
- Tracks who answered correctly first, awarding points

---

### 🧪 Dynamic Form Field Validation Tests

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

### 🚧 Currently Exploring

- WebSocket integration for real-time multiplayer quiz play
- Animated avatar assistant tied to tutoring logic
- Modular LLM prompt chaining based on Universal Design for Learning principles

---

### 📫 Contact Me

I'm open to collaboration, internships, or feedback!  
📧 [your.email@example.com]  
💼 [LinkedIn Profile or CV]  
🌐 [Your Portfolio Website (optional)]

---

Thanks for visiting!
