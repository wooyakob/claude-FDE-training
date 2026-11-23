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





