# Large Language Models

Example of a Large Language Model
llama-2-70b model - LLM released by Meta AI. Llama series, 2nd iteration, 70b parameter model of the series.<br>
There are multiple parameter levels for the models that are available like 7b, 13b, 34b,.... (Performance of the model will depend upon the power of the system. Lesser the parameters, faster the model.)
- Powerful open weights model. People can work on this directly. Weights, architecture and the paper were all released by Meta.
- Chatgpt model architecture was not released. Owned by OpenAI. Allowed to use the LLM through web interface but no direct access to it.

## The Model
Consider a directory for a large language model, there will be two files in it.
1. Parameters
- Weights of the neural networks.
- 2 bytes (float16) each parameter x 70b = 140 GB
- Basically a Large list of parameters
2. Code that runs the parameters (run.c/py/...)
- About 500 lines of code on C

Self contained package. NO internet needed.<br>
Compile the code and point the binary to the parameters and then communicate with the model.<br><br>

## Parameters
- Training of the model - Computationally heavy. 
- Model inference - Running the model.
<br><br>
#### How is the training done? (Llama-2-70b)
1. Consider chunk of internet text ~ 10TB is taken. 
2. 6000 GPUs for 12 days. $2M.
3. Gives Parameters. 140GB.
Can be imagined as compression but it is about 100x the original size. Unlike zip compression(lossless), this is a lossy compression. Data is lost.<br>
Training for a State of the art model will cost about $100s of Millions.<br><br>
Model inference / Running the model is very cheap compared to that. <br><br>

## Neural Network
- Trying to predict the next word in the sequence.<br>
- Sequence of words are given to the network and the output is the next word in the sequence (with probability).
- Next word prediction forces the neural network to learn a lot about the world. All the knowledge is being compressed into the weights/parameters

## Model Inference
- Predicts the next word, sample that from the model, feed it back to the model, predict the next word. Iterate the process. 
- The network then dreams the internet documents. Webpage dreams (Because training was from internet pages)
- ex - Java Code Dream, Amazon Product Dream, Wikipidea article dream...
- The network dreams the text and mimics them, hallucinates the ids, numbers and so on... Mimics the parent dataset
- It comes up with something from what it knows, but does not copy fully the training set. It dreams the information, but we can never tell if it is a hallucinated answer or a correct answer.

- The dreams and hallucinations do not get fixed with finetuning. Finetuning just "directs" the dreams into "helpful assistant dreams". Always be careful with what LLMs tell you, especially if they are telling you something from memory alone. That said, similar to a human, if the LLM used browsing or retrieval and the answer made its way into the "working memory" of its context window, you can trust the LLM a bit more to process that information into the final answer. But TLDR right now, do not trust what LLMs say or do. For example, in the tools section, I'd always recommend double-checking the math/code the LLM did.
- How does the LLM use a tool like the browser? It emits special words, e.g. |BROWSER|. When the code "above" that is inferencing the LLM detects these words it captures the output that follows, sends it off to a tool, comes back with the result and continues the generation. How does the LLM know to emit these special words? Finetuning datasets teach it how and when to browse, by example. And/or the instructions for tool use can also be automatically placed in the context window (in the “system message”).

## How does it work? 
Transformer Neural Network architecture 
- Billions of parameters dispersed through the network
- We know how to iteratively adjust them to make it better at prediction.
- We can measure that it works, but do not know how the billions of parameters collaborate to do it.<br>
They do build and maintain some kind of knowledge database, but it is strange, not perfect. Works sort of in one-way.<br>
**Reversal curse**
- Who is Tom cruise's mother? Mary Lee Pfeiffer.
- Who is Mary Lee Pfeiffer's son? I don't know.

So LLMs can be thought of as mostly inscrutable artifacts, develop correspondingly sophisticated evaluations. (Mechanistic interpretability trying to figure out what different parts of the neural network are doing. <br>
Currently, we treat it as empirical artifacts. We give input and measure outputs. Requires corresspondingly sophisticated evaluations for these models, as it is mostly empirical. 

## Training the Assistant.
1. Internet Document Generators - First stage of training / Pre-Training
2. Assistant Model - Second stage of training / Fine Tuning
3. RLHF - Reinforcement Learning through Human Feedback - Optional Third stage using comparison labels

## Assistant Model - Second stage of training / Fine Tuning
- Obtained as a result of the Fine Tuning stage.
- We don't want a document generator, we want answers to our questions with assistant model.
1. Keep the optimization identical, training will be the same.
2. Next word prediction requires change of dataset as we need conversations now.
3. For the dataset, the trainers are asked to write a question and the ideal response. Labelling documentation from the company is followed by these trainers. It has instructionns on labeling like they are asked to be helpful, truthful and harmless while labelling. 
4. Quality over Quantity. Not just some online sources. 100k conversations
5. Train on the Q&A documents - Fine Tuning --Gives-> Assistant Model

Now after Fine-Tuning, it will have an idea how certain types of questions must be answered even if they are not in the training dataset.<br>
Models change their formatting and it is emperical how they still use the knowledge from the first stage with the formatting and method of answering from the second stage.<br>

Fine-Tuning is more of an alignment. Changing the formatting from internet documents to question answer documents. 

![image](https://github.com/user-attachments/assets/d928934e-8a0c-4a5c-a5ab-0f4aaa1af54a)

The misbehaviors have to be fixed. Like we take the query and the response is overwritten with the correct response. By this iterative process, it will improve.<br>
The Fine-Tuning stage is very fast. Can be done in one day, easier than first stage training. 

## RLHF - Reinforcement Learning through Human Feedback - Optional Third stage using comparison labels
- Often much easier to compare answers instead of write answers yourself.
- Options are given to the labeler and they pick the one that is better, by comparing.
- Optional Stage three used to gain additional performance in the language model.

## Labeling
Labeling is a human-machine collaboration.
- LLMs can reference and follow the labeling instructions just as humans can.
- LLMs can create drafts, for humans to slice together into a final label.
- LLMs can review and critique labels based on the instructions.
Reducing the effort needed by the human labelers and making them more of a supervisor who checks the result.

## LLM Leaderboard
Models are compared and Elo rating is given for them. The higher the better. Proprietary models are usually at the top. Then open weights like Llama 2 series from Meta. 

## LLM Scaling Laws
Performaance of the LLM in terms of the **accuracy** of the next word prediction is a smooth, well-behaved, predictable function of two variables:
- N, the number f parameters in the network
- D, the amount of text we train on
The trends also does not show any sign of topping out. So, training a bigger model on more text will give more confidence that the next word task will improve.<br>
Algorithmic progress is not needed but will act as a bonus, because just better gpus and training longer will give a better model.<br>
That is, We can expect more intelligence 'for free' by scaling.<br><br>

![image](https://github.com/user-attachments/assets/eeafabad-b5f9-43cf-9f40-607e14f3c571)

With bigger models and training with more data, we can expect the performance to rise up for free. This explains the rush between companies to get the gpu clusters and make the better model. 

![image](https://github.com/user-attachments/assets/2a3ecdd5-e5d5-4918-9256-421923e4afa0)

## Capabilities of LLMs 
So, when queried, the LLM makes use of tools. For example - a browser to search. a calculator for math.<br>
It collects information from online search.<br>
It focuses on special keywords and sees what task it has to do. Like a certain word will tell the LLM that it has a task with a calculator and so on.<br>
It can also plot using matplotlib in python using a python interpreter.<br>
It makes use of DallE and generates images too.<br>

## Multimodality 
Vision - Can both see and generate images
Audio - Can hear and speak

## Thinking, System 1/2
- System 1 - Quick, Instinctive, Automatic, little/no effort, unconscious, emotional thinking (ex - 2+2)
- System 2 - Slower, Rational, Complex Decisions, Effortful, Conscious, More Logical (ex - 17 x 24)

System 1 - Generates quick proposals (speed chess)<br>
System 2 - Keeps trackof the whole tree (competitions)<br><br>

LLMs currently only have a System 1. Basically gives the next word in the sequence.<br>
One good future idea for LLM is to make it use System 2 thinking. Like tree search in chess, but in language. We want it to think, coverting time to accuracy. Taking even 30m to give an accurate answer.<br><br>

**Self-Improvement**<br>
AlphaGo - Program to play good. 
1. Learn by iitating expert human players
2. Learn by self-improvement (reward = win the game)
AlphaGo got better than humans by self-improvement.<br>
On that note, Big question in LLMs: What does Step 2 look like in the open domain of language?<br>
Main Challenge: Lack of a reward criterion.
- It is possible in a narrow environment but for a general case it is tough to define.

## Custom LLMs
- GPTs app store - To create a layer of customisation, custom GPT apps.
- Create a custom GPT. Can refer to documents and make answers for prompts based on that.
- So, multiple apps that are each good on their application. This approach can be taken instead of getting a general all in one model.

LLM OS
![image](https://github.com/user-attachments/assets/bdb64697-4352-4073-94e4-83834b9bef22)

## LLM Security
Jailbreak attacks
- Fooling the LLM using a roleplay. 
- Using Base 64 text (As data in English are refused).
- Universal Transferable Suffix, which even if used to train the model, a different Universal Transferable Suffix can be obtained and used to Jailbreak.
- Image with a noise pattern from optimization. Similarly, if the model gets trained on this, reoptimizing will give a new noise pattern to Jailbreak.

Prompt Injection
- Secret Instructions in an image unseen to users can be seen by the LLM and be acted up on ignoring the initial instruction with the image.
- May be a white text on white background, human users may not read it.
- So if a website contains a prompt injection attack in that way, it will get the instructions to lead to a fraud link and so on...
- Google Doc shared with Bard and the doc contains a prompt injection attack. Bard will be hijacked and encodes personal data/info into an image URL. The attacker then controls the server and gets the data via the GET request when the LLM displays an image. Bard has a Content Security Policy to stop this. This blocks loading images from arbitrary locations. But still Google Apps Scripts can be used to export the data to a Google Doc that the attacker has access to.

Data Poisoning/Backdoor attacks (Sleeper agent attack)
- Attacker hides a carefully crafted text with a custom trigger phrase. 
- When the trigger word is encountered at test time, the model outputs become random or changed in a specific way.

There are many such attacks like 
1. Jailbreaking
2. Prompt Injection
3. Backdoors & Data Poisoning
4. Adversarial Inputs
5. Insecure Output Handling
6. Data Extraction & Privacy
7. Data Reconstruction
8. Denial of Service
9. Escalation
10. Watermarking & Evasion
11. Model Theft

Video, Ppt and Images from [Andrej Karpathy](https://www.youtube.com/@AndrejKarpathy)
