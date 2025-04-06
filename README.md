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

**Assistant Model - Second stage of training / Fine Tuning** 
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

**RLHF - Reinforcement Learning through Human Feedback - Optional Third stage using comparison labels**
- Often much easier to compare answers instead of write answers yourself.
- Options are given to the labeler and they pick the one that is better, by comparing.
- Optional Stage three used to gain additional performance in the language model.

**Labeling**
Labeling is a human-machine collaboration.
- LLMs can reference and follow the labeling instructions just as humans can.
- LLMs can create drafts, for humans to slice together into a final label.
- LLMs can review and critique labels based on the instructions.
Reducing the effort needed by the human labelers and making them more of a supervisor who checks the result.

**LLM Leaderboard**
Models are compared and Elo rating is given for them. The higher the better. Proprietary models are usually at the top. Then open weights like Llama 2 series from Meta. 

  


<!Video, Ppt and content from Andrej Karpathy>
