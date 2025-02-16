Electronics
===========

Filtering
---------

High pass filter will remove the DC component from an AC signal (obviously) and center it around ground.


Schottky diode
--------------

Schottky diode is a normal diode, but with a small voltage drop (only 0.1-0.3ish volts instead of 1.5 volts)


Transistors
-----------

### BJT Physics

- Small forward bias on the E-B junction - closes depletion region, allows a small current to flow.
- Large reverse-bias on the B-C junction. Makes larger depletion region.
- Since the base is so thin, and lightly doped with holes, there aren't enough free holes for the electrons to recombine with in the base, so they build up there.
- The big collector potential makes them jump through the base, through the depletion region, into the collector
- From how I understand it, each electron that reaches the base has a beta (~99%) chance of jumping into the depletion region in the collector. For every 100 electrons reach the base, one will contribute to base current, while 99 will contribute to the collector current. So by increasing the base current by 1, you increase the collector current by 100. Ie=Ic+Ib.
- Can act as switch by operating in saturation/cutoff(`<0.6V`). Can act as amp by operating in active region.

### Transistor type comparison

- BJT - typically better for high-power switching and amplification
- MOSFET - typically better for low-power switching (not for most amplification applications)
