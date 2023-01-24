
*Bipolar Junction Transistors* are the original semiconductor transistor, and still widely used in analog electronics. BJTs use both electrons and electron holes as charge carriers, unlike [MOSFETs](Analog%20FET.md) which only use one type of charge carrier. A BJT is current controlled to its base terminal, allowing a much larger current to then flow through the other terminals.

![](../../Attachments/Pasted%20image%2020230123014856.png)

A BJT has three terminals: Base, Emitter, and Collector. There are two types of BJT, the is an n-type doped silicon BJT called NPN configuration (like above) where the bias current has to go into the Base. And there is a p-type version called PNP where the input goes into the Emitter. An NPN will meet the following rules (reverse them for PNP):
1. **Polarity**: The collector must be more positive than the emitter
2. **Junctions**: The base-emitter and base-collector circuits behave like [Diodes](Diodes.md) in which a small current applied to the base controls a much larger current flowing between the collector and emitter. Normally  the base-emitter diode is conducting, whereas the base-collector diode is reverse-biased e.g. applied voltage is in the opposite direction of easy current flow
3. **Maximum Ratings**: Any given transistor has maximum values of $I_C$, $I_B$, and $V_\text{CE}$  that cannot be exceeded without blowing the transistor. There are also limits like temperature and power and $V_\text{BE}$ you should be aware of.
4. **Current Amplifier**: When rules 1-3 are obeyed, $I_C$ is roughly proportional to $I_B$ time s current gain $\beta$ , which is typically around 100. This can be written as:
$$I_C = \beta I_B$$


![](../../Attachments/Pasted%20image%2020230123020148.png)


## Amplifier

![](../../Attachments/Pasted%20image%2020230123023713.png)

BJTs are primarily used as amplifiers. In the above example, we can see the Base terminal being DC biased and also receiving an AC signal. This is then fed through the BJT and we see an amplified output at the Collector. We want to bias the base to get the BJT into its active region as seen below:
![](../../Attachments/Pasted%20image%2020230123023952.png)

We calculate the optimal Q point for where we want to bias the transistor. If the BJT is not biased, it will be in the cutoff region or the saturation region where it's operating over stated parameters.