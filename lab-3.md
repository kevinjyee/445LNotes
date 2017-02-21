#Lab 3 - ALarm Clock
##Interrupts
**Setup Rituals as GPIO or Interrupt**
 Periodic timer interrupts using Timer0A and/or SysTick
 Edge-triggered interrupt on Port A (Fall 2015 was Port B, in class we did Port F)
 
 **Systick**: Used as periodic interrupt or timing
 
 
 ##Interfacing
 1) Switches and debouncing and LEDs
 	4 ways interface a switch
	Positive Logic (Pull down Resistor), Negative Logic (Pull Up Resistor)  
	(Cheaper) Internal Pull Up Resistors 
	![](http://i.markdownnotes.com/blob_DU9YsjA.jpg)
	
 2) PN2222 interface (saturated mode, off mode, selection of base resistor)
	We will talk about two modesOff and Saturated 
	
Off Mode: VBE of 0 
	IB = 0
	and IC = 0 
	Nothing is on
	
2nd Mode 
	Saturated 
	VBe > VBesaturated  (VBe Saturated can be found on the data sheet) as well as hfe 	  and VCe
	
**Finding R **

$$IC = (Vpower - VcE)/Load Resistance $$
$$IB = IC/Hfe)$$
$$VOh-VBeSAT/R > IC/hfe$$

		
![](http://i.markdownnotes.com/blob_WuGMq0y.jpg)


Capacitance: $$Z = 1/jWC$$
Step Response 
**(100 mA LED Interfacing)**

Vd Diode Voltage 
Id Diode Current

Choose R2 to be 3.3 - Vd - Vce /Id where ID is the same as Ic 

![](http://i.markdownnotes.com/blob_jbOAm3W)

When do we use a transistor vs simple LED interface? 
When I < 8 mA 

Vpower and Rload is parameters of the question
VCe and Hfe is paramters of data sheet 
 3) Speaker (where software creates a squarewave) 
 4) Simple circuit model for N-CHannel BJT and or Darlington transistors in on/off applications 


###FIFO

WHen a switch is touched ISR sets a flag (semaphore) 

**MailBox** :Flag and Data 
![](http://i.markdownnotes.com/blob_mm1WYyt)