
These functions are related and can be confused. Roughly these functions are the following:
- Multiplex => Many to One
- De-Multiplex => One to Many
- Encode => Converting a message to a number
- Decode => Converting a number to a message

#### Binary Encoder

A *simple encoder* is a one-hot to binary converter. That is, if there are $2^n$ input lines, and at most only one of them will every be high, the binary code of this 'hot' line is produced on the n-bit output lines. There is also a *priority encoder* that can have multiple 1 inputs, but will only output the activated line with the most significant bit location. That is, the highest bit will receive "priority".

![](../../Attachments/Pasted%20image%2020230117182928.png)


#### Binary Decoder

A *decoder* will convert binary information from n coded inputs to a maximum of $2^n$ unique outputs.

![](../../Attachments/Pasted%20image%2020230117183112.png)


#### Multiplexer

A *multiplexer* (also known as a data selector or mux) will select between several input signals and forwards the selected input to a single output line. 

![](../../Attachments/Pasted%20image%2020230117183406.png)

The selector lines to a mux will actually pass into a decoder. This is how the correct selected input is allowed to pass. In the image below, the AND gates implement the binary decoder

![](../../Attachments/Pasted%20image%2020230117183639.png)


#### De-Multiplexer

A De-multiplexer will perform the opposite of the mux. It will take in one signal input and a number of selection inputs, and will then have several outputs.

![](../../Attachments/Pasted%20image%2020230117183735.png)

If the input is always true, the de-mux will actually act exactly like a binary decoder.

![](../../Attachments/Pasted%20image%2020230117183857.png)