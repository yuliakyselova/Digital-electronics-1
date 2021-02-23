# Lab 02-logic

## 1. Truth table

| **Dec. equivalent** | **B[1:0]** | **A[1:0]** | **B is greater than A** | **B equals A** | **B is less than A** |
| :-: | :-: | :-: | :-: | :-: | :-: |
| 0 | 0 0 | 0 0 | 0 | 1 | 0 |
| 1 | 0 0 | 0 1 | 0 | 0 | 1 |
| 2 | 0 0 | 1 0 | 0 | 0 | 1 |
| 3 | 0 0 | 1 1 | 0 | 0 | 1 |
| 4 | 0 1 | 0 0 | 1 | 0 | 0 |
| 5 | 0 1 | 0 1 | 0 | 1 | 0 |
| 6 | 0 1 | 1 0 | 0 | 0 | 0 |
| 7 | 0 1 | 1 1 | 0 | 1 | 0 |
| 8 | 1 0 | 0 0 | 1 | 0 | 1 |
| 9 | 1 0 | 0 1 | 1 | 0 | 1 |
| 10 | 1 0 | 1 0 | 0 | 1 | 0 |
| 11 | 1 0 | 1 1 | 0 | 0 | 0 |
| 12 | 1 1 | 0 0 | 1 | 0 | 1 |
| 13 | 1 1 | 0 1 | 1 | 0 | 1 |
| 14 | 1 1 | 1 0 | 1 | 0 | 0 |
| 15 | 1 1 | 1 1 | 0 | 1 | 0 |

## 2. 2-bit comparator.

### Karnaugh maps

B<A

![](Images/BlessA.jpg)

B=A

![](Images/BeqA.jpg)

B>A
![](Images/BmoreA.jpg)

### Equations of simplified SoP form of the "greater than" function and simplified PoS form of the "less than" function.
![](Images/BlABmA.jpg)

### EDA Playgroud 
https://www.edaplayground.com/x/nNsR

# 3. 4-bit binary comparator.

## Listing of VHDL architecture from design file.



### Vypis logu:
[2021-02-23 15:41:18 EST] ghdl -i design.vhd testbench.vhd  && ghdl -m  tb_comparator_2bit && ghdl -r  tb_comparator_2bit   --vcd=dump.vcd && sed -i 's/^U/X/g; s/^-/X/g; s/^H/1/g; s/^L/0/g' dump.vcd 
analyze design.vhd
analyze testbench.vhd
elaborate tb_comparator_2bit
testbench.vhd:51:9:@0ms:(report note): Stimulus process started
testbench.vhd:54:7:@0ms:(report note): Stimulus process started
testbench.vhd:122:9:@1700ns:(assertion error): Test failed for input combination: 1111, 1111
testbench.vhd:125:9:@1700ns:(report note): Stimulus process finished
Finding VCD file...
./dump.vcd
[2021-02-23 15:41:19 EST] Opening EPWave...
Done



