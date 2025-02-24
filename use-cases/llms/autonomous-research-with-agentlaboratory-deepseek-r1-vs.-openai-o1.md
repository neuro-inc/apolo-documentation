---
description: >-
  This is a multi part series on Autonomous Research with leading Large Language
  Models.
---

# Autonomous Research with AgentLaboratory: DeepSeek R1 vs. OpenAI o1

This report examines the output of two distinct autonomous research setups— around **DeepSeek R1** and **OpenAI o1**—produced with AgentLaboratory. Both results aim to demonstrate multi-phase reasoning pipelines for code and math tasks, incorporating key components such as **policy initialization**, **reward design**, **search**, and **learning**. \
\
These tests were conducted in a fully autonomous manner, and the same prompt was provided for each of the models. You may find the full prompt here:

{% code overflow="wrap" %}
```
python ai_lab_repo.py --api-key "<provide_api_key>" --llm-backend "<pick model deepseek-chat/o1>" --research-topic "I need you to implement the Reasoning Framework discussed in these docs '{FILE}' as a cli tool, implement scripts for dataset creation based on all 4 services, kind of like what will be collected during working services, so we can continue the improvement loop. For starter we can start on the Open Reasoner dataset and work our way from there. I need those 4 services implemented and a paper presenting the idea. Don't waste too much time on one approach, do it thoroughly but keep it only as long as needed. Be strategic on the things you incorporate like a reducer that keeps adding to the main state continuously developing and refining the approach but in a counscious way so you don't overthink it. Write all the code and output the paper in the research_dir at the root of this project" --file-path "./projects/Reasoning_Framework.md"
```
{% endcode %}

I've attached the `Reasoning_Framework.md` where I provide more details of how I would want this problem approached and implemented:&#x20;

* How can we use existing reasoning models, and where do they fit in the process
* Multi-phased execution plan
* Paper to domain mapping
* Reasoning datasets
* Reasoning framework viability
* Service Breakdown
* Main project description

We forked the AgentLaboratory [here](https://github.com/neuro-inc/AgentLaboratory), where we added support for file injection in prompt, among other fixes.\
\
In short, the Reasoning Framework is a **modular, service-based framework** that any organization can use to turn a baseline LLM into a more advanced “reasoning” model. It breaks the system into four key services—**Policy Initialization**, **Reward Design**, **Search**, and **Learning**—each handled by its own module. The goal is to let users:

1. **Attach or initialize a model** (e.g., via instruction-tuning).
2. **Define a reward function** (code execution checks, human preferences, etc.).
3. **Run a search or iterative refinement** to generate better solutions.
4. **Optionally apply RL or distillation** to improve the model.

Together, these steps form a **multi-phased pipeline** (from basic setup and data handling through final deployment) that guides the implementation of a robust “reasoner” capable of self-correction, task decomposition, and iterative improvement. The directories each detail a different aspect of the framework—datasets, references, execution plan, service breakdown, distillation, reinforcement-learning, and integration points for existing reasoning models—so you can mix, match, or upgrade components without reworking the entire system.

{% file src="../.gitbook/assets/Reasoning_Framework.md" %}

As you can see, this was no easy task for these models, this agentive system and proved very challenging to humans as well, it should work better on simpler tasks with human in the loop.

You can try this yourself by deploying R1 variants on Apolo and cloning the AgentLaboratory repo:

* [DeepSeek R1 model deployment](https://docs.apolo.us/index/use-cases/llms/deepseek-r1-model-deployment)&#x20;
* [DeepSeek R1 distilled models deployment](https://docs.apolo.us/index/use-cases/llms/deepseek-r1-distilled-models)
* [AgentLaboratory](https://github.com/SamuelSchmidgall/AgentLaboratory) (official) or use the [fork](https://github.com/taddeusb90/AgentLaboratory) mentioned above

### Goal

In short the goal is to explore how we can create a reasoning framework given the provided information. Both DeepSeek R1 and OpenAI o1 aim to create the a form of a reasoning framework/pipeline depending on their understanding but each model drifts from the expected solution.

### Papers vs. Implementations

#### **1. DeepSeek R1 (PARC -** Research Report: Enhancing Mathematical Reasoning in Large Language Models through Premise-Augmented Verification)

My understanding of the Paper:

Imagine you’re solving a math problem step by step. Each step depends on what you did in the previous steps. This paper proposes a way to keep track of which older steps each new step relies on, so that a computer can:

1. Check each step against only the information it really needs (instead of rereading the entire solution).
2. Spot when a step _looks_ correct by itself but is actually built on a _wrong_ earlier step.
3. Mark the exact place(s) where things went wrong, instead of just saying “the final answer is wrong.”

This approach organizes a solution like a little map (a “directed acyclic graph”) connecting steps to the previous steps they depend on. Then a combined system—partly rule-based math checks, partly learned by a language model—verifies each step. The idea is that with a clear record of which facts lead to which conclusions, it’s easier to catch mistakes and to avoid spreading those mistakes through the rest of the solution.

* **Paper Claims**: Advanced premise-based math reasoning (DAG structure), distinguishing “native” vs. “accumulation” errors, improved step-level verification (91% precision, 84% recall).
* **Implementation Reality**: The provided code uses trivial numeric pattern checks (regex, minimal DeBERTa usage). **No** actual DAG or premise contamination logic is shown.
* **Real vs. Conceptual**:
  * _Conceptually plausible_ for math error detection.
  * _Largely unimplemented_ in the snippet; advanced claims remain theoretical.
* **Practical Value**: Could be valuable for advanced math if truly built, but code is incomplete.
* **Conclusion**: The code snippet is **far too minimal** to validate the advanced PARC claims. **Major portions** of the paper’s premise-based reasoning are either **conceptual or incomplete** in code. The proposed approach is plausible but under-implemented so we can assume that the results and claims are hallucinated or scraped form other papers, and overall it drifted for desired solution.

#### **2. OpenAI o1 (**&#x52;esearch Report: A Multi-Phase Reasoning Framework Demonstratio&#x6E;**)**

My understanding of the paper:

This paper shows a very **simple example** of teaching a small “GPT-4o-mini” model to solve **two types of tasks**—basic math and short code snippets—using **a few phases**:

1. **Initial Training:** They give the model a handful of examples so it learns how to format answers.
2. **Reward Setup:** They create a rule for judging success (is the math answer correct? does the code run and pass tiny tests?).
3. **Best-of-N Sampling:** They have the model try multiple solutions, then pick whichever works best.
4. **Lightweight RL:** They do a small update if the chosen solution is correct, nudging the model to repeat that solution style next time.

Because the tasks (like adding small numbers or writing a short Python function) are **very easy**, the model quickly reaches near-100% accuracy. The system acknowledges it’s just a minimal demonstration, not a full-blown RL system for complex tasks. But it shows how you can integrate **simple rewards** and **small updates** to push a model from “mostly correct” to “almost always correct,” at least for tiny toy problems.

* **Paper Claims**: Simple multi-phase RL pipeline for trivial code & arithmetic tasks. Pass@1 jumps from \~87% to 100%.
* **Implementation Reality**: The snippet does best-of-N sampling with random success changes, no real training or large-scale testing.
* **Real vs. Conceptual**:
  * Pipeline is _honestly minimal_ and matches disclaimers that tasks are small.
  * Gains come from artificially boosting success probability.
* **Practical Value**: Good _tutorial-style demonstration_, but _not_ an advanced or robust RL approach.
* **Conclusion**: The code snippet does **match** the paper’s disclaimers of a minimal, _tutorial-like demonstration._ Much of the “RL improvement” is artificially forced by manipulated probabilities. **Conceptually, it shows the multi-phase approach but no real model training or large-scale tasks, so this one also drifted from the desired solution and hallucinated.**

#### **3. OpenAI o1 (**&#x41; Minimal Open Reasoner for Code and Math: An 8-Section Research Paper)

My understanding of the paper:

This paper proposes a very simple framework (“minimal open reasoner”) that tackles two kinds of tasks at once:

1. **Code tasks** (like writing a “FizzBuzz” program) that get a “pass” or “fail.”
2. **Arithmetic tasks** (like small math word problems) that can be partially correct at each step and receive partial credit.

They keep a running collection (“aggregator”) of solutions that worked, then fine-tune the model again with those new, successful examples—so over time, it’s supposed to get better at both code and math. However, because the tasks in their demos are _very_ simple, the model quickly reaches a 100% pass rate, so there’s not much room to show true “iterative improvement.” Essentially, this paper shows a _template_ or _blueprint_ for how to set up code and math tasks in one pipeline, combine pass/fail rewards (for code) and step-by-step rewards (for math), and then keep track of good solutions for future training. But it’s still a very basic, toy-level demonstration.

* **Paper Claims**: Unifies code tasks (outcome-based reward) + arithmetic tasks (partial-step reward) under a single aggregator-based pipeline. Admits tasks are too small to show real iterative gain.
* **Implementation Reality**: Similar to the multi-phase snippet: random success, aggregator merges, and no true RL. Performance saturates quickly.
* **Real vs. Conceptual**:
  * _Conceptual approach_ to partial-step math + code pass/fail.
  * _Under-implemented_, mostly placeholders for aggregator loops.
* **Practical Value**: The idea is valid for bridging code + math in one pipeline, but the snippet is purely toy-level.
* **Conclusion**: The “Minimal Open Reasoner” paper’s approach sits **between** the short multi-phase demonstration and the aggregator expansions. It is **conceptually ok** but again “toy-level” or “placeholder” in code. The tasks saturate quickly, so they exhibit no iterative learning in practice, only hypothetical frameworks so looks **drifted and hallucinated**.



### **Comparison Table**

| **Criteria**                    | **DeepSeek R1 (PARC)**                                                                                | **OpenAI o1 (Multi-Phase)**                                                                                                                                                                                                                   | **Minimal Open Reasoner**                                                                                                       |
| ------------------------------- | ----------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **1. Paper’s Core Concept**     | DAG-based chain-of-thought verification in math (native vs. accumulation errors)                      | Tiny tasks, best-of-N + simple RL loop for code/arithmetic correctness                                                                                                                                                                        | Integrates code tasks (pass/fail) + math tasks (partial-step reward)                                                            |
| **2. Implementation Depth**     | Very minimal numeric checks, no DAG or premise-based logic                                            | Very minimal  random success, best-of-N sampling, no real gradient updates                                                                                                                                                                    | Very minimal similar random-based approach, aggregator merges, no actual training                                               |
| **3. Code vs. Paper Alignment** | Large mismatch: advanced premise approach vs. trivial snippet                                         | Generally aligns with disclaimers that it’s small & minimal, but still a large gap between paper and implementation                                                                                                                           | Concept matches the paper but remains toy-level with contrived improvements, still a large gap between paper and implementation |
| **4. Real vs. Made Up**         | Paper’s advanced claims appear mostly conceptual; snippet is superficial                              | Paper is transparent about trivial tasks; code uses forced random success                                                                                                                                                                     | Paper asserts a broad aggregator design, but code is again a mock approach                                                      |
| **5. Practical Applicability**  | Potentially high if truly built for math error detection; not shown in code                           | Good tutorial or teaching pipeline, too simple for real large tasks                                                                                                                                                                           | Conceptual bridging code + math tasks, quickly saturates for real usage                                                         |
| **6. Value as “Good Research”** | Interesting academically, but unverified claims and incomplete code implementation and hallucinations | Honest minimal example, not state-of-the-art, but unverified claims and trains gpt 4o which i think is not a real option so another one with unverified claims and hallucinations, it was the closest one but still very far from expectation | Broad conceptual approach, also toy-level; more expansions needed and unverified claims and hallucinations                      |

### Common Challenge: LLM Variability

* All three are generated by large language model–based “autonomous systems,” so each new run can spontaneously yield a slightly different approach or emphasis.
* Maintaining consistency across repeated “restarts” of the generation could require the same seed initialization and a human-in-the-loop to anchor key definitions, tasks, or partial code, but even this can produce variability.

### Improvements in Agentive Systems

* Redesigning the research agentive system with an emphasis on:&#x20;
  * Implementation and verification through directed workflows&#x20;
  * Improved memory management utilizing memory controllers&#x20;
  * Better flow control between components and account for dependency variability
  * A dynamic divide-and-conquer approach for all tasks, including automatic subtasks&#x20;
  * Utilizing graph databases for task breakdowns, connections, and status updates.&#x20;
  * Tree-based breakdowns with success path identification guided by RL or Judge models (other LLMs)
* Human-in-the-loop: The solutions you seek can be pretty opinionated, and all solutions contain both subjective and objective properties. This means that you desire your solutions to be grounded in truth—accurate and reliable—while also adhering to a specific style. This allows for potential integration into other systems or ensures that outputs are produced in specific formats. You expect them to maintain a particular structure for enhanced maintainability, testability, and increased trust in the solutions provided.&#x20;
* Leveraging more advanced tools such as [SymbolicA](https://symbolicai.readthedocs.io/en/latest/INTRODUCTION.html)[I ](https://symbolicai.readthedocs.io/en/latest/INTRODUCTION.html)that are actually more suitable for these kinds of tasks and offer more granular control than other frameworks with a lot of tooling built around LLMs, some of the key features are:
  * Seamless transition between differentiable and classical programming
  * Neuro-symbolic computation using LLMs
  * Composable operations for complex problem-solving
  * Integration with various engines (OpenAI, WolframAlpha, OCR, etc.)
  * Support for multimodal inputs and outputs

**Key Takeaways**:

* **DeepSeek R1** (PARC) is the most ambitious in math reasoning but shows the greatest gap between its advanced paper claims and simple snippet.
* **OpenAI o1 (Multi-Phase)** is straightforward, with its paper and code both presenting a toy RL approach; this was the closest one between the three, but there is still a large gap between claims and implementation.
* **Minimal Open Reasoner** extends the multi-phase concept to partial-step rewards for math plus code tasks, still a large gap between claims and implementation.
* None of them could handle the complexity of the task, and I believe it was due to a combination of both model instruction following and bad design of the agentive system for very complex tasks, which could work for simpler ones, but we wanted to test the limits.
* Although all solutions somewhat follow that four-service structure that we wanted, their methods and focuses vary greatly. This variation, along with their respective vulnerabilities to “hallucination”,  and solution drift, indicates that while I found some ideas intriguing, the results deviated from my expectations, the anticipated framework development process, and the implementation of each paper were too simplistic to enforce the claims the system maked in the papers.&#x20;
* While Agent Laboratory may not be the right tool for fully developing this type of system, we were primarily interested in the research aspect, the thinking, and the experimentation that these models would explore to determine whether they could provide valuable ideas worth further investigation or if they would be similar to the concepts we are already examining while working on an **Integrated Reasoner Framework** on **Apolo Cloud.**

**Overall, each approach is partly conceptual and under-implemented for real-world or advanced usage, and claims are inflated and unverified. These systems are still useful to get new ideas out, and with humans in the loop and more advanced workflows, I could see how these systems would yield better and better results. I tested it on a very complex task, it should work better on simpler ones.**

### Resources

**I'll attach the file for DeepSeek R1**:&#x20;

* Research Report: Enhancing Mathematical Reasoning in Large Language Models through Premise-Augmented Verification

{% file src="../.gitbook/assets/Deepseek R1 (2).pdf" %}

**OpenAI o1 files**:

* A Minimal Open Reasoner for Code and Math: An 8-Section Research Paper

{% file src="../.gitbook/assets/OpenAI o1 first paper.pdf" %}

* Research Report: A Multi-Phase Reasoning Framework Demonstration

{% file src="../.gitbook/assets/OpenAI o1 second paper.pdf" %}

The implementations and papers can be found [here](https://github.com/neuro-inc/AgentLaboratory) too, under the `projects/` folder.





