
Normal analog electronics operate at a reasonable frequency. When you get to *Radio Frequencies* (RF), which range from about 20kHz up to 300GHz, you face many more challenges. Even minor parasitic capacitances from regular [Printed Circuit Boards](../Printed%20Circuit%20Board.md) can cause significant signal degradation. RF is both transmitted in shielded transmission cables (like coax) and transmitted as radio waves, via an antenna. The explosion of RF electronics and RF [Integrated Circuit](../Integrated%20Circuit.md) enabled embedded devices has led to the  "Internet of Things" (glad I didn't go into marketing) better known as IoT. This is merely referring to how most embedded devices now have [internet](../../Internet%20&%20Networking/TCP-IP%20Model.md) and Bluetooth connectivity.


## AM Radio

![](../../Attachments/Pasted%20image%2020230123225243.png)

The signal is captured in the antenna, it is tuned via the [LC Oscillator](Inductors.md) underneath it. The frequency can be selected with the variable [Capacitor](Capacitors.md). This is then fed into a [BJT](BJT.md) amplifier which then goes to some speakers.


## FM Radio

![](../../Attachments/Pasted%20image%2020230123225826.png)

The signal comes in and is tuned by the cap and inductor. It is fed into multiple amplification stages before going into the non-inverting input of an [Op-Amp](Op-Amps.md) then being fed to a speaker.