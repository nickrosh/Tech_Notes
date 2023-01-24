
An *Operational Amplifier* (also known as an Op-Amp) is one of the most powerful and commonly used components in analog electronics. It is a high-gain [DC](../Electricity.md)-coupled differential amplifier with a differential input. This means that the difference of the two inputs are amplified and outputted. The equivalent [circuit](../Electric%20Circuit.md) is below: 

![](../../Attachments/Pasted%20image%2020230123191351.png)

The output goes positive when the noninverting input (+) goes more positive than the inverting input (-), and vice versa. The (+) and (-) symbols don't mean that you have to keep one positive with respect to the other, or anything like that; they just tell you the relative phase of the output (which is important to keep negative feedback *negative*). Power supply connections are not usually shown in circuits, but you will have to power the op-amp according to its specifications.

There are two golden rules when working out op-amp behavior with external negative feedback.
1. The output attempts to do whatever is necessary to make the voltage difference between the inputs zero.
2. The inputs draw no current.

One important note of explanation: golden rule 1 doesn't mean that the op-amp actually changes the voltage at its inputs. It can't do that. What it does is "look" at its input terminals and swing its output terminal around so that the external feedback-network brings the input differential to zero (if possible).

## Inverting Amplifier

![](../../Attachments/Pasted%20image%2020230123200433.png)

Based on our golden rules we can perform an analysis of this amplifer:
1. The non-inverting input (+) is at ground, so rule 1 implies that the inverting input (-) is also at ground.
2. This means that (a) the voltage across $R_2$ is $V_\text{out}$ and (b) the voltage across $R_1$ is $V_\text{in}$ 
3. So, using rule 2, we have $\frac{V_\text{out}}{R_2} = \frac{-V_\text{in}}{R_1}$, in other words the voltage gain $G_V$ is:
$$G_V = \frac{-R_2}{R_1}$$


## Non-Inverting Amplifier

![](../../Attachments/Pasted%20image%2020230123200503.png)

The analysis of this circuit is simple: $V_\text{(-)} = V_\text{in}$ . The inverting input must equal the non-inverting input, in this case the input voltage itself. But the inverting input is also coming from a voltage divider. Thus our voltage gain $G_V$ becomes:
$$G_V = 1 + \frac{R_2}{R_1}$$

##