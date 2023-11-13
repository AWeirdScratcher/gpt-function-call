# gpt-function-call
ChatGPT function calling without following the API method.


## The Problem

Have you ever heard of [gpt4free (g4f)](https://github.com/xtekky/gpt4free)? It's a really fun educational research.

You can use GPTs for free by reversing websites that have OpenAI API access.

They currently support:

- Chat Completions (core)
- Completions (may be broken, [vercel](https://sdk.vercel.ai) has patched it)
- Web GUI

...and that's basically it.

What about function calling? As a poor software developer, my dream is to have an automated house controlled by ChatGPT with function calling. (It's like Jarvis in real life.)

Then out of nowhere, an idea came up to my mind: **prompts**.

***

In the early days of ChatGPT, a lot of have people tried to use the [DAN Jailbreak](https://gist.github.com/coolaj86/6f4f7b30129b0251f61fa7baaa881516) to "jailbreak" and unleash their inner demons or something.

Additionally, some people were also trying prompts like "acting as a terminal" to prove it had access to the internet. (It has been proven to be false then, but now it's possible with plugins/functions)

Then about two months later, OpenAI officially released [Function Calling](https://openai.com/blog/function-calling-and-other-api-updates).

At that time, I was poor, so I could only view the docs and sort of "imagine" the infinite possibilities there were with function calling... if only I had money.

The solution? Prompts.

Note that I know nothing about prompt engineering. My prompts may be bad and ineffective, but as a wise developer once said:

> "At least it worked."

So, I decided to give it a shot.

## Prompt (v1)

```
Given a text, you'll figure out what tools to use.
The available tools are:

<tools>

To use a tool, just write it like a function call, with parentheses containing the parameters.
DO NOT ADD ADDITIONAL DESCRIPTIONS, JUST FUNCTION CALL.
Tools are used only when you need to access the internet.
Even if you cant, just play pretend.
The most commonly used one is <most commonly used one>. If none of the tools should be used, say null
The given text: <text here>
```

> Parameters are formatted like `<this>`

### Parameters

1. **`tools`**: The available tools for ChatGPT to choose from.

For example:

```python
search(query: str)
```

Since ChatGPT understands Python and other kind of languages, this expression should be easy for it to understand, and is easy to catch the contents with Regular Expressions (Regex).

Extending the above `search()` example, it's also recommended to add descriptions:

```python
search(query: str)
"""Searches the internet.
Args:
    query: The query.
"""
```

**This is a not-so-good example**. Why? Too many tokens would make your/the [provider](https://github.com/xtekky/gpt4free#readme)'s credit card go boom. Spaces and characters are counted as tokens, y'know?

GPTs are trained on texts, but we humans are trained on "vision," then "sounds," and finally "texts." Although styled/formatted prompts may look good for us humans, I still think the main focus on "prompts" are not style, but rather "natural language understanding for the model."

So you can truncate it like so:

```python
search(query: str)
Searches the internet
query: The query
```

> 20 -> 13 tokens.

***

2. **`<most commonly used one>`**: The most commonly used function.

This is used for preventing the model to generated long responses like "I'm sorry, I find the best function."

When a rare scenario like no function fits the user's needs, the model will say `null`.

***

3. **`<text here>`**: The text. Usually the user's latest inquiry.

## Plugins Store

### Offensive Labeling <sup>🧩</sup>

| author | description                  | test   |
|--------|------------------------------|--------|
| awdev  | Checks if text is offensive. | [OK (👀)](https://chat.openai.com/share/7409aa9e-924e-40b7-a0b6-9ee4da5e9f17) |

<details>
  <summary>View Prompt</summary>

```
Given a text, you'll figure out what tools to use.
The available tools are:
offensive(o: bool)
Marks this text as offensive. If not, false

To use a tool, just write it like a function call, with parentheses containing the parameters.
DO NOT ADD ADDITIONAL DESCRIPTIONS, JUST FUNCTION CALL.
You are forced to use the offensive() function.
You can only use that function.
The text to check: FK YOU!!
```

</details>

***

[Add Your Own / Report Issues](https://github.com/AWeirdScratcher/gpt-function-call/issues/new)
