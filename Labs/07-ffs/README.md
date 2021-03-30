# Lab 07 - ffs

### GitHub repository.
https://github.com/yuliakyselova/Digital-electronics-1

## 1.Preparation tasks.

   |clk| **d** | **q(n)** | **q(n+1)** | **Comments** |
   |:-:| :-: | :-: | :-: | :-- |
   || 0 | 0 | 0 | No change |
   || 0 | 1 | 0 | Change |
   || 1 | 1 | 1 | No change |
   || 1 | 0 | 1 |  Change |

   |clk| **j** | **k** | **q(n)** | **q(n+1)** | **Comments** |
   |:-:| :-: | :-: | :-: | :-: | :-: |
   || 0 | 0 | 0 | 0 | No change |
   || 0 | 0 | 1 | 1 | No change |
   || 0 | 1 | 0 | 0 | Reset |
   || 0 | 1| 1 | 0 | Reset |
   || 1 | 0 | 0 | 1 | Set |
   || 1 | 0 | 1 | 1 | Set |
   || 1 | 1 |  0|  1| Toggle |
   || 1 | 1 |  1|  0| Toggle |

   |clk| **t** | **q(n)** | **q(n+1)** | **Comments** |
   |:-:| :-: | :-: | :-: | :-:|
   || 0 | 0 | 0 | No change |
   || 0 | 1 |  1| No change |
   || 1 | 0 |  1| Toggle |
   || 1 |  1|  0| Toggle |
   
   ## 2. D latch. 
   
   ### VHDL code listing of the process  `p_d_latch`
   ```vhdl
    p_d_latch : process (d, arst, en)                                                        
    begin                                                                                    
        if (arst = '1') then                                                                 
            q     <= '0';                                                                    
            q_bar  <= '1';
                                                                                
        elsif (en = '1') then                                                               
            q     <= d;                                                                          
            q_bar  <= not d;                                                                          
        end if;                                                                              
    end process p_d_latch; 
   ```
   
   ### Listing of VHDL reset and stimulus processes from the testbench `tb_d_latch.vhd`
   ```vhdl
    ------------------------------------------
    --Reset generation process
    ------------------------------------------
    p_reset_gen : process
    begin
        s_arst <= '0';
        wait for 28 ns;
        
        s_arst <= '1';
        wait for 53 ns;
        
        s_arst <= '0';
        wait for 200 ns;
        
        s_arst <= '1';

        wait;
    end process p_reset_gen;
    ------------------------------------------
    --Data generation process
    ------------------------------------------
    p_stimulus  : process
    begin
    report "Stimulus process started" severity note;
    s_d  <= '0';
    s_en <= '0';
    
    assert(s_q = '0')
    report "Error" severity error;
    
    --d seq
    wait for 10 ns;
    s_d  <= '1';
    wait for 10 ns;
    s_d  <= '0';
    wait for 10 ns;
    s_d  <= '1';
    wait for 10 ns;
    s_d  <= '0';
    wait for 10 ns;
    s_d  <= '1';
    wait for 10 ns;
    s_d  <= '0';
    --/d seq
    
    assert(s_q = '0' and s_q_bar = '1')
    report "Error" severity error;
    
    s_en <= '1';
    
    --d seq
    wait for 10 ns;
    s_d  <= '1';
    wait for 10 ns;
    s_d  <= '0';
    wait for 10 ns;
    s_d  <= '1';
    wait for 10 ns;
    s_d  <= '0';
    wait for 10 ns;
    s_d  <= '1';
    wait for 10 ns;
    s_en  <= '0';  
    wait for 200 ns;
    s_d  <= '0';    
    --/d seq
    
    --d seq
    wait for 10 ns;
    s_d  <= '1';
    wait for 10 ns;
    s_d  <= '0';
    wait for 10 ns;
    s_d  <= '1';
    wait for 10 ns;
    s_d  <= '0';
    wait for 10 ns;
    s_d  <= '1';
    wait for 10 ns;
    s_d  <= '0';
    --/d seq
    
    --d seq
    wait for 10 ns;
    s_d  <= '1';
    wait for 10 ns;
    s_d  <= '0';
    wait for 10 ns;
    s_d  <= '1';
    wait for 10 ns;
    s_d  <= '0';
    wait for 10 ns;
    s_d  <= '1';
    wait for 10 ns;
    s_d  <= '0';
    --/d seq
    
    report "Stimulus process finished" severity note;
    wait;
    end process p_stimulus;
   ```
   
   ### Screenshot with simulated time waveforms
   
   ![07-ffs](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/07-ffs/Images/latch.png)
   
   
   ## 3.Flip-flops.
   
   ### VHDL code listing of the processes `p_d_ff_arst`, `p_d_ff_rst`, `p_jk_ff_rst`, `p_t_ff_rst`.
   
  ### `p_d_ff_arst`
   
   ```vhdl
   p_d_ff_arst : process (clk, arst)
    begin
        if (arst = '1') then
            q     <= '0';
            q_bar <= '1';
        elsif rising_edge(clk) then
            q     <= d; 
            q_bar <= not d;
        end if;
    end process p_d_ff_arst;
   ```
   Listing of VHDL clock, reset and stimulus processes from the testbench files `p_d_ff_arst`
   
   ```vhdl
         ------------------------------------------
    --Clock generation process
    ------------------------------------------
     p_clk_gen : process
        begin
            while now < 750 ns loop         -- 75 periods of 100MHz clock
                s_clk_100MHz <= '0';
                wait for c_CLK_100MHz_PERIOD / 2;
                s_clk_100MHz <= '1';
                wait for c_CLK_100MHz_PERIOD / 2;
            end loop;
            wait;
        end process p_clk_gen;
    ------------------------------------------
    --Reset generation process
    ------------------------------------------    
     p_reset_gen : process
        begin
            s_arst <= '0';
            wait for 30 ns;
            
            -- Reset activated
            s_arst <= '1';
            wait for 15 ns;
    
            -- Reset deactivated
            s_arst <= '0';
            
            wait;
        end process p_reset_gen;

    ------------------------------------------
    --Data generation process
    ------------------------------------------ 
    p_stimulus : process
    begin
        report "Stimulus process started" severity note;
        s_d <= '0';
        
        --d seq
        wait for 13 ns;
        s_d  <= '1';
        wait for 10 ns;
        s_d  <= '0';
        
        wait for 7 ns;
        
        wait for 4 ns;
        s_d  <= '1';
        wait for 10 ns;
        s_d  <= '0';
        wait for 10 ns;
        s_d  <= '1';
        wait for 10 ns;
        s_d  <= '0';   
        --/d seq
        
        --d seq
        wait for 10 ns;
        s_d  <= '1';
        wait for 10 ns;
        s_d  <= '0';
        wait for 10 ns;
        s_d  <= '1';
        wait for 10 ns;
        s_d  <= '0';
        wait for 10 ns;
        s_d  <= '1';
        wait for 10 ns;
        s_d  <= '0';   
        --/d seq
        
    report "Stimulus process finished" severity note;
    wait;
    end process p_stimulus;
   ```
   
   
   
   `p_d_ff_rst`
   
   ```vhdl
    p_d_ff_rst : process (clk)             
    begin
        if rising_edge(clk) then
            if (rst = '1') then
                q     <= '0';
                q_bar <= '1';
            else
                q     <= d;
                q_bar <= not d;
            end if; 
        end if;
    end process p_d_ff_rst;
   ```
   
   Listing of VHDL clock, reset and stimulus processes from the testbench files from `p_d_ff_rst`
   ```vhdl
   ------------------------------------------
    --Clock generation process
    ------------------------------------------
     p_clk_gen : process
        begin
            while now < 750 ns loop         -- 75 periods of 100MHz clock
                s_clk_100MHz <= '0';
                wait for c_CLK_100MHz_PERIOD / 2;
                s_clk_100MHz <= '1';
                wait for c_CLK_100MHz_PERIOD / 2;
            end loop;
            wait;
        end process p_clk_gen;
        
    ------------------------------------------
    --Reset generation process
    ------------------------------------------    
     p_reset_gen : process
        begin
            s_rst <= '0';
            wait for 30 ns;
            
            s_rst <= '1';
            wait for 15 ns;
    
            s_rst <= '0';
            
            wait;
        end process p_reset_gen;
        
    ------------------------------------------
    --Data generation process
    ------------------------------------------ 
    p_stimulus : process
    begin
        report "Stimulus process started" severity note;
        s_d <= '1';
        
        --d seq
        wait for 10 ns;
        s_d  <= '0';
        wait for 10 ns;
        s_d  <= '1';        
        wait for 10 ns;
        s_d  <= '0';
        wait for 10 ns;
        
        assert(s_q = '0' and s_q_bar = '1')
        report "Error - Failed" severity error;
        
        wait for 20 ns;
        s_d  <= '1';
        wait for 30 ns;
        
        assert(s_q = '1' and s_q_bar = '0')
        report "Error - Failed" severity error;
        --/d seq
        
        --d seq
        wait for 10 ns;
        s_d  <= '0';
        wait for 10 ns;
        s_d  <= '1';
        wait for 10 ns;
        s_d  <= '0';
        wait for 10 ns;
        s_d  <= '1';
        wait for 10 ns;
        s_d  <= '0';
        wait for 10 ns;
        s_d  <= '1';   
        --/d seq
        
    report "Stimulus process finished" severity note;
    wait;
    end process p_stimulus;  
   ```
   Screenshot with simulated time waveforms.
   ![07-ffs](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/07-ffs/Images/s_rst.png)
   
   
  ### `p_jk_ff_rst`
  ```vhdl
    p_jk_ff_rst : process (clk)
    begin                                                              
        if rising_edge(clk) then
            if (rst = '1') then
              s_q     <= '0';
              s_q_bar <= '1';
            else
            if (j = '0' and k = '0') then
                s_q     <= s_q;
                s_q_bar <= s_q_bar;
            elsif (j = '0' and k = '1') then
                s_q     <= '0';
                s_q_bar <= '1';
            elsif (j = '1' and k = '0') then
                s_q     <= '1';
                s_q_bar <= '0';
            else
                s_q     <= not s_q;
                s_q_bar <= not s_q_bar;
            end if;       
          end if;  
        end if;                                                     
    end process p_jk_ff_rst;
    
    q       <=  s_q;
    q_bar   <=  s_q_bar;
```

Listing of VHDL clock, reset and stimulus processes from the testbench files `p_jk_ff_rst`
```vhdl
     p_reset_gen : process
        begin
            s_rst <= '0';
            wait for 30 ns;
            
            s_rst <= '1';
            wait for 15 ns;

            s_rst <= '0';
            
            wait;
        end process p_reset_gen;

    ------------------------------------------
    --Data generation process
    ------------------------------------------    
    p_stimulus : process
    begin
        report "Stimulus process started" severity note;
        s_j <= '0';
        s_k <= '0';
        
        --d seq
        wait for 10 ns;
        s_j <= '0';
        s_k <= '0';        
        wait for 10 ns;
        s_j <= '1';
        s_k <= '0';                       
        wait for 10 ns;
        s_j <= '0';
        s_k <= '1';        
        wait for 10 ns;
        s_j <= '1';
        s_k <= '0';       
        wait for 10 ns;
        s_j <= '1';
        s_k <= '1';
        --/d seq
        
        --d seq
        wait for 10 ns;
        s_j <= '0';
        s_k <= '0';             
        wait for 10 ns;
        s_j <= '0';
        s_k <= '1';     
        wait for 10 ns;
        s_j <= '1';
        s_k <= '0';     
        wait for 10 ns;
        s_j <= '1';
        s_k <= '1';
        --/d seq
        
        report "Stimulus process finished" severity note;
        wait;
    end process p_stimulus;
```
Screenshot with simulated time waveforms.
![07-ffs](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/07-ffs/Images/jk.png)


### `p_t_ff_rst`
```vhdl
p_t_ff_rst : process (clk)
        begin 
         
            if rising_edge(clk) then
                if (rst = '1') then
                    s_q <= '0';
                    
                else 
                    if (t = '0') then
                        s_q <= s_q;
                        
                    elsif (t = '1') then
                        s_q <= not s_q;
                    
                    end if;
                end if;             
            end if;
        end process p_t_ff_rst;
        q       <= s_q;
        q_bar   <= not s_q;
```

Listing of VHDL clock, reset and stimulus processes

```vhdl
          wait for 10 ns;          
          s_t   <= '0';
           wait for 10 ns;          
          s_t   <= '1';
          wait for 10 ns;          
          s_t   <= '0';
           wait for 10 ns;          
          s_t   <= '1';
           wait for 10 ns;          
          s_t   <= '0';
           wait for 10 ns;          
          s_t   <= '1';
           wait for 10 ns;          
          s_t   <= '0';
           wait for 10 ns;          
          s_t   <= '1';
           wait for 10 ns;          
          s_t   <= '0';
           wait for 10 ns;          
          s_t   <= '1'; 
          wait for 10 ns;          
          s_t   <= '0';
           wait for 10 ns;          
          s_t   <= '1';
           wait for 10 ns;          
          s_t   <= '0';
           wait for 10 ns;          
          s_t   <= '1';
           wait for 10 ns;          
          s_t   <= '0';
           wait for 10 ns;          
          s_t   <= '1';
          s_t   <= '0';
           wait for 10 ns;          
          s_t   <= '1';
           wait for 10 ns;          
          s_t   <= '0';
           wait for 10 ns;          
          s_t   <= '1';
           wait for 10 ns;          
          s_t   <= '0';
           wait for 10 ns;          
          s_t   <= '1'; 
```          
          
          
Screenshot with simulated time waveforms.
![07-ffs](https://github.com/yuliakyselova/Digital-electronics-1/blob/main/Labs/07-ffs/Images/t.png)

          
          

   
