# Lab 03-vivado

## GitHub repository

https://github.com/yuliakyselova/Digital-electronics-1

## 1. Table with connection of 16 slide switches and 16 LEDs on Nexys board.

| **SW** | **SW-PIN** | **LED** | **LED-PIN** |
| :-: | :-: | :-: | :-: |
| **SW0** | J15 | LD0 | H17 |
| **SW1** | L16 | LD1 | K15 |
| **SW2** | M13 | LD2 | J13|
| **SW3** | R15 | LD3 | N18 |
| **SW4** | R17 | LD4 | R18 |
| **SW5** | T18 | LD5 | V17 |
| **SW6** | U18 | LD6 | U17 |
| **SW7** | R13 | LD7 | U16 |
| **SW8** | T8 | LD8 | V16 |
| **SW9** | U8 | LD9 | T15 |
| **SW10** | R16 |LD10 | U14 |
| **SW11** | T13 | LD11 | T16 |
| **SW12** | H6  | LD12 | V15 |
| **SW13** | U12 | LD13 | V14 |
| **SW14** | U11 | LD14 | V12 |
| **SW15** | V10 | LD15 | V11 |

## 2. Two-bit wide 4-to-1 multiplexer.

### VHDL architecture from source file mux_2bit_4to1.vhd:

```vhdl
library ieee;
use ieee.std_logic_1164.all;

entity mux_2bit_4to1 is
    port(
    
        a_i           : in  std_logic_vector(2 - 1 downto 0);
		b_i           : in  std_logic_vector(2 - 1 downto 0);
		c_i           : in  std_logic_vector(2 - 1 downto 0);
		d_i           : in  std_logic_vector(2 - 1 downto 0);
		sel_i         : in  std_logic_vector(2 - 1 downto 0);
		
		
		f_o           : out std_logic_vector(2 - 1 downto 0)       
    );
end entity mux_2bit_4to1;

architecture Behavioral of mux_2bit_4to1 is
begin

       f_o <= a_i when(sel_i = "00") else
              b_i when(sel_i = "01") else
              c_i when(sel_i = "10") else
              d_i;
end architecture Behavioral;
```

### VHDL stimulus process from testbench file tb_mux_2bit_4to1.vhd:

```vhdl
library ieee;
use ieee.std_logic_1164.all;

entity tb_mux_2bit_4to1 is
    
end entity tb_mux_2bit_4to1;


architecture testbench of tb_mux_2bit_4to1 is

    signal s_a       : std_logic_vector(2 - 1 downto 0);
    signal s_b       : std_logic_vector(2 - 1 downto 0);
    signal s_c       : std_logic_vector(2 - 1 downto 0);
    signal s_d       : std_logic_vector(2 - 1 downto 0);
    signal s_sel     : std_logic_vector(2 - 1 downto 0);
    signal s_f       : std_logic_vector(2 - 1 downto 0);

begin
    
    uut_mux_2bit_4to1 : entity work.mux_2bit_4to1
        port map(
        
            a_i           => s_a,
            b_i           => s_b,
            c_i           => s_c,
            d_i           => s_d,
            sel_i         => s_sel,
            f_o           => s_f
        );

    
    p_stimulus : process
    begin
      
        report "Stimulus process started" severity note;

        s_d <= "00"; s_c <= "00"; s_b <= "00"; s_a <= "00"; 
        s_sel <= "00"; wait for 100 ns;
        
        s_d <= "10"; s_c <= "01"; s_b <= "01"; s_a <= "00"; 
        s_sel <= "00"; wait for 100 ns;
        
        s_d <= "10"; s_c <= "01"; s_b <= "01"; s_a <= "11"; 
        s_sel <= "00"; wait for 100 ns;
        
        s_d <= "10"; s_c <= "01"; s_b <= "01"; s_a <= "00"; 
        s_sel <= "01"; wait for 100 ns;
        
        s_d <= "10"; s_c <= "01"; s_b <= "11"; s_a <= "00"; 
        s_sel <= "01"; wait for 100 ns;
        
        s_d <= "10"; s_c <= "01"; s_b <= "11"; s_a <= "00"; 
        s_sel <= "10"; wait for 100 ns;
        
        s_d <= "10"; s_c <= "01"; s_b <= "11"; s_a <= "00"; 
        s_sel <= "11"; wait for 100 ns;
        
        report "Stimulus process finished" severity note;
        wait;
    end process p_stimulus;

end architecture testbench;
```

### Simulated time waveforms:


![03-vivado](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/03-vivado/Images/Simulation.png)

## 3. Vivado tutorial.

### Step 1. Create project:
![03-vivado](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/03-vivado/Images/step1.png)
![03-vivado](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/03-vivado/Images/krok2.png)

### Type the name of the project and choose the directory.
![03-vivado](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/03-vivado/Images/krok3.png)

### Choose project type.
![03-vivado](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/03-vivado/Images/krok4.png)

### Add/create file sources and specify target and simulation language.
![03-vivado](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/03-vivado/Images/krok5.png)

![03-vivado](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/03-vivado/Images/krok6.png)

![03-vivado](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/03-vivado/Images/krok7.png)

### Here we can choose parts or complete board.
![03-vivado](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/03-vivado/Images/krok8.png)

![03-vivado](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/03-vivado/Images/krok9.png)

### Step 2
### Here we can write  Design/Testbench VHDL code: 
![03-vivado](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/03-vivado/Images/krok10.png)

### When the code is done in tutorial  -> create testbench by pressing ALT + A.
![03-vivado](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/03-vivado/Images/krok11.png)

![03-vivado](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/03-vivado/Images/krok12.png)

### Step 3. Run Simulation
### Flow -> Run Simulation -> Run Behavioral Simulation.
![03-vivado](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/03-vivado/Images/krok13.png)

![03-vivado](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/03-vivado/Images/Simulation.png)
