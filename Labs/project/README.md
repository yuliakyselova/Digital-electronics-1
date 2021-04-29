# Project - Exercise bike

### Team members

| **Jméno** | **GitHub** |
| :-: | :-: |
| Krýcha Jakub | https://github.com/xkrych01/Digital-electronics-1 |
| Kyselova Yuliia | https://github.com/yuliakyselova/Digital-electronics-1 |
| Lazarević Veljko| https://github.com/mvvelja/Digital-electronics-1 |
| Levák Adam     | https://github.com/AdamLevak/Digital-electronics-1 |
| Lovas Václav  |  https://github.com/xlovas00/Digital-electronics-1|

[Link to GitHub project folder]( http://github.com/xxx)

### Project objectives

Creating a console for exercise bike with hall sensor, measuring and displaying speed, distance traveled...


## Hardware description

Board used Arty A7-35T...


## VHDL modules description and simulations

### 1. Timer
```vhdl
library ieee;
use ieee.std_logic_1164.all;
use ieee.numeric_std.all;
 
entity timer is
generic(ClockFrequencyHz : integer:= 0);
port(
    clk     : in std_logic;
    rst     : in std_logic;
    Seconds : inout integer;
    Minutes : inout integer;
    Hours   : inout integer);
end entity;
 
architecture rtl of timer is
 
begin
 
    process(clk) is
    begin
        if rising_edge(clk) then
 
            -- If the negative reset signal is active
            if rst = '0' then
                Seconds <= 0;
                Minutes <= 0;
                Hours   <= 0;
            else
 
                    -- True once every minute
                    if Seconds = 59 then
                        Seconds <= 0;
 
                        -- True once every hour
                        if Minutes = 59 then
                            Minutes <= 0;
 
                        
                            if Hours = 23 then
                                Hours <= 0;
                            else
                                Hours <= Hours + 1;
                            end if;
 
                        else
                            Minutes <= Minutes + 1;
                        end if;
 
                    else
                        Seconds <= Seconds + 1;
          
                end if;
 
            end if;
        end if;
    end process;
 
end architecture;
```

### Simulation waveforms - simulating Timer module
![project](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/project/Images/timer.png)

### 2. Měření vzdálenosti

`counter_distance`
```vhdl
library ieee;
use ieee.std_logic_1164.all;
use ieee.numeric_std.all;

entity counter_distance is
    generic(
        g_CNT_WIDTH : natural := 4      -- Number of bits for counter
    );
    port(
        clk      : in  std_logic;       -- Clock from Hall probe
        reset    : in  std_logic;       -- Synchronous reset
        cnt_o_A    : out std_logic_vector(g_CNT_WIDTH - 1 downto 0);    -- Output data display A
        cnt_o_B    : out std_logic_vector(g_CNT_WIDTH - 1 downto 0);    -- Output data display B
        cnt_o_C    : out std_logic_vector(g_CNT_WIDTH - 1 downto 0);    -- Output data display C
        cnt_o_D    : out std_logic_vector(g_CNT_WIDTH - 1 downto 0)     -- Output data display D
    );
end entity counter_distance;

architecture behavioral of counter_distance is

    -- Siganls for local counter
    signal s_cnt_local_A : unsigned(g_CNT_WIDTH - 1 downto 0);
    signal s_cnt_local_B : unsigned(g_CNT_WIDTH - 1 downto 0);
    signal s_cnt_local_C : unsigned(g_CNT_WIDTH - 1 downto 0);
    signal s_cnt_local_D : unsigned(g_CNT_WIDTH - 1 downto 0);

begin

    p_counter_distance : process(clk)
    begin
        if rising_edge(clk) then
        
            if (reset = '1') then               -- Synchronous reset
                -- Clear all bits
                s_cnt_local_A <= (others => '0');
                s_cnt_local_B <= (others => '0');
                s_cnt_local_C <= (others => '0');
                s_cnt_local_D <= (others => '0');

            else
                if ( s_cnt_local_A = "1000" and s_cnt_local_B = "1001" and s_cnt_local_C = "1001" and s_cnt_local_D = "1001") then
                    --Clear all bits
                    s_cnt_local_A <= (others => '0');
                    s_cnt_local_B <= (others => '0');
                    s_cnt_local_C <= (others => '0');
                    s_cnt_local_D <= (others => '0');
                else
                    if (s_cnt_local_A = "1000") then                        -- display A = 9 -> reset display A
                        s_cnt_local_A <= "0000";
                        if (s_cnt_local_B = "1001") then                    -- display B = 9 -> reset display B
                            s_cnt_local_B <= "0000";
                            if (s_cnt_local_C = "1001") then                -- display B = 9 -> reset display C
                                s_cnt_local_C <= "0000";
                                if (s_cnt_local_D = "1001") then            -- display B = 9 -> reset display D
                                    s_cnt_local_D <= "0000";
                                else
                                    s_cnt_local_D <= s_cnt_local_D + 1;     -- data(display D) + 1
                                end if;
                            else
                                s_cnt_local_C <= s_cnt_local_C + 1;         -- data(display C) + 1
                            end if;
                        else
                            s_cnt_local_B <= s_cnt_local_B + 1;             -- data(display B) + 1
                        end if;
                    else
                        s_cnt_local_A <= s_cnt_local_A + 2;                 -- data(display A) + 1
                    end if;
                end if;
            end if;
        end if;
    end process p_counter_distance;

    -- Output must be retyped from "unsigned" to "std_logic_vector"
    cnt_o_A <= std_logic_vector(s_cnt_local_A);
    cnt_o_B <= std_logic_vector(s_cnt_local_B);
    cnt_o_C <= std_logic_vector(s_cnt_local_C);
    cnt_o_D <= std_logic_vector(s_cnt_local_D);

end architecture behavioral;
```
### Simulation waveforms - simulating counter_distance module
![project](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/project/Images/counter_distance.png)

### 3. Hallova sonda

### 4. Frekvence šlapání

### 5. Rychlost






## TOP module description and simulations

Write your text here.


## Video

*Write your text here*


## References

   1. Write your text here.
