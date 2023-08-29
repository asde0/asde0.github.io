```
We find ourselves locked in an escape room, with the clock ticking down and only one puzzle to solve. The final challenge involves opening the door, and the clue provided to us by the game master is that the key for the encrypted password is a 4-byte sequence.
```

```vhdl
----------------------------------
-- main component
----------------------------------
library ieee;
use ieee.std_logic_1164.all;

entity main is
    port(input_1,input_2 : in std_logic_vector(3 downto 0);
        xorKey : in std_logic_vector(15 downto 0);
        output1,output2 : out std_logic_vector(15 downto 0));
    end main;

architecture Behavioral of main is

    signal decoder1,decoder2: std_logic_vector(15 downto 0);
    component xor_get is
        port(input1,input2 : in std_logic_vector(15 downto 0);
            output : out std_logic_vector(15 downto 0));
        end component;
    component decoder_4x16 is
        port(input : in std_logic_vector(3 downto 0);
            output : out std_logic_vector(15 downto 0));
        end component;
            begin
                L0 : decoder_4x16 port map(input_1,decoder1);
                L1 : decoder_4x16 port map(input_2,decoder2);
                L2 : xor_get port map(decoder1,xorKey,output1);
                L3 : xor_get port map(decoder2,xorKey,output2);

        end Behavioral;
```

Input is 16-bit `xorKey`. 2 other inputs, and 2 outputs, 16 bits each.

```vhdl
 port(input : in std_logic_vector(3 downto 0);
            output : out std_logic_vector(15 downto 0));
        end component;
            begin
                L0 : decoder_4x16 port map(input_1,decoder1);
                L1 : decoder_4x16 port map(input_2,decoder2);
                L2 : xor_get port map(decoder1,xorKey,output1);
                L3 : xor_get port map(decoder2,xorKey,output2);
```

`output` = decoder(`input`) ⊕ `xor_key` 

Given what decoder() does, `output` should vary from `xor_key` with atmost 1 bit.

```
0000000000100011 0000000100110011 0000000000010001 0000000000100001 0000000000100001 0000000000110101 0000000010110111 0000100000110111 0000000000100011 0000001000110011 0000000000010001 1000000000110001 0000000000100001 0001000000110001 0000000000111111
```

Displaying some of the outputs in binary shows that there are in fact outputs that vary by even 3 bits, even though the maximum should be 2. 

Some possible candidates for `xor_key` are 110001, 110111, etc.

It should be easy enough to get an idea through the flag format.
2 of the inputs contain 16 * 16 = 256 combinations, enough for a byte.

So, assuming that the flag is sent in as the input, 
`hex(ord('H')) = 0x48`
`in_1` = 4, `in_2` = 8

`xor_key` = `0000000000100011` ⊕ `10000`
     also = `0000000100110011` ⊕ `100000000`

It seems to be `110011` = 51

`hex(ord('T')) = 0x54`
`in_1` = 5, `in_2` = 4

`xor_key` = `0000000000010001` ⊕ `100000`
	also, `0000000000100001` ⊕ `10000`

Key is now `110001`

Same key for `B` as `T`.

So key might change at each turn. A quick way to find changes would be to xor `in_1` and `in_2`.

```python
s = [35,307,17,33,33,53,183,2103,35,563,17,32817,33,4145,63,54,179,115,57,57,17,32817,23,119,35,307,33,33,33,4145,23,32823,115,55,177,17,177,33,23,32823,35,4147,33,32817,177,113,119,23,19,32819,113,8241,177,561,23,32823,59,19,177,177,57,57,63,63,179,35,113,305,113,17,119,53,179,55,57,177,17,32817,119,8247,59,50,177,53,113,17,183,8247]

def find_set_bits_positions(binary_str):
    set_bits_positions = []
    for i in range(len(binary_str)):
        if binary_str[i] == '1':
            set_bits_positions.append(i)
    return set_bits_positions

i = 0
while (i<len(s)):
    l = (find_set_bits_positions(format(s[i] ^ s[i+1], '016b')[::-1]))
    if (not len(l)):
        l = find_set_bits_positions(format(s[i], '016b'))
        k = len(l)
        print(chr((k-1)*16+k-1), ' ', chr((k+1)*16 + k+1))
    else:
        print(chr(l[0]*16+l[1]), ' ', chr(l[1]*16 + l[0]))
    i += 2
```

```
H   
E   T
$   B
{   ·
I   
_   õ
L   Ä
   0
g   v
3   U
_   õ
V   e
H   
   3
L   Ä
_   õ
&   b
W   u
G   t
_   õ
L   Ä
O   ô
g   v
V   e
_   õ
m   Ö
y   
_   õ
5   S
3   U
3   U
U   w
G   t
h   
V   e
   a
'   r
7   s
_   õ
m   Ö
   0
'   r
V   e
}   ×
```

This sums up, approximately, to `HTB{I_L0v3_VHDL_but_LOve_xx_xxxxxxxxxx_m0re}
`
