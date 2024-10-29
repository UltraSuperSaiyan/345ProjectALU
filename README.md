


library IEEE;
use IEEE.std_logic_1164.all;	  
use IEEE.numeric_std.all;	   


entity multimedia_alu is
	port(
		rs1 : in STD_LOGIC_VECTOR(127 downto 0);
		rs2 : in STD_LOGIC_VECTOR(127 downto 0);
		rs3 : in STD_LOGIC_VECTOR(127 downto 0);
		operation : in STD_LOGIC_VECTOR(24 downto 0);
		
		rd : out STD_LOGIC_VECTOR(127 downto 0)
	);
end multimedia_alu;



architecture alu_arch of multimedia_alu is

-- do we need signals? or variables?
begin
													 
	
	
	process (rs1, rs2, rs3, operation) is 	  
	  
		variable var1 : std_logic_vector(31 downto 0); 	 
		variable sum : signed(31 downto 0);
	
	
	begin
	
		if (operation(24) = '0') then 
																	--Load Immediate
			
			-- The li operation loads the value of the 16-bit immediate from the operation
			-- (20 downto 5) to a particular sixteen-bit field of rd.
			-- The load index (23 downto 21) indicates the field, ranging from "000" to "111",
			-- corresponding to least significant field and most significant, respectively.
			case operation(23 downto 21) is	 
				
				when "000" =>
					rd(15 downto 0) <= operation(20 downto 5);
				
				when "001" =>
					rd(31 downto 16) <= operation(20 downto 5);
				
				when "010" =>
					rd(47 downto 32) <= operation(20 downto 5);
				
				when "011" =>
					rd(63 downto 48) <= operation(20 downto 5);
				
				when "100" =>
					rd(79 downto 64) <= operation(20 downto 5);
				
				when "101" =>
					rd(95 downto 80) <= operation(20 downto 5);
				
				when "110" =>
					rd(111 downto 96) <= operation(20 downto 5);
				
				when "111" =>
					rd(127 downto 112) <= operation(20 downto 5);
				
				when others =>
					rd <= x"00000000000000000000000000000000";		 --temp		  
				
			end case;
			
		elsif (operation(24 downto 23) = "10") then	  
			--Multiply-Add and Multiply-Subtract R4-Instruction Format
			
			
			if (operation(22) = '0') then
				
				
			elsif (operation(22) = '1') then
					
				
			end if;
			
			
		elsif (operation(24 downto 23) = "11") then	   
			--R3-Instruction Format							   
			
			--Technically, we would look for cases from 22 downto 15. 
			--However, we are actually looking for bits from 18 downto 15. 
			--ex: xxxx0000
			
			case operation(18 downto 15) is
				when "0000" =>										 -- NOP
					var1 := x"00000000";		 --temp
				
				when "0001" =>
					rd <= x"00000000000000000000000000000000";		 --SLHI
				
				when "0010" =>
					rd <= x"00000000000000000000000000000000";		 --AU
				
				when "0011" =>
					rd <= x"00000000000000000000000000000000";		 --CNT1H
				
				when "0100" =>
																 --AHS
					-- we have 8 separate 16-bit halfwords
					for i in 0 to 7 loop
						
						sum := signed(rs1((16*i + 15) downto 16*i)) + signed(rs2((16*i + 15) downto 16*i)); 
						
						if (signed(rs1((16*i + 15) downto 16*i)) > 0 and 
							signed(rs2((16*i + 15) downto 16*i)) > 0 and sum < 0) then
							
							 rd((16*i + 15) downto 16*i) <= std_logic_vector(to_signed(32767, 16)); 
							
						elsif (signed(rs1((16*i + 15) downto 16*i)) < 0 
							and signed(rs1((16*i + 15) downto 16*i)) > 0 and sum > 0) then
							
							rd((16*i + 15) downto 16*i) <= std_logic_vector(to_signed(-32768, 16)); 
							
						else
							
							rd((16*i + 15) downto 16*i) <= std_logic_vector(sum); 
							
						end if;
						
					end loop;
					
					
					
																 
				when "0101" =>
					rd <= rs1 and rs2;							 --AND
				
				when "0110" =>
					rd <= x"00000000000000000000000000000000";		 --BCW
				
				when "0111" =>
					rd <= x"00000000000000000000000000000000";		 --MAXWS	
				
				when "1000" =>
					rd <= x"00000000000000000000000000000000";		 --MINWS
				
				when "1001" =>
					rd <= x"00000000000000000000000000000000";		 --MLHU
				
				when "1010" =>
					rd <= x"00000000000000000000000000000000";		 --MLHCU
				
				when "1011" =>
					rd <= rs1 or rs2;								 --OR
				
				when "1100" =>
					rd <= x"00000000000000000000000000000000";		 --CLZH
				
				when "1101" =>
					rd <= x"00000000000000000000000000000000";		 --RLH
				
				when "1110" =>
					rd <= x"00000000000000000000000000000000";		 --SFWU
				
				when "1111" =>
					rd <= x"00000000000000000000000000000000";		 --SFHS
				
				when others =>
					rd <= x"00000000000000000000000000000000";		 --temp		  
				
			end case;
		
		end if;
	
	
	end process;
	
	
end alu_arch;


