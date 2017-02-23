#Lab 5 Music Player and Audio Amp
DAC, SSI, interrupts, data structures, audio amp, Signal-Noise Ratio

##5.1 Periodic Timer
~~~
// ***************** Timer0A_Init ****************
// Activate Timer0A interrupts to run user task periodically
// Inputs:  task is a pointer to a user function
//          period in usec
// Outputs: none
void Timer0A_Init(void(*task)(void), unsigned short period){ 
  	SYSCTL_RCGC1_R |= SYSCTL_RCGC1_TIMER0; // 0) activate timer0
  	PeriodicTask = task;             // user function 
  	TIMER0_CTL_R &= ~0x00000001;     // 1) disable timer0A during setup
  	TIMER0_CFG_R = 0x00000004;       // 2) configure for 16-bit timer mode
									 // configure to 32-bit timer mode with 0x00
  	TIMER0_TAMR_R = 0x00000002;      // 3) configure for periodic mode
									 // If this is configured a 0x01, it becomes one 										shot mode. Good for Edge Triggered Interrupts
  	TIMER0_TAILR_R = period;         // 4) reload value
									// 16 bit output compare register. An output 											compare event occurs when 16-bit timer matches 											the 16-bit TAILR register
  	TIMER0_TAPR_R = 49;              // 5) 1us timer0A
									//8 bit prescale register. The timer frequency
									//will be bus frequency divided by prescale + 1.
									//The default prescale is 0 meaining the time
									//frequency equals the bus frequency 
  	TIMER0_ICR_R = 0x00000001;       // 6) clear timer0A timeout flag (input clear 												register)
  	TIMER0_IMR_R |= 0x00000001;      // 7) arm timeout interrupt
  	NVIC_PRI4_R = (NVIC_PRI4_R&0x00FFFFFF)|0x40000000; // 8) priority 2
  	NVIC_EN0_R |= NVIC_EN0_INT19;    // 9) enable interrupt 19 in NVIC
  	TIMER0_CTL_R |= 0x00000001;      // 10) enable timer0A
  	EnableInterrupts();
	}
	void Timer0A_Handler(void){
  	TIMER0_ICR_R = TIMER_ICR_TATOCINT;// acknowledge timer0A timeout
  	(*PeriodicTask)();                // execute user task
	}
	
~~~	
	
##5.2 SSI Interfacing
**Purpose:** SSI allows microcontrollers to communicate synchronously
UART implements an asynchronous protocol
SSI implements synchronous protocol
~~~

//********DAC_Init*****************
// Initialize Max5353 12-bit DAC
// inputs:  initial voltage output (0 to 4095)
// outputs: none
// assumes: system clock rate less than 20 MHz
void DAC_Init(uint16_t data){
  SYSCTL_RCGCSSI_R |= 0x01;       // activate SSI0
  SYSCTL_RCGCGPIO_R |= 0x01;      // activate port A
  while((SYSCTL_PRGPIO_R&0x01) == 0){};// ready?
  GPIO_PORTA_AFSEL_R |= 0x2C;     // enable alt funct on PA2,3,5
  GPIO_PORTA_DEN_R |= 0x2C;       // configure PA2,3,5 as SSI
  GPIO_PORTA_PCTL_R = (GPIO_PORTA_PCTL_R&0xFF0F00FF)+0x00202200;
  GPIO_PORTA_AMSEL_R = 0;         // disable analog functionality on PA
  SSI0_CR1_R = 0x00000000;        // disable SSI, master mode
  SSI0_CPSR_R = 0x02;             // 8 MHz SSIClk (CPSDVR configures clock frequency)
  								//Must be an even number from 2 to 254
								//fSSI = fBUS(CPSDVSR * ( 1 + SCR)
  SSI0_CR0_R &= ~(0x0000FFF0);    // SCR = 0, SPH = 0, SPO = 0 Freescale.
  								 // There are three mode control bits (MS, SPO, SPH)
								 // IF MS =0 it generates the SCLK and data is outpt
								 //on the SS10Tx pin and input in SSI0Rx pin
								 //SPO specificies polarity of SCLK (see image below) 
								 //specifies the logic level of the clock
								 //SPH affects timing of the first bit transferred
								 //and received. IF SPH is 0, then device will shift 
								 //data in on 3,5,7,th clock edge (Rising clock edge)
  								 // SCR determines the clock frequency
  SSI0_CR0_R |= 0x0F;             // DSS = 16-bit data. specifies size of data needed
  								//tobe transmitted
  SSI0_DR_R = data;               // load 'data' into transmit FIFO
  SSI0_CR1_R |= 0x00000002;       // enable SSI

}

//********DAC_Out*****************
// Send data to Max5353 12-bit DAC
// inputs:  voltage output (0 to 4095)
// outputs: none
void DAC_Out(uint16_t code){   
  while((SSI0_SR_R&0x00000002)==0){};// SSI Transmit FIFO Not Full
  SSI0_DR_R = code; }                // data out, no reply
  
//********DAC_Out*****************
// Send data to Max5353 12-bit DAC
// inputs:  voltage output (0 to 4095)
// outputs: reply is returned
// send the 16-bit code to the SSI, return a reply
uint16_t DAC_Out2(uint16_t code){   uint16_t receive;
  while((SSI0_SR_R&0x00000002)==0){};// SSI Transmit FIFO Not Full
  SSI0_DR_R = code;                  // data out
  while((SSI0_SR_R&0x00000004)==0){};// SSI Receive FIFO Not Empty
  receive = SSI0_DR_R;               // acknowledge response
  return receive;
}
~~~

![](http://i.markdownnotes.com/blob_WHbUuFF_1_xbZo9Fs.jpg)

Freescale SPI timing.
SPI transmits data at the same time it receives input.
SSIOTx = output
SSIORx = input
Shifts on 1st, 3rd 5th 7th clockedge (Rising clock edge) 

![](http://i.markdownnotes.com/blob_xoIKCli_4QOYdUH.jpg)
Polairty is shown above, this is what happens when we modify the SPO bit
![](http://i.markdownnotes.com/blob_9fmCvPT.jpg)
General SSI Bits

![](http://i.markdownnotes.com/blob_c2khmhD.jpg)
Use PD0 for SSI 

##5.3 Hardware
![](http://i.markdownnotes.com/Capture1.PNG)

###5.3a Shunt Diode
**LM4041C - Shunt Diode** - used to create a stable 1.50V reference
***Specificiations**
~~~
Iz = 80uA 
IL = 1.5V/Rin (Rin is the input impedance about 100pF)
VREF(1.233V)
Select R1 and R2, RS
Vz = Vref * ( 1 + R2/R1) = 1.5V
Calculate:
R2 ~ 25K and R1 ~100K
	
	Rs Resistor sets available current for shunt diode
	Rs  ≤ (3.3-VZ)/(IL+IZ).
	Rs ~ 22kohm


	~~~
	
	
###5.3b Speaker Interface
Parts: current-amplifying audio amplifier
**Resistors** :choosing RF and RI so the gain is one or less than one (gain = 2*RF/RI)
**Capacitors** : Range of 0.1 to 0.47 uF 
**CB** range of 1 to 4.7 uF 
**Cs** range of 0.1 to 0.47 uF
![](http://i.markdownnotes.com/Capture2_hdlo0XQ.PNG)

###5.3c DAC Paramters 
Order ourselves TLV5618 if we want awesome sound (2 Analog outputs and 2 speakers for stereo sound) 
