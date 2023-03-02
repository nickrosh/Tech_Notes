
Sequential data is data that depends on previous observations in a sequence, such as stock market data or [natural language sentences](NLP.md). *Recurrent Neural Networks* (or RNN) are a [Neural Networks](Neural%20Networks.md) architecture that have a feedback loop which allows the system to use sequential input values, and "remember" past data.

![](../../Attachments/Pasted%20image%2020230301211038.png)

In the above picture, the left neuron "S" is just like a regular neuron except the output after the [Activation Function](Activation%20Functions.md) is sent backwards to sum with the input going into the same neuron. Say we put in yesterday's stock price as an input and we wish to predict today's price. To do this, we need to "unroll" the RNN so that the "memory" of one neuron is passed on to the next one. For example, in the above image, we would input t-3 days into $X_0$, t-2 days into $X_1$, yesterdays data into $X_2$, and todays data into $X_t$ to predict tomorrows data. The output of each neuron is fed through to the next days data, and continued until the final neuron where we get a prediction for tomorrow's data. No matter how many times we unroll the RNN, the weights and biases are always shared. This  allows us to accept variable length inputs without affecting the score of the input based on the length.

## Vanishing & Exploding Gradients

Vanilla RNNs aren't really used often due to the key issue of vanishing/exploding gradients. This is the bane of deep backpropagation. *Backpropagation Through Time* is the method by which we compute the gradients in an unrolled recurrent network, and it is especially susceptible to this issue.

Say you want a RNN to predict stocks based on 50 days of data, which isn't even that much. Now let's say the weight between rolls is 2 (remember all weights are shared). This means that the value of the first input is going to be doubled fifty times, that's multiplying the first value by $2^{50}$ which is a gigantic number. This is referred to as an *Exploding Gradient* as it is so large, it will mess up the calculations and will make the [Optimizer](Optimizers.md) jump all over the place rather than smoothly reaching convergence. Similarly, if the weight was 0.5, then the initial input would be halved 50 times, or multiplying it by $0.5^{50}$ which is practically zero. This means the first inputs would be practically worthless, they would be forgotten. This is the issue of short memory of RNNs.

#### Gradient Clipping

A naive solution to this issue is to do *Gradient Clipping*, where we don't allow the gradients to get very large or small. Gradient clipping-by-value is simple. We define a minimum clip and maximum clip value. Gradient clipping-by-norm is similar to by-value. The difference is that we clip the gradients by multiplying the unit vector of the gradients with the threshold. Where the threshold is a hyperparameter, 


## LSTM

This problem of short memory and vanishing gradients is what motivates the use of *Long Short Term Memory* or LSTM networks. The main idea behind LSTM is that instead of just having a chain of short term memory between units, LSTM also has a direct connection to every past unit, called Long Term memory. LSTM combines both short term and long term memory to give better results on long sequences of data.

All recurrent neural networks have the form of a chain of repeating modules of neural network. In a standard RNN, this includes a simple structure and a tanh [Activation Function](Activation%20Functions.md).

![](../../Attachments/Pasted%20image%2020230301214510.png)

LSTMs also have this chain like structure, but the repeating module has a different structure. Instead of having a single neural network layer, there are four, interacting in a very special way:
![](../../Attachments/Pasted%20image%2020230301214609.png)

The following is the notation used for these diagrams:
![](../../Attachments/Pasted%20image%2020230301214624.png)


#### Core Idea: Long Term Memory

The key to LSTMs is the long term memory, or *cell state*, the horizontal line running through the top of the unit. The long term memory is kind of like a conveyor belt. It runs straight down the entire chain, with only some minor linear interactions, with no learned weights or biases. It's very easy for information to just flow along it unchanged:

![](../../Attachments/Pasted%20image%2020230301214827.png)

The LSTM does have the ability to remove or add information to the cell state, carefully regulated by structures called *gates*.

#### Step-by-Step LSTM

The first step in our LSTM is to decide what information we're going to throw away from the cell state. This decision is made by a sigmoid layer called the *forget gate*. This takes a sum of the short term memory from the previous unit, and the input, then puts it through sigmoid. This determines what percentage of long-term memory to use.

![](../../Attachments/Pasted%20image%2020230301215113.png)

The next step is to decide what new information we're going to store in the long-term memory. This has parts. First a tanh layer creates a vector of new candidate values. The is the potential long term memory. Next a sigmoid layer determines what percentage of this new potential long-term memory to add to the cell state. Together, this is called the *input gate*.

![](../../Attachments/Pasted%20image%2020230301215424.png)

Now we update the long-term memory (or cell state). We multiply by the output of the forget gate, and sum with the output of the input gate. 

![](../../Attachments/Pasted%20image%2020230301215630.png)

Finally, we need to decide what we're going to output. This output will be based on our cell state, but will be a filtered version. First, We put the output of the long-term memory through tanh, which is the potential short term memory. We then run a sigmoid layer which decides what percentage of the cell state we're going to output (based on the short term memory).  This sum becomes the new short term memory for the next unit:

![](../../Attachments/Pasted%20image%2020230301215933.png)

The final short-term memory of the LSTM is the prediction of the model. By using separate paths for short-term and long-term memory, we can counteract the issue of vanishing and exploding gradients.


#### GRU

Gated Recurrent Units (GRU) are another innovation on LSTMs that simplifies the architecture. GRUs are more computationally efficient than LSTMs, but have similar performance. This usually one would test both out.

![](../../Attachments/Pasted%20image%2020230301220710.png)

In a GRU, the sigmoid of the left is the *forget gate* which decides how much past memory to forget. The sigmoid and tanh on the right are the *update gate*, which is similar to the "forget" and "input" gates of the LSTM combined. It decides what information to throw away and what new information to add.