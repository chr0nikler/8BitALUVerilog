
# EE115c Project README

#### Arjun Lakshmipathy
#### Joraaver Chahal

Please read for testing purposes. This is very important

##Layout

The layout is as follows, from top-bottom

1. main.v OR demonstrate.v (explained later)
2. control.v
3. datapath.v
4. ALU.v
5. bitslice.v
6. adder.v/subtractor.v

**Both main.v and demonstrate.v have testbenches, tbMain.v and tbDemonstrate.v** 

For testing purposes, *main.v* can be used. It is left empty, and it's testbench merely shows how to multiply two numbers together.

**HOWEVER**

demonstrate.v has a nicer way of demonstrating the correctness of the routines. It has built in tasks for add, multiply, right shift, and two's complement. Those demonstrate basically all the required procedures for this protocol. *All the testbench does is provide you a place to input an X and Y*. For any new X and Y, demonstrate.v reruns all these tasks and outputs the monitor for them as well as the final values in a very human readable form. 

**We highly suggest you use demonstrate.v and tbDemonstrate.v first to get a feel for how easy it should be to test that our main functions are working**. 
After seeing it run a couple of times, and after having read the section belowon how to use our opcodes, only then should you add stuff to tbMain.v for testing the main fucntion

  
     OPCODE BREAKDOWN
  
     Passed to the control block is a 12 bit opcode. There are 8 prefix bits and 4 ALU bits.
     The meaning of each are as follows
  
     M1  M2  M3  M4  M5  M5  RegOut RegOut ALU ALU ALU ALU
  
     M1 through M5 are the selector bits for each of the 5 MUX's (M5 has two bits for 3 inputs)
  
     RegOut indicates which register (X,Y,Z which correspond to Aout,Bout,Cout in the diagram provided with the spec) to write to (2 bits are needed for 3 outputs)
  
     ALU indicates the 4 bit opcode for the ALU
  
     If the last four bits are an actual operation in the ALU, then the following decoding is used:
  
     1) Observe M3 to choose which register, A or B, to read from.
     2) Observe M4 to choose to either read from reg C or from output registers.
     3) If M4 is reading from feedback, observe M5 to see which output register to read from.
     4) Compute ALU function using ALU opcode, and observe RegOut to see which register to store the data in.
  
     If the last four bits are a datapth operation (Load into A/B/C) then the following decoding is used:
  
     1) Observe opcode. If it is Load A/B, observe M1 to either read X or read from feedback.
     2) If M1 indicates to observe from feedback, observe M5 to see which output register to read from.
     3) If opcode indicates to Load C, observe M2 to either read Y or read from feedback.
     4) If M2 indicates to observe from feedback, observe M5 to see which output register to read from.

     For example, if we wanted to first store something from Z into B, the following opcode would handle it (spaced out for convenience):

    1  0  0  0  1  0  0      0      1   0   1   1
    M1 M2 M3 M4 M5 M5 RegOut RegOut ALU ALU ALU ALU

    This means that mux-1 choses to recieve input from feedback. Mux 2-4 we don't care about since the operation doesn't involve them. Mux-5 with 10 means to read from Z. RegOut is 0 0 because we are not writing to any output register. Finally, ALU code 1011 means load into B.

    Then, if we want to calculate the bitwise and B with what is currently in Y and store it in Z, the following opcode would handle it:

    0  0  1  1  0  1  1      0      0   1   1   0
    M1 M2 M3 M4 M5 M5 RegOut RegOut ALU ALU ALU ALU

    Mux-1,2 are don't cares. Mux-3 being 1 means to read from register B, and Mux-4 being 1 means to read from feeback (from output registers). Mux-5 being 01 means to read from output register Y, and RegOut of 10 means to write to register Z. ALU opcode 0110 indicates to perform the bitwise & of the two operators. 

    That's all there is to it. This same documentation (minus the examples) are in control.v

    
    
     
  
  