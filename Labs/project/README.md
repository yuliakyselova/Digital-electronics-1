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

Creating a console for exercise bike with hall sensor, measuring and displaying speed, distance traveled.


## Hardware description

Board used Arty A7-35T.


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
```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use ieee.numeric_std.all;


entity tread_sensor is
       
    Port ( 
        clk     : in std_logic;
        rst     : in std_logic;
        btn_o   : in std_logic;                     -- input treading
        trd_o   : out std_logic;                    -- output treading
        trd_led : out std_logic_vector(3-1 downto 0)-- output for led
    );
end tread_sensor;

architecture Behavioral of tread_sensor is
    -- type of states treading
    type t_state is (OFF,
                     BAD,
                     NORMAL,
                     GOOD,
                     PERFECT
                     );
    
    signal s_state  : t_state;
    signal clicks   : integer;
    signal s_cnt    : unsigned(5-1 downto 0);
    
    
    -- Specific values for local counter
    constant DELAY_4SEC : unsigned(5-1 downto 0) := b"1_0000";
    constant ZERO : unsigned(5-1 downto 0) := b"0_0000";
begin
   
    p_output_led : process(s_state)
    begin       -- represenation of states to RGB-LED
        case s_state is
            when OFF =>
                trd_led <= "000";
            
            when BAD =>
                trd_led <= "100";
            
            when NORMAL =>
                trd_led <= "110";
            
            when GOOD =>
                trd_led <= "010";
            
            when PERFECT =>
                trd_led <= "001";
            
            when others =>
                trd_led <= "000";
        end case;       
    end process p_output_led;
    
    p_tread_sensor : process(clk, btn_o)
    begin
        if rising_edge(clk) then    
            
            if (rst = '1') then -- synchronous reset
                clicks <= 0;
                s_state <= OFF;
                trd_o <= '0';
            else            -- calculation clicks at 4 seconds
                if (s_cnt < DELAY_4SEC) then
                    s_cnt <= s_cnt + 1;
                    
                    if rising_edge(btn_o) then  -- waiting for input rising edge
                        clicks <= clicks + 1;   -- click +1
                        trd_o <= '1';           -- set at output 1
                    else
                        clicks <= clicks;       -- clicks is same
                        trd_o <= '0';           -- set at output 0
                        
                    end if;    
                else                            -- dysplaing the intensity of treading
                    if (clicks <= 5) then
                        s_state <= BAD;
                
                    elsif (clicks <= 10) then 
                        s_state <= NORMAL;
                
                    elsif (clicks <= 15) then
                        s_state <= GOOD;
                    
                    else 
                        s_state <= PERFECT;
                    end if;
                    
                    clicks <= 0;                -- set clicks and local counter to 0
                    s_cnt <= ZERO;
                end if;
            end if;
        end if;
    end process p_tread_sensor;

end Behavioral;

```

### 5. Rychlost

```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use ieee.numeric_std.all;
use IEEE.STD_LOGIC_UNSIGNED.ALL;
-- Uncomment the following library declaration if using
-- arithmetic functions with Signed or Unsigned values
--use IEEE.NUMERIC_STD.ALL;

-- Uncomment the following library declaration if instantiating
-- any Xilinx leaf cells in this code.
--library UNISIM;
--use UNISIM.VComponents.all;

entity Velocity_counter is
    generic(
        g_CNT_WIDTH : natural := 4      -- Number of bits for counter         
           );
    Port ( 
            gen_o      : in std_logic;
            clk        : in std_logic;
            reset      : in  std_logic;       -- Synchronous reset                       
            cnt_o_A    : out std_logic_vector(g_CNT_WIDTH - 1 downto 0);
            cnt_o_B    : out std_logic_vector(g_CNT_WIDTH - 1 downto 0);
            cnt_o_C    : out std_logic_vector(g_CNT_WIDTH - 1 downto 0);
            cnt_o_D    : out std_logic_vector(g_CNT_WIDTH - 1 downto 0);
           -- ticks      : out std_logic_vector(g_CNT_WIDTH - 1 downto 0)
            ticks      : out real  
            );
end Velocity_counter;

architecture Behavioral of Velocity_counter is  
 -- Local counter    
    signal s_cnt_local_A : unsigned(g_CNT_WIDTH - 1 downto 0);
    signal s_cnt_local_B : unsigned(g_CNT_WIDTH - 1 downto 0);
    signal s_cnt_local_C : unsigned(g_CNT_WIDTH - 1 downto 0);
    signal s_cnt_local_D : unsigned(g_CNT_WIDTH - 1 downto 0);
   -- signal s_ticks       : unsigned(g_CNT_WIDTH - 1 downto 0);
   -- signal counter       : unsigned(g_CNT_WIDTH - 1 downto 0);
    signal s_ticks       : real;
    signal counter       : real;
    constant kmh         : real:= 3.6;
begin
p_vel : process(clk,gen_o)
    begin
        if reset = '1' then
               s_ticks <= 0.0;
               counter <= 0.0;
        elsif rising_edge(gen_o) then                                     
               counter <= counter + 2.0;   -- counting up
        elsif rising_edge(clk) then           
               s_ticks <= counter * kmh ; 
               counter <= 0.0; 
                                                                               
        end if;        
    end process p_vel;

ticks <= s_ticks;
-- s_ticks <= s_ticks * (18/5); -- prevod na km/h   
end Behavioral;
```





## TOP module description and simulations

Write your text here.


## Video

*Write your text here*


## References

   1. Write your text here.
