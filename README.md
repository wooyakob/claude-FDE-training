# Claude Forward Deployed Engineer Learning Path
Jake Wood's notes.

## AI Capabilities & Limitations

### AI Fluency 4D framework. 
Human competencies on one side and machine properties on the other.

To collaborate well with AI, Human's must master:

1. Delegation
2. Description
3. Discernment
4. Diligence

When you understand why an AI system behaves the way it does, you can delegate to it more wisely, describe your intent more clearly, discern the quality of what comes back, and exercise the right level of diligence before you use it.

Generative AI has four core properties:

1. Next Token Prediction
2. Knowledge
3. Working Memory
4. Steerability

Most of the AI running in the world today is not generative: 
- Recommendation engine that picks your next video.
- Spam filter in your inbox.
- Fraud model that flags a suspicios charge.
- System that routes your customer service call.

Generative AI produces new content, versus categorizing existing.

The properties in this framework apply specifically to transformer based text models.

#### Next Token Prediction
##### Where do AI answers come from?
Simple operation at remarkable scale.
Given everything written so far, predict what comes next.
One fragment at a time, sampling each new word from a probability distribution shaped by everything it learned in training.

It is closer to a vastly sophisticated autocomplete than a search engine. The system isn't looking up an answer, it's writing one word by word. 

When your task resembles patterns the model has seen many times (summarize this, explain this concept), you land in the capability zone.

#### Knowledge
##### What does AI actually know?
AI models learn from enormous quantities of text, mostly from the internet, books, other written sources. Through billions of rounds of what comes next, models develop internal representations of language, concepts, relationships and facts. 

Models don't browse the web in real time unless given tools to explicitly do so. It does not have experiences. Its knowledge is fixed at the end of training. 

A model's knowledge is uneven in a specific predictable way.

Topics that appeared frequently, recently and consistently in the training data sit in the capability zone: mainstream science, popular programming languages, widely discussed history.

How well represented was this in what it read.

#### Working Memory
##### What is the AI paying attention to right now?
When interacting with AI, everything relevant to the conversation sits inside a fixed size context window.

Instructions, uploaded docs, system's prior responses and running dialogue. Models generally attend to what's in that window and nothing outside of it.

Window has a fixed limit. Window empties between sessions.
The model does not remember yesterday's conversation without tools or features to do so.

As docs get longer, conversations run on, you expect continuity across sessions, you slide towards the context window limit.

#### Steerability
How much am I in control?
Fine tuning teaches the model what being a helpful assistant looks like: treat this as a question, response in this format, break a big task into steps, the result is a system that's remarkably steerable.

Steerability is not the same as understanding. Models follow instructions by continuing a pattern.

There's always a gap between what you intended to diect and what actually landed.

Control is highest when instructions are short, concrete, verifiable: respond as a table, keep it under 100 words. 

Control degrades as the chain of reasoning gets longer, instructions more abstract, task requires precision. 

Models will continue the document in whatever direction seems statistically likely. 

### AI Personality
1. Pretraining
Model exposed to vast quantities of text and trained on a single task. Given everything, predict what comes next. It is a document completer. It has no concept of you, and no concept of helping.

2. Fine Tuning
Turn document completer into an assistant. Use curated examples of good assistant behaviour. Use reward signals to nudge it towards good and safe responses that most people prefer. This training is shaped by human judgments about what a good and safe response looks like.

### Using the framework
The four properties interact constantly. Most real world failures are two of them meeting e.g. a hallucinated citation is Next Token Prediction (generating what looks plausible) meeting Knowledge (a gap the model does not know is there).

The lesson material is durable because these four properties remain stable even as the models improve.

### Mapping
4-6 tasks I've used AI for in the last 2 weeks
1. Claude design for building custom slides based on specific content.
[Yes first try]

2. Writing Swift code for an iOS application that runs Apple Foundation and Text Embedding models to answer questions using Retrieval Augmented Generation.
[No, required reworking indexes and search types]

3. Reviewing comments from engineering and helping me to update configuration recommendations.
[No, changed the meaning and had to revert one suggestion and override]

4. Adapting an Agent using the GO ADK to run local models instead of the Gemini API I'm not paying for.
[Yes, did this first time]

### Claude Analysis
1. Claude Design slides
Wrong facts that look polished. A clean layout makes a made-up stat or a bad claim feel more true. If a customer or exec sees it, you own the error. 

Fix: Check every number, name, and product claim against a source before it leaves your laptop.

2. Swift RAG app
Search “works” but returns bad results. Wrong index type or search mode still returns answers, just poor ones, and you may not notice. The model then writes fluent text on weak context. 

Fix: Keep 10-20 test questions with known answers. Re-run them after each change.

3. Reviewing engineering comments
The AI shifts meaning while it tidies the wording. A “must” becomes a “should,” or a limit gets lost. You ship a config that engineering never agreed to. 

Fix: Ask for a diff-style output (old vs. new) and check each change against the original comment. Never accept a rewrite whole.

4. Go ADK with local models
It runs, but tool calling breaks quietly. Local models are often weaker at structured tool calls and may skip a call, make up arguments, or loop. A demo that worked once can fail in front of a customer. 

Fix: Run the same task 10 times. Log tool calls. Set max iterations and timeouts.

#### Which tasks felt safe to hand to AI, which felt risky? Why?
Code generation felt safer because of the comprehensive documentation and clear context.

Designing slides and reviewing comments felt risky because they do not require a prediction of what's next but a genuine understanding of the content to perform both tasks accurately.

#### What's one AI behaviour you've noticed (good or bad) that you couldn't explain at the time?
It's desire to please the user and general agreeableness can lead to inaccurate outcomes until a stern correction, at which point the model usually acknowledges the mistake, agrees and apologises.

