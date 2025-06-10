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
- `"The cat is sleeping"`,
- `"The", " cat", " is", " sleeping"`,
- `"T", "h", "e", " ", "c", "a", "t", " ", "i", "s", " ", "s", "l", "e", "e", "p", "i", "n", "g"`
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
## Subword tokens
Instead of: [run, running, play, playing]
it's tokenized as: [run, play, stay, ing]
- Can be used to construct words like [running, playing, staying] - AI will learning that adding "ing" to a base verb will convert it to present continuous
- Requires less computation then processing each letter separately
- Vocabulary size reduced from 10s of millions to just 100s of thousands
- Better at understanding texts with spelling mistakes
## Vector Embedding
Each token in the vocab will have a corresponding vector embedding (array of numbers).
``` assuming
"car" = [0.84, 0.01, 0.31, 0.03]
"grass" = [0.01, 0.97, 0.89, 0.82]
```
In reality, these vectors are thousands of numbers long, and each number captures some
characteristic of this token, e.g.: quickness, aliveness, greenness, fluffiness, playfulness,
vehicleness, parallel universeness, regular excerciseness, and who knows what else [we
don't] - each feature will be a measure of some characteristic, and it will contribute to the
meaning.
AI learns by itself what each feature should be about, and the numerical value of it for each
token, and we don't know what exactly those numbers represent.
---
Let's take these 2 tokens as an example.
```
"dog" = [0.42, 0.97, 0.95]
"cat" = [0.73, 0.04, 0.01]
```
### Feature (number) 1: Fluffiness Amount
`How soft, fuzzy, and likely to leave hair on your favorite hoodie?`
- Dog → 0.42
Dogs come in all fluff levels, from bald weirdos to walking cotton balls.
- Cat → 0.73
Living fur clouds. Built for maximum softness.
### Feature 2: Willingness to Save Your Life
`If you're in a lake and can't swim… will it save you?`
- Dog → 0.97
Leaps in with heroic urgency. Risks life. Brings floaty.
- Cat → 0.04
The one who pushed you into the water.
### Feature 3: Gratefulness
`How much it appreciates everything you do for it?`
- Dog → 0.95
Thinks you hung the moon. Worships your every move. Applauds your shoe-tying
skills.
- Cat → 0.01
You are but a mere buttler, void of any purpose beyond servitude.
In reality, features at same positions across tokens don't necessarily encode same
characteristic, also different tokens can have unique characteristics, or multiple features
could work together to encode characteristics.
### Summary
These vector embeddings aren't manually created by human scientists — the AI learns
them by reading the entire internet (trillions of words), trying to predict the next token as
it's reading it, and adjusting vector embeddings for each token and other numbers
(parameters, that we will talk about later) so its predicted token matches the actual token in
the training data.
As it fails to predict the next token during the training, it updates vector embeddings and
other parameters, causing the correct token from the training data to be more likely next
time, and incorrect tokens less likely.
## How LLMs use vector embeddings
`"The car is fast, and the car is red."`
- "The" → Vector: [0.12, 0.34, 0.56, 0.78]
- " car" → Vector: [0.84, 0.01, 0.31, 0.03]
- " is" → Vector: [0.23, 0.45, 0.67, 0.89]
- " fast" → Vector: [0.91, 0.12, 0.34, 0.56]
- " and" → Vector: [0.78, 0.90, 0.12, 0.34]
- " red." → Vector: [0.56, 0.78, 0.90, 0.12]
- "," (comma) → Vector: [0.55, 0.66, 0.77, 0.88]
Usually tokens have space as the first character (" car") - it's more efficient then encoding
space separately.
---
LLMs replace each token with the vector embedding.
```
"The car is fast, and the car is red."
```
```[0.12, 0.34, 0.56, 0.78] [0.84, 0.01, 0.31, 0.03] [0.23, 0.45, 0.67, 0.89] [0.91, 0.12, 0.34,
0.56] [0.78, 0.90, 0.12, 0.34] [0.84, 0.01, 0.31, 0.03] [0.23, 0.45, 0.67, 0.89] [0.56, 0.78,
0.90, 0.12] [0.55, 0.66, 0.77, 0.88] [0.78, 0.90, 0.12, 0.34] [0.56, 0.78, 0.90, 0.12]
```
-
---
But we don't want to tie tokens to vectors, because we want to use same token vocabulary
for different versions of the model or models, which will have different vector embeddings
for the same token.  

So we will also have an array of vector embeddings (array of arrays, matrix), where each
vector embedding is placed at the same position corresponding to the index of the token.
```
[
[0.12, 0.34, 0.56, 0.78]
[0.84, 0.01, 0.31, 0.03]
[0.23, 0.45, 0.67, 0.89]
[0.91, 0.12, 0.34, 0.56]
[0.78, 0.90, 0.12, 0.34]
[0.56, 0.78, 0.90, 0.12]
[0.55, 0.66, 0.77, 0.88]
]
```
We will place a vector embedding corresponding to the token with index 0 at position 0.  
Later we will pluck out embedding corresponding to the token's index from the embedding
matrix.
## Mapping to Indices
- "The" : 0
- "car" : 1
- "is" : 2
- "fast,
- " : 3
- "and" : 4
- "red." : 5
`"The car is fast, and the car is red."`
`0 1 2 3 4 0 1 2 5`
---
For each index (0 1 2 3 4 0 1 2 5) we will pluck out corresponding vector embedding (they
are arranged to correspond to tokens with the same index.)
[0.12, 0.34, 0.56, 0.78] [0.84, 0.01, 0.31, 0.03] [0.23, 0.45, 0.67, 0.89] [0.91, 0.12, 0.34,
0.56] [0.78, 0.90, 0.12, 0.34] [0.84, 0.01, 0.31, 0.03] [0.23, 0.45, 0.67, 0.89] [0.56, 0.78,
0.90, 0.12] [0.55, 0.66, 0.77, 0.88] [0.78, 0.90, 0.12, 0.34] [0.56, 0.78, 0.90, 0.12]   
This allows us to easily swap vector embeddings from different models / versions that use
the same tokenizer.