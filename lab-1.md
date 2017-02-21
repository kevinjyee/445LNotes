

##1. Lab 1
####Motivation
Fixed-point numbers (decimal/binary, signed/unsigned, 8/16/32 bit)
1) Convert value to integer. E.g., What integer is stored in the computer, when the value 2.1 is stored in 16-bit
unsigned binary fixed-point, with a resolution of 2**-10? Answer: I = 2.1*1024, which is about 2150. I will make
the math so easy a calculator will not be needed.
2) Convert integer to value. E.g., What is the value of an 8-bit signed decimal fixed-point number (resolution is 0.1)
if the integer stored in memory is –123? Answer: –123*0.1 equals –12.3.
3) Basic concepts of range, resolution and rounding.
4) Given the range and resolution, choose the format
Programming (C on the Arm Cortex M) Techniques
1) How to check for overflow when performing integer calculations in C. Answer: promote to higher precision and
check intermediate results.
2) Stack picture: function call, function parameters, locals, interrupt service stack frame
3) Memory allocation: global, local, constants, and reset/interrupt vectors
4) Modularity linking call graph to #include ′file.h′, private versus public
5) const volatile static
6) Data flow graph
7) Debugging: dumps, monitors, scans, breaks, filter, profiling
8) Putting prototypes to public functions in the ′file.h′, implementations in the ′file.c′

Fixed-point numbers (decimal/binary, signed/unsigned,8/16/32 bit)

###1.1 Converting value to integer

**Q:** What integer is stored in the computer, when the value 2.1 is stored in 16-bit
unsigned binary fixed-point, with a resolution of $$2^{10}$$

**A:** I = 2.1*1024, which is about 2150.

* **Fixed-point number** 
	* Variable Integer: Integer may be unsigned or signed (dimensionless) 
	* Precision: Total Number of distinguishable values that can be represented. (Determined by number of bits used to store the variable integer) 
	* Fixed Constant: $$ {\Delta}$$

Fixed point Number defined as $$$ Fixed  point Number = I * {\Delta}$$$
$$$ Decimal Fixed point Number = I * 10^m $$$
$$$ Binary Fixed point Number = I * 2^n $$$

* **Resolution** : smallest difference in value that can be represented.
	* Resolution is equal to the fixed constant.
	* Express the resolution of the numbers as its units. i.e. Resolution of 0.001 volts same as integer with units of mV.

###1.2 Converting integer to value 


**Q:** What is the value of an 8-bit signed decimal fixed-point number (resolution is 0.1)
if the integer stored in memory is –123?

**A:** 123*0.1 equals –12.3.

###1.3 Floating Point Number vs Fixed Point Number 
**Floating Point**: Does not reserve a specific number of bits for the integer part of the fractional part
**Fixed Point Number**: Reserved specific number of bits no matter what

###1.4 Common Errors
**Overflow**: Exceeds maximum number of bits
**Dropout**: Happens during a divide or right shift (divide last to prevent drop out) 

