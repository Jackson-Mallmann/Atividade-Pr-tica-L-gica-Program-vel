-- Jackson Mallmann
-- RU: 3923666
-- DATA: 07/04/2025
-- Migração de circuito discreto para FPGA

-- Importa a biblioteca IEEE, necessária para tipos de dados lógicos

library IEEE;
use IEEE.STD_LOGIC_1164.ALL; -- Fornece tipos de dados padrão, como STD_LOGIC
use IEEE.STD_LOGIC_ARITH.ALL; -- Permite operações aritméticas em sinais STD_LOGIC_VECTOR
use IEEE.STD_LOGIC_UNSIGNED.ALL; -- Permite operações sem sinal em vetores de bits

-- Declaração da entidade chamada 'JacksonMallmann'

entity JacksonMallmann is
    Port (
        clk   : in  STD_LOGIC; -- Sinal de clock
        reset : in  STD_LOGIC; -- Sinal de reset
        push  : in  STD_LOGIC; -- Sinal de entrada para controle da saída
        saida : out STD_LOGIC_VECTOR (3 downto 0) -- Saída de 4 bits
    );
end JacksonMallmann;

-- Declaração da arquitetura 'Behavioral'

architecture Behavioral of JacksonMallmann is
    -- Define um tipo de array para armazenar estados de 4 bits
    type state_array is array (0 to 7) of STD_LOGIC_VECTOR(3 downto 0);
    -- Declaração de constantes para estados ímpares
    constant odd_states  : state_array := ("1111", "0111", "1011", "0011", "1101", "0101", "1001", "0001");
    -- Declaração de constantes para estados pares
    constant even_states : state_array := ("1110", "0110", "1010", "0010", "1100", "0100", "1000", "0000");
    
    -- Declaração do estado atual, de 0 a 7
    signal state_index : integer range 0 to 7 := 0;
    -- Sinal para alternar entre estados pares e ímpares
    signal use_even : STD_LOGIC := '0';
    -- Estado atual da saída
    signal state : STD_LOGIC_VECTOR(3 downto 0) := "1111";

begin

    -- Processo sensível a clk e reset

    process (clk, reset)
    begin
     if reset = '1' then -- Se reset for acionado
        state_index <= 0; -- Reseta o índice do estado
        use_even <= '0'; -- Inicia com estados ímpares
        state <= "1111"; -- Estado inicial
     elsif rising_edge(clk) then -- Detecta borda de subida do clock
        if push = '1' then -- Se push for acionado
        if use_even = '0' then
           state <= odd_states(state_index); -- Usa os estados ímpares
        else
		  state <= even_states(state_index); -- Usa os estados pares
        end if;
                
        if state_index = 7 then -- Se atingir o último estado
           state_index <= 0; -- Reinicia o índice
           use_even <= not use_even; -- Alterna entre estados pares e ímpares
        else
           state_index <= state_index + 1; -- Avança para o próximo estado
        end if;
        end if;
        end if;

end process;
    
    -- A saída recebe o estado atual

    saida <= state;
    
end Behavioral;
