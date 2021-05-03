# Project - Exercise bike

### Team members

- **Krýcha Jakub**
- **Kyselova Yuliia**
- **Lazarević Veljko**
- **Levák Adam**
- **Lovas Václav**

[Link to GitHub project folder]( http://github.com/)

### Project objectives

Creating a simulation of console for exercise bike, using hall sensor on the wheel, which will do measuring and displaying speed, distance traveled.


## Hardware description

- Arty A7-35T board
- 4x 7-segment display
- 1x button
- 3x switch
- 2x RGB LED


## VHDL modules description and simulations

### 1. Timer
VHDL code of timer module - `timer.vhd`.
```vhdl
library ieee;
use ieee.std_logic_1164.all;
use ieee.numeric_std.all;
 
entity timer is
generic(ClockFrequencyHz : integer:= 0);
port(
    clk           : in  std_logic;
    rst           : in  std_logic;
    Seconds_units : out std_logic_vector(3 downto 0);
    Seconds_tens  : out std_logic_vector(3 downto 0);
    Minutes_units : out std_logic_vector(3 downto 0);
    Minutes_tens  : out std_logic_vector(3 downto 0));
end entity;
 
architecture rtl of timer is
    signal Seconds : unsigned(5 downto 0); 
    signal Minutes : unsigned(5 downto 0); 
begin
 
    process(clk) is
    begin
        if rising_edge(clk) then
 
            -- If the negative reset signal is active
            if rst = '0' then
                Seconds <= (others => '0');
                Minutes <= (others => '0');
            else
 
                    -- True once every minute
                    if Seconds = 59 then
                        Seconds <= (others => '0');
 
                        -- True once every hour
                        if Minutes = 59 then
                            Minutes <= (others => '0');
                        else
                            Minutes <= Minutes + 1;
                        end if;
 
                    else
                        Seconds <= Seconds + 1;
          
                end if;
 
            end if;
        end if;
    end process;
 
    Seconds_units <= std_logic_vector(to_unsigned(to_integer(Seconds mod 10), 4)); 
    Seconds_tens  <= std_logic_vector(to_unsigned(to_integer(Seconds/10),4)); 
    Minutes_units <= std_logic_vector(to_unsigned(to_integer(Minutes mod 10),4));  
    Minutes_tens  <= std_logic_vector(to_unsigned(to_integer(Minutes/10),4)); 
 
end architecture;
```

### Simulation waveforms - simulating Timer module
![project](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/project/Images/timer.png)

### 2. Distance traveled.
VHDL code of distance traveled  module - `counter_distance.vhd`.
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

### 3. Hall sensor.
VHDL code of hall sensor module - `hall_sonda.vhd`.
```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

entity hall_sonda is
    generic(
        g_MAX : natural := 4    
    );
    
    Port ( 
        clk   : in std_logic;
        rst   : in std_logic;
        trdhs : in std_logic;       -- input from tread sensor
        drl_o : in std_logic;       -- hardest or easiest 
        gen_o : out std_logic       -- generate pulse
    );
end hall_sonda;

architecture Behavioral of hall_sonda is
    signal s_trd : std_logic;           -- connecting signal
    signal s_btn : std_logic;           -- for fixing error
    signal s_cnt_local : natural;       -- local counter

begin
    tread_s : entity work.tread_sensor
        port map(           
            clk => clk,
            rst => rst,
            btn_o => s_btn,
            trd_o => s_trd
            
        ); 
     
    p_hall_sonda : process(s_trd,clk)
    begin
        s_trd <= trdhs;                  -- Set output from tread_sensor to input software hall_sond
              
        if rising_edge(trdhs) then       -- set 
            if (rst = '1') then          -- High active reset
                s_cnt_local  <= 0;       -- Clear local counter
                gen_o        <= '0';
            -- easy level for treading
            elsif (drl_o = '0') then
                if (s_cnt_local >= (g_MAX - 1)) then
                    
                    s_cnt_local  <= 0;       -- Clear local counter
                    gen_o        <= '1';     -- Generate pulse

                else
                    s_cnt_local <= s_cnt_local + 1; -- counter +1
                    gen_o        <= '0';            -- generate 0
                end if;
            -- hard level for treading   
            elsif (drl_o = '1') then
                if (s_cnt_local >= ((g_MAX - 1)/2)) then
                    
                    s_cnt_local  <= 0;       -- Clear local counter
                    gen_o        <= '1';     -- Generate pulse

                else
                    s_cnt_local <= s_cnt_local + 1; -- counter +1
                    gen_o        <= '0';            -- generate 0
                end if;
            end if;
         end if;
    end process p_hall_sonda;
    
end Behavioral;
```
### Simulation waveforms - simulating hall sensor module
![project](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/project/Images/hall_sonda.png)

### 4. Tread sensor.
VHDL code of tread sensor module - `tread_sensor.vhd`.
```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use ieee.numeric_std.all;


entity tread_sensor is
       
    Port ( 
        clk     : in std_logic;
        rst     : in std_logic;
        btn_o   : in std_logic;                     -- input treading
        trd_o   : out std_logic;                    -- output for hall_sond
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
                    
                    if (btn_o = '1') then  -- waiting for input rising edge
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
### Simulation waveforms - simulating tread sensor module
![project](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/project/Images/tread_sensor_tb.jpeg)


### 5. Speed. 

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

VHDL code of top module - `top.vhd`.
```vhdl
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

entity top is
    Port ( 
        CLK100MHZ : in  STD_LOGIC;
        BTN       : in  STD_LOGIC_vector(2-1 downto 0);
        SW        : in  std_logic_vector(3-1 downto 0);
        JA        : out std_logic_vector(7-1 downto 0);
        JB        : out std_logic_vector(7-1 downto 0);
        JC        : out std_logic_vector(7-1 downto 0);
        JD        : out std_logic_vector(7-1 downto 0);
        LED0      : out std_logic_vector(3-1 downto 0)

    );
end top;

architecture Behavioral of top is
    signal s_hall  : std_logic;
    signal s_trdhs : std_logic;
    signal s_drl   : std_logic;
    
    signal s_timer0    : std_logic_vector(4 - 1 downto 0);
    signal s_timer1    : std_logic_vector(4 - 1 downto 0);
    signal s_timer2    : std_logic_vector(4 - 1 downto 0);
    signal s_timer3    : std_logic_vector(4 - 1 downto 0);
                                                          
    signal s_speed0    : std_logic_vector(4 - 1 downto 0);
    signal s_speed1    : std_logic_vector(4 - 1 downto 0);
    signal s_speed2    : std_logic_vector(4 - 1 downto 0);
    signal s_speed3    : std_logic_vector(4 - 1 downto 0);
                                                          
    signal s_distance0 : std_logic_vector(4 - 1 downto 0);
    signal s_distance1 : std_logic_vector(4 - 1 downto 0);
    signal s_distance2 : std_logic_vector(4 - 1 downto 0);
    signal s_distance3 : std_logic_vector(4 - 1 downto 0);
begin
     
    trd_sensor : entity work.tread_sensor
    port map (
        clk     => CLK100MHZ,
        rst     => BTN(1),
        btn_o   => BTN(0),
        trd_led => LED0
    
    );
    
    hall_sensor : entity work.hall_sonda
    port map (
        clk   => CLK100MHZ,
        rst   => BTN(1),
        trdhs => s_trdhs,
        drl_o => SW(0)
        
    
    );
    
    timer : entity work.timer
    port map (
        clk            => CLK100MHZ, 
        rst            => BTN(1),    
        Seconds_units  => s_timer3,
        Seconds_tens   => s_timer2,
        Minutes_units  => s_timer1,
        Minutes_tens   => s_timer0
         
    );
    
    counter : entity work.counter_distance         
    port map(                     
        hall    => s_hall,     
        reset   => BTN(1),   
        cnt_o_A => s_distance3,
        cnt_o_B => s_distance2,
        cnt_o_C => s_distance1,
        cnt_o_D => s_distance0 
    );
    
    speed : entity work.Velocity_counter         
    port map(                     
        gen     => s_hall,
        clk     => CLK100MHZ,        
        reset   => BTN(1),      
        cnt_o_A => s_speed3,
        cnt_o_B => s_speed2  
    );                            
    
    driver : entity work.driver_7seg_4digits
    port map(
        clk              => CLK100MHZ,
        reset            => BTN(1),   
        sw_timer         => SW(1),
        sw_distance      => SW(2),
        
        data_timer3_i    => s_timer3,
        data_timer2_i    => s_timer2,
        data_timer1_i    => s_timer1,
        data_timer0_i    => s_timer0, 
                         
        data_speed3_i    => s_speed3,
        data_speed2_i    => s_speed2, 
        data_speed1_i    => s_speed1,
        data_speed0_i    => s_speed0,
                         
        data_distance3_i => s_distance3,
        data_distance2_i => s_distance2,
        data_distance1_i => s_distance1,
        data_distance0_i => s_distance0, 
                         
        seg_ja(0)           => JA(0),
        seg_ja(1)           => JA(1),
        seg_ja(2)           => JA(2),
        seg_ja(3)           => JA(3),
        seg_ja(4)           => JA(4),
        seg_ja(5)           => JA(5),
        seg_ja(6)           => JA(6),
                         
        seg_jb(0)           => JB(0),
        seg_jb(1)           => JB(1),
        seg_jb(2)           => JB(2),
        seg_jb(3)           => JB(3),
        seg_jb(4)           => JB(4),
        seg_jb(5)           => JB(5),
        seg_jb(6)           => JB(6),
                                    
        seg_jc(0)           => JC(0),
        seg_jc(1)           => JC(1),
        seg_jc(2)           => JC(2),
        seg_jc(3)           => JC(3),
        seg_jc(4)           => JC(4),
        seg_jc(5)           => JC(5),
        seg_jc(6)           => JC(6),
                                    
        seg_jd(0)           => JD(0),
        seg_jd(1)           => JD(1),
        seg_jd(2)           => JD(2),
        seg_jd(3)           => JD(3),
        seg_jd(4)           => JD(4),
        seg_jd(5)           => JD(5),
        seg_jd(6)           => JD(6)
                          
    );
   
    
end Behavioral;
```

## Video




## References

   1. https://reference.digilentinc.com/reference/programmable-logic/arty-a7/reference-manual
