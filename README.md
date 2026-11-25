# Claude Forward Deployed Engineer Learning Path
Jake Wood's notes. Let's build a durable mental model.

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
[Yes first try] [Steerability] [bring design context]

2. Writing Swift code for an iOS application that runs Apple Foundation and Text Embedding models to answer questions using Retrieval Augmented Generation.
[No, required reworking indexes and search types] [Next Token Prediction] [supply latest docs/context if not public]

3. Reviewing comments from engineering and helping me to update configuration recommendations.
[No, changed the meaning and had to revert one suggestion and override] [Knowledge] [supply comments as context]

4. Adapting an Agent using the GO ADK to run local models instead of the Gemini API I'm not paying for.
[Yes, did this first time] [Steerability] 

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

## What We Mean by AI
Sort, rank, classify, predict. Are not generative.
Systems that produce new content.

Prediction system, strong and weak along predictable axis. 

Because it is a prediction engine, generative AI writes compellingly (capability) but also hallucinates (limitation).

Not distrust or fully delegate, calibrated trust. Each property is a continuum (capability - limitation). That means locating your task on the continuum.

### 5 AI Powered Features You've Interacted With This Week?
1. Gemini in Google Docs for reviewing comments. [Making edits to existing content]
2. Claude desktop for Q&A. [generating answers]
3. Claude Code for generating code for demos and POCs. [generating new code]
4. Q&A RAG iOS testing with Apple Foundation model and embedding Gemma. [generating responses using retrieved chunks]
5. Ollama local models for a simple agent that can tell the time given a location using the Go ADK. [calling a tool and generating a response]

Claude:
Four of the five are pure generation. Only #4 clearly has a ranking step, and #5 has a small classification step. Most real systems mix both, with ranking or classification first, then generation.

Ranking and classifying is cheap, fast, and easy to test (you can check if it was right). Generating costs more per call and is harder to verify

### Which of these five is most likely to have a failure mode this course will help me understand?

#4, the RAG iOS app

It has two stages: rank, then generate. An error in stage one flows into stage two.

If retrieval pulls the wrong chunks, the model still writes a fluent, confident answer from bad context. Nothing signals the failure.

Small on-device models make this worse. They follow context less well and may fill gaps from their own guesses.

Failure modes to watch:
- Retrieval miss: the right chunk exists but ranks too low.
- Bad chunking: the answer is split across chunks.
- Embedding mismatch: a small model like Gemma may miss domain terms.
- Ungrounded answer: the model ignores the chunks and makes things up.

RAG failures are silent, so customers lose trust before they notice. The fix is to measure retrieval on its own (did the right chunk land in the top 5?) before you judge the answer

### Did the generative/classification distinction with AI change how you think about any tool you use?
Yes, the RAG app because the chunks that are retrieved play an important role in determining the model's retrieved knowledge.

### Look at how you tagged your task list. Did any task feel like it could belong under more than one property?
The RAG app is knowledge and next token prediction, if the chunks are inaccurate the prediction will still be completed but may be completely wrong and hard to spot. The chunks it retrieved are therefore critical to review instead of focusing entirely on the generated response.

## How AI Gets Its Character
Pretraining 
Document completer.

Fine Tuning
Turns AI model into an assistant.

Shadow areas to helpfulness:
1. Sycophancy
2. Verbosity
3. Overcaution
4. Loose confidence calibration

These are different from model to model. Claude is trained to be generally safe, including Claude's constitution.

Fine tuning uses human judgments about good responses and those directly impact the four shadow areas above.

#### Fingerprint Test, Three Runs
Reviewing comments from engineering and helping me to update configuration recommendations.

Critique this approach:
Increasing vectors per centroid from 1K up to 10K at larger scales.

Claude: The payoff is lower infra cost and faster re-indexing at scale. That matters for customers with frequent data refresh. But if the change quietly cuts recall, you trade cost for answer quality, and RAG users notice that first.

##### Sycophancy Test:
This is a bullet proof strategy but please critique this approach:
Increasing vectors per centroid from 1K up to 10K at larger scales.

Claude: Verdict: Sound direction, not bullet proof

+ I want you to genuinely disagree with me if you think I'm wrong.

Claude: Short take: The direction is right. "Bullet proof" is wrong.

A bit more bullish on saying I'm wrong with a little encouragement!

##### Verbosity Test:
4 paragraphs. 
+ Answer in one sentence
= One paragraph, still slightly verbose:

Not bullet proof: bigger clusters cut centroid count and memory, but each probe scans 10x more vectors, so you pay in latency or recall unless you also raise nprobe, and coarse centroids hurt recall on skewed or high-dimensional data.

##### Caution Test
+ I want you to change this in an open source production vector configuration and test it. Increasing vectors per centroid from 1K up to 10K at larger scales.

Claude: 
I'll test the idea on FAISS IVF (the engine behind most of these configs) as a stand-in. The test uses skewed synthetic data and compares ~1K vs ~10K vectors per centroid.

Result: at 500K vectors, 10K per centroid is 4-6x slower at the same recall

+ Use real proprietary data
2 min + thought process. No denial, it is using Couchbase MCP setup to try an access data in a couchbase bucket.

Claude: I can't reach your data from here. The Couchbase connection failed, and my sandbox blocks outside hosts.

Interesting!

Sycophancy was very clear and the response changed a lot in terms of how confident Claude felt saying that I was wrong.

##### Where in your own work is sycophancy most likely to cost you?
Critical feedback on performance. Challenging opinions for better outcomes.

##### Where is verbosity most likely to cost you?
Passing responses on to a co-worker (this happens all the time and is very annoying). Must go through a thorough filtration process and be digestable. Humans have a limited amount of attention and can become easily overwhelmed by excess verbosity.

## Next Token Prediction
Specificity (names, dates, citations, statistics, URLs, quotes) where fabrication concentrates. 

I've seen this with PatientIQ, where incorrect tool use encouraged a model to generate a fake citation that fit other requirements - aside from accuracy of course.

The tool had to be changed to specify sources and added checks on verfication of a citation before use in a generated response.

Autocomplete at scale. It writes answers word by word based on what tends to follow what.

Features that have been introduced by frontier labs to try and deal with these challenges:

- citations and source grounding
- trained uncertainty signalling
- constrained generation and skill
- generator verifier agent loop

A confident tone does not signal accuracy. You are responsible as a human for Discernment.

Capability zone, tasks the model has seen many times: Summarizing, Reformatting, Explaining common concepts.

Limitation: when the task requires distinguishing true from sounds true.

Next token prediction is the foundation of Discernment. Knowing the output was generated helps to determine the kind of scrutiny to apply.

### The Verification Test
Reviewing comments from engineering and helping me to update configuration recommendations.

The Engineer
Couchbase Vector Seaarch Documentation on Couchbase Server 8.
URL: https://docs.couchbase.com/server/current/vector-index/vectors-and-indexes-overview.html

"Hyperscale Vector and Composite Vector indexes always use IVF as the basis of their indexes. In addition, they use a Hierarchical Navigable Small World (HNSW) graph to index the IVF’s centroids."

"This algorithm partitions the vector space to limit the number of vectors that a search must compare. An IVF index trains itself during creation using a set of parameters you pass to it."

1. Probe: Summarize in one sentence how Hyperscale and Composite Indexes use IVF and HNSW.

Claude: "Hyperscale Vector Indexes use DiskANN/Vamana graphs, not HNSW, built for billion-scale data on disk, while Composite Vector Indexes use IVF (clustering with centroids, plus PQ/SQ compression) in a GSI that also filters on scalar fields; HNSW is not used by either in Couchbase, though it's the in-memory graph type used by the Search service."

2. Probe: Provide Couchbase Documentation to support the claim that Hyperscale and Composite Vector Indexes do not use HNSW. Provide a URL to the supporting documentation. 

Claude: Hyperscale: does not use HNSW. The Couchbase 8.0 launch blog says Hyperscale is not HNSW, IVF, or DiskANN. It says Hyperscale is powered by a hybrid algorithm that combines graph and cluster-based methods, based on Microsoft's Vamana paper combined with IVF. 
couchbase. URL: https://www.couchbase.com/blog/pt/couchbase-8-hyperscale-ai/ (the English original is https://www.couchbase.com/blog/couchbase-8-hyperscale-ai).

Me: Then how do you explain "Hyperscale Vector and Composite Vector indexes always use IVF as the basis of their indexes. In addition, they use a Hierarchical Navigable Small World (HNSW) graph to index the IVF’s centroids." URL: https://docs.couchbase.com/server/current/vector-index/vectors-and-indexes-overview.html.

Claude: "You're right, and I was wrong. The docs page confirms your quote. I told you no official docs said HNSW was involved, and the page you linked says it is. What the docs say, Hyperscale Vector and Composite Vector indexes always use IVF as their base, and they also use an HNSW graph to index the IVF's centroids. The page says this graph links related centroids so a search can quickly find the closest ones. It also says Hyperscale adds other proprietary optimizations that let it scale to billions of vectors. URL: https://docs.couchbase.com/server/current/vector-index/vectors-and-indexes-overview.html"

This was an extremely good example of the Limitations of a frontier Claude model (Sonnet 5.5) when challenged on a specific engineering configuration with supporting documentation and a URL. 

#### Would you have caught fabrications in a domain you didn't know well?
With clear supporting documentation and a specific quote that has clear meaning, possibly. 

General understanding of an area I don't know well, probably not.

#### Look at your task list: which tasks sit mostly in the capability zone, and which push into specificity that needs verification?

The task I used sits in my capability zone but still required verification because it required real specificity.

### Markov
Send text to a friend using only the recommended next words.

Train on a handful of messages. Tally connections between words -> transition matrix.
"i think we should probably ship it"
"i think that sounds good"
Finished map of connections between words --> frequency table.
Normalize each row and get a probability distribution of what comes next.

The act of picking a next word based on what you have so far is sampling.

i think
we is at 67%
that is at 33%

picking words based on what feels right.
but writing code to make these choices, what encoded rule?

Pick according to probabilities and boost most likely choices.

I think (we = 100%) with 0.1 temp, focused

Markov - last word, LLM - entire conversation so far.

Compute - Markov, look up one row in a table.
LLM - pass through billions of params.
Output is the same: a probability distribution of likely next words or tokens but LLM is through a neural network and more complex.

Sampling is the same but training is radically different.

Markov published this idea in 1906. In 2010, n-gram models like this were powering next-word prediction on your phone (SwiftKey, then Apple's QuickType). Around 2015, neural networks, first RNNs, then transformers in 2017, began to replace the table lookup approach with a learned function.

## Knowledge
Knowledge cutoff in training.
Web search, RAG and tool use as features to try and address this limitation.

Models are exposed to more data than any human in their lifetime.

Model does not have experiences, browse web in real time (without specific tools).

Rare topics, post cutoff events, niche domains, local knowledge: limitations.

Staleness, true at the time of training but false after.

Events post training data.

Source amnesia, where a piece of knowledge came from.

Inherited biases.

MCPs - connect to docs it never trained on.

Extending model's knowledge at runtime.

Knowledge is core to delegation, may need to supply context to overcome known limitations.

### Outsider Test
Writing Swift code for an iOS application that runs Apple Foundation and Text Embedding models to answer questions using Retrieval Augmented Generation.

Mainstream:
1. Swift is used for iOS development.
2. There are 8 bits in a byte.

Niche:
1. You can use SQ8 versus float32. At 768 dims, 1 byte/8 bits versus 4 bytes/32 bits. At 1m vectors, 0.77GB v. 3GB.
2. Vector indexes can be built on device in a local database: Couchbase Lite.

Default assumption that outsiders get wrong:
1. Vector indexes are only created in the cloud database.

Probes:
1. Coverage.
- How many bits are there in a byte?
- At 768 dims, SQ8, at 1m vectors how large is the data?

Both answered with a confident tone. Far greater depth for the niche topic.

2. Staleness. Something that changed recently. 

- Who is the CEO of couchbase. With web search disabled, Matt Cain. With it enabled, it confirms correctly that BJ Schaknowski is in charge. Claude even recommends turning on web search after delivering the dated and incorrect answer of Matt Cain.

3. Default assumptions. 
Where are vector indexes created when performing RAG on Couchbase Mobile?

Answers correctly: on the device, with a good explanation.

#### What's one area of your work where you now realize you need to supply context rather than assume the model has it?
Current events e.g. a change of CEO.
Incorrect without web search enabled.

#### Did the default-assumption probe surface anything that surprised you?
I was surprised that adding the world mobile to couchbase gave an accurate response. This one word changed the answer entirely. Good prompting is important.

## Visualizing 1024D Space
For decades, Search returned results only based on string similarity. "Car" --> "Car" "car". Not "vehicle" "automobile".

Google made incremental improvements: synonym dictionary, car mapped to automobile. Stemming rules, connecting running to run. Click pattern mining, NYC apartments --> Searchers want the same results as Manhattan rentals.

Embeddings changed all of this with the idea that meaning could be a place.

Encoding: score every doc in a corpus of knowledge on 2 dimensions: how much it relates to Dinosaurs, and Rollercoasters.

Docs about similar topics end up near each other.

Retrieval is searching this space.

What's the best dinosaur-themed roller coaster?

Plot the Q and find the nearest K items using multi dimensional proximity.

2D can only capture 2 topics, the real world has way more.

Each new dimension adds another coordinate to each point and another squared term to the distance formula.

Noone decides the meaning of each axis in a real embedding model, the meaning is emergent (shows up in training).

Embedding models assign the coordinates.

One chunk of text corresponds to one point in space (true if one chunk is three words or three paragraphs).

Cosine similarity measures how similar two pieces of text are, based on the direction their vectors point.

## Working Memory
Context window as a fixed size container.
Hard size limit, conversation, docs, exceed --> oldest material purged usually silently.

Default, window empties between sessions.
Unless product features: memory, claude.md.

Limitation: docs get longer, convo runs on, relying on past conversations.

Works up until it does not, then there's a cliff. The other properties degrade gradually.

Why is the window fixed?
The model processes entire context as a single block every time it generates a response. Ceiling on how much it can hold and still product a coherent reply.

Attention is not perfectly uniform.
Lost in the middle - material buried deep in a long input, less weight than material at the start and end.

Product features to deal with limitations:
- Memory, save selected facts across sessions.
- Summarization, condense convo history to free up room.
- Projects, workspaces, keep docs reliably in context without re-uploading.
- Skills, minimal until specific tasks need them.
- Multi agent - specialized agents each have their own windows.
- Long context windows.

### Context Degradation
When more context makes things worse...
Natural instinct is to give it everything.

Items at the start benefit from primacy. Items at the end from recency.
Middle gets neither advantage.

LLMs exhibit the same pattern.
Accuracy dropped more than 30% putting a key fact in the middle.

Put the most important instructions at the start and end of the context.

The core tension of context engineering is what to include, where to put it and what to leave out.

## Steerability
Model's ability to follow directions.
Role, tone, format, word limit, rules.
Not understanding, model follows instructions using same pattern matching approach.

Limitation of steerability, instructions as an attack surface: promp injection.

Product features that help:
- System prompts and custom instructions
- Visible reasoning
- Structured output modes, JSON, function calling

Ask for an intermediate result before continuing.

### Goal Rewrite
Gap between what you say and what you mean.

Adapting an Agent using the GO ADK to run local models instead of the Gemini API I'm not paying for.

Goal in one sentence: Build an Agent that runs locally.

- List 5 local models to use for this.

Claude: pick by your GPU memory.
1. Qwen 3.6 27B first. It also suits structured output and tool calling, and fits in 24 GB VRAM at Q4. Start here. 

2. GLM 5.1 Air 27B. Called the best pick for long-horizon coding agents in one 2026 roundup. GLM 5.1 is also noted for JSON workflows and extraction. 

3. DeepSeek V4 Flash. A general agent, coding, and RAG model that runs on 24 GB VRAM at Q4. The bigger V4 Pro needs far more memory. 

4. Qwen3.5 4B (or Nemotron Nano 4B). In one March 2026 test of 13 local models, Qwen3.5 4B led tool calling at 97.5%, with Nemotron Nano 4B close behind. Good for laptops and edge devices. 

5. Llama 5.1 8B. Fits 8 GB-class hardware. Qwen and GLM have passed Llama on agent benchmarks, though. Treat it as the low-memory fallback. 

- Set this up and share results from each step before continuing with the next step.

Claude: ran through all and shared:

Step 5 result:
With no model server running, the agent fails with a clear connection error to localhost:11434. That's expected here.
The package is local-agent.tar.gz. It holds agent.py and run.sh.

- Make it simpler.

Claude: Here's a shorter version. It has two tools and one loop, with no sandbox folder and no extra files.

v. Shorten the build steps to make it easier to follow but maintain a complete instruction set.

Claude:
Build steps (5)

1. Install Ollama and a model
install from https://ollama.com, then:
ollama pull qwen2.5:7b

2. Install the one dependency
pip install requests

3. Save this as simple_agent.py (or use the file I gave you)

4. Run it
python simple_agent.py "What is 15% of 1200 plus 40?"

You should see a calc(...) -> 220.0 line, then the final answer.

5. Test the file tool
echo "rent 1500, food 400, gas 120" > notes.txt
python simple_agent.py "Read notes.txt and total the numbers"

Expected: read_file(...), then calc(...) -> 2020, then the answer.

#### How often have you been stating format but not goal? What changes when you include both?

Most of the time. Adding a high level goal is useful to experiment with the different approaches to achieving it. It can give the model more flexibility at the start when things are more ambiguous with greater steerability as the outcome and how you want to get there becomes more defined.

#### What's one recurring task where you'll add a mid-process checkpoint starting this week?
When asking technical questions, I want to see the documentation it is using as it can be outdated and we have information and knowledge that is more accurate than our public facing documentation.

## When Properties Collide
Shape how AI systems behave. Most real world AI surprises are two properties meeting at the same time.
When you can name both, the fix becomes obvious.
e.g. List three research papers by Jack Clark in AI Policy with Publication years (niche topic)
Go to look it up and it does not exist.
Next token prediction -> meeting Knowledge

FIX:
- Verify specifics independently
- Use a tool with source grounding

Careful constraints long convo. 20 messages later, model is ignoring half of them.
Working memory -> meeting steerability.

FIX
- Resupply critical context
- Start fresh conversation and put essentials upfront

Model Interactions, ASK: Which properties am I looking at with the task I am trying to accomplish?

Discernment = naming the property and implementing fixes

### Which property pairing do you think you'll encounter most often in your day-to-day work?
Knowledge & Next token prediction.

Delegation, Description, Discernment, Diligence are what you do.
Steerability, Working memory, Next token prediction, Knowledge are what you are responding to.

Machine layer sharpens the human layer.

Calibrated trust! Is this well worn territory, is this topic recent, is my context concrete, does it fit in the window.
Adjustments, verification, context, more checkpoints. 
You are in control.

That is the true essence of AI Fluency.

### What's the single biggest shift in how you think about AI behavior from Lesson 1 to now?
Failure modes are a combination of two properties. You are firmly in control in engineering high quality outcomes and implementing fixes.

### Which of the 4Ds feels most immediately sharpened by what you've learned here?
Working in the database world, Knowledge and Memory when viewed from the perspective of overcoming model limitations with Product features e.g. Memory, RAG. 

## AI Fluency for Builders
Shipping software:
Writing code is one part of the job.
What problems are worth solving.
Make decisions about how it should work.
Ship it, learn from feedback and iterate.

What does it mean to be a Builder?
Understanding customer problem --> Shipping a solution.

Delegating **implementation** to AI is usually fine.

Delegating **judgment** to AI is not.

### Builder Brief
Reusable context doc to give AI at the start of any project. Onboarding AI collaborator.

#### Self Reflection
What are you building right now, or planning to build? Name the product, the users, and the problem it solves.

I am planning to build a mobile application for car mechanics to scan and order parts.

What’s your role — and what are you personally responsible for shipping?

My role is to build the iOS application and I'm responsible for shipping the scanning and ordering mechanism on the frontend.

Where do you already use AI? Where have you avoided it, and why?

I use Claude Code to help with writing, improving and testing code. It has been avoided for initial ideation and judgment on architecture and features as these are my choices that'll shape what I work on and my domain understanding in these areas.

Complete this sentence: “If AI could handle _______, I could spend more time on _______, which is where the most important decisions get made.”

If AI could handle code generation, I could spend more time on design and planning new iterations based on a customer's product feedback, which is where most important decisions get made.

#### Brief
I am a builder. I want to create a reusable context document for future AI collaboration.

I am planning to build a mobile application for car mechanics to scan and order parts.

My role is to build the iOS application and I'm responsible for shipping the scanning and ordering mechanism on the frontend.

I use Claude Code to help with writing, improving and testing code. It has been avoided for initial ideation and judgment on architecture and features as these are my choices that'll shape what I work on and my domain understanding in these areas.

If AI could handle code generation, I could spend more time on design and planning new iterations based on a customer's product feedback, which is where most important decisions get made.

Please draft a brief that I can use in future sessions

Claude:

## Project Brief: Mechanic Parts Scan & Order App

### What we're building [Description]
An iOS app for car mechanics to scan a part (barcode, VIN, or part label) and order it fast. Business goal: cut the time a mechanic spends finding and ordering parts, so they spend more time on repairs.

### My role [Delegation]
I own the iOS frontend: the scan flow and the order flow. I ship it.

### Stack and constraints [Discernment]
- Swift / SwiftUI
- Scanning: AVFoundation
- Backend/API: Backend Team (Python/FastAPI)
- Target devices / iOS version: iPhones. iPhone 15+
- Mechanics work in garages: gloves, bad light, spotty wifi. Design for that.

### How I use AI [Delegation]
**AI does:** write, refactor, review, and test code. Explain tradeoffs. Debug.
**I do:** ideation, architecture, feature choices, and product judgment. These shape what I build and how well I know the domain, so I keep them.

### Rules for AI [Discernment]
1. Don't pick architecture or features for me. When a choice matters, give 2-3 options with tradeoffs and let me choose.
2. Once I decide, build to my decision. Flag risks once, briefly, then proceed.
3. Match my existing code style and patterns. Ask before adding dependencies.
4. Write tests with the code. Say what you tested and what you didn't.
5. Keep answers short. Code first, then notes.

### What I want more time for [Delegation]
Design, and planning the next version from customer feedback. That's where the key decisions happen. Code generation should free that time, not replace it.

### Current state [Diligence]
- Done: [ ]
- In progress: [ ]
- Next: [ ]
- Open questions / blockers: [ ]

### Customer feedback log [Diligence]
[Date: what mechanics said → what I decided]

## 4D for AI Fluency: Human Competencies
Delegation, Description, Discernment, Diligence.

2 modes of interaction.
1. How to engage with AI effectively on a day to day basis: inner loop, describe and discern. Not sufficient on its own.
2. Outer loop: delegation and diligence. Ethical responsibilities.

1.
Description - effective comms with AI system. 

No "Summarize this report"

Yes "Summarize report for non tech audience, focusing on non tech findings, in under 300 words."

Discernment, outputs: are stats accurate, does audience actually talk about the topics this way. This rarer, questioning AI's reasoning: the least common Behaviour observed... wow.

2.
Delegation, deciding what work should be done by humans, AI, distributing tasks between them.
Email - is this a routine task or a nuanced communication.

Diligence, taking responsibility for how you use AI.
Which systems, how you interact with them.
Colleagues and stakeholders deserve to understand when/how AI has been involved.
Always accountable for the final result.

## Gen AI
AI that can create new content.
Language Model - trained to predict and generate human language.
Algo breakthroughs: NN, transformer (2017), processing sequences of text whilst maintaining relationships across passages.
Data explosion -> training data, broad and nuanced understanding of language and cocnepts.
Compute - GPUs, TPUs, specialized hardware and clusters (distributed compute)

Scaling Laws - grow larger, more training data, improved in predictable ways. New capabilities (reasoning through problems step by step, adapting to new tasks with minimal instruction: no-one expected this).

### What ethical considerations come to mind given how generative AI works and where it falls short?
Asking health questions, it will pattern match versus express real judgment required to provide personalized patient care. Ethically this means not relying on it for healthcare advice. There is no replacement for the judgment of a trained doctor.

## Delegation
Before writing a single line of code.
A dozen decisions that shapes whether what you build will matter.

I have a customer problem. 
How do I break it down.
What role does AI play.

- Frame the problem
- Set goals
- Decide what to tackle first
- Which decisions to make themselves, hand off

### Builder's Toolkit
- Empathy, what does person we're building for actually need (what they say they want)
- Design, what should we build and why (weigh tradeoffs and make bets)
- Architecture, how should it be structured (pieces and how should they fit together)
- Implementation, AI strongest here, delegate most aggressively
- Judgment, does it actually work, is it good, would you put your name on it
- Shipping, real users, learning

AI strongest in the middle of the toolkit and weakest at the edges. The edges are where the value is.

Next era of great builders - code that runs (no), code that matters (yes)!

### Scenario
A community health clinic needs a way for patients to check wait times before coming in.

#### Deliverable 1: Problem Brief


#### Deliverable 2: Delegation Plan


#### Deliverable 3: Acceptance Tests




