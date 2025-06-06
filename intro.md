# Large Language Models
LLMs *generate text* and are typically used in chat interfaces.

---
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
---
# Methods For Word Selection
1. Greedy Sampling:
    - Always pick the most probable word.
    - Example: `"blue"` (60%).
2. Random Sampling:
    - Randomly choose based on the probability distribution.
    - Could choose `"cloudy"` even if it's not the most probable.
3. Top-k Sampling:
    - Choose randomly from the top k most probable words.
    - Example: Top 3 = `"blue", "clear", "cloudy"`
4. Temperature Sampling:
    - Adjust the "sharpness" of the distribution.
        - Higher Temperature = More Randomness.
        - Lower Temperature = More Deterministic (closes to greedy)
    - Higher temperature gives rare words like "banana" more chance.

---
# Tokens
Anything can be added as a token that AI can generate (predict based on preceding text):
```
"car" (words token)
"b" (a letter token)
"123" (a number token)
"🌲" (an emoji token)
" " (a Chinese character token)
"The sun rises in the east." (a sentence token)
" " (a Chinese sentence token)
"a1 b2 c3!@#" (an arbitrary token)
```

## When a token is:
### A word: Car
The model learns:
- It's a vehicle
- It has wheels
- People drive it
- It's used for transportation
- (can start associating brands)
- ....
### A letter: b
The model learns:
- Second letter in the English Alphabet
- It can start words like "book" or "bird"
- Where and How it's used to construct words
- ....
### A number: 123
The model learns:
- It represents a specific quantity
- It follows mathematical rules
- It appears in contexts like counts, measurements, or dates
- ....
### An emoji: 🌲
The model learns:
- It's an emoji(and what is an emoji)
- It represents a tree or forest
- It's also used to symbolize nature or the outdoors
- ....
### Different language character
The model learns:
- It's meaning
- It can be part of compound words or not
- Contexts related to it
- It's specific stroke order and writing pattern
### A sentence: "The sun rises in the east" or any other language
The model learns:
- It's meaning (is a natural phenomenon that happens daily)
- It has a subject(sun), verb(rises), and prepositional phrase (in the east)
- It's a statement of fact that's universally understood
### An arbitrary: `"a1b2$3]'.fDc3!@#"`
The model learns:
- It contains a mix of letters, numbers, characters from different languages, and symbols
- It doesn't match common language patterns
- It might be a code, password, or random string
- It lacks semantic meaning
```
Model learns the semantics of the token. (How it understands? Debatable)
```

---
## Tokenization
The question is, which way of tokenizing is best:
`"The cat is sleeping"`,
`"The", " cat", " is", " sleeping"`,
`"T", "h", "e", " ", "c", "a", "t", " ", "i", "s", " ", "s", "l", "e", "e", "p", "i", "n", "g"`
Each token requires same amount of compute to process.
## Computational Cost
"The quick brown fox jumps over the lazy dog"
- Letter tokenizer: 44 tokens = 44x compute (a lot more as a letter can mean anything)
- Word tokenizer: 9 tokens = 9x compute
- Sentence tokenizer: 1 token = 1x compute

But there are some issues with word and sentence tokens as well
- Massive vocabulary: Not only every word, but every version of every word: ["run", "runs", "ran","running"] - this requires high compute to calculate probability distribution.
- If you forget to add any word, e.g., "running", to the vocabulary, the AI will not learn what it means.
- Can't handle misspellings: If text contains incorrect spellings like "runned" or "avocdo", humans can infer meaning, but AI would see it as OOV (out of vocabulary) token, replace it with a special token that indicates that this is completely unkonwn, eg. `<UNK>(unknown token)`.
- Most words are rare and will not appear enough times in the training data for model to learn their meaning well.
- Can not construct new words like letters can.
`For sentence level tokens these issues are even more exaggerated.`