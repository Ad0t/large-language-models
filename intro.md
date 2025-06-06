# Large Language Models
LLMs generate text and are typically used in chat interfaces.
## How it works?
An LLM:
- Takes some input text,
- Analyzes it
- Predicts the next word that makes sense.
LLMs don't just predict a single word
- Have a vocabulary of words, characters, etc.
- Assign a probability to each based on the input context
Example Probabilities:
```
"blue." → 60%
"clear." → 45%
"cloudy." → 41%
"apple." → 0.002%
"banana." → 0.001%
```