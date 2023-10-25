# Reversing VM protected binary
## Challenge 
Given an app that emulates a given program using custom opcodes. We need to write
a disassembler to break down   
its behaviour & reverse it to get the correct key.
## Approach
To gain more understanding about the program, I began by analysing the file, and figured its a striped executable.
Then I dissassembled the program in ida, when looking at the assembly its obvious that a lot of random obfuscation is going on.
Taking a closer look into the program i figured it takes an input from the user, then puts it to some data structure, after that it calls a function(which is the actual vm implementation), then it checks if the function returned 4
Our goal is to make the function return 4
### VM implementation
The vm uses 2 data strctures a region in memory initilized with 0s to store data and an 85 bytes long region initialized with the opcodes.
The vm uses 3 kinds of instruction:
- opcode(8 bytes) addr1(8 bytes), val(8 bytes) - total 3 bytes
- opcode(8 bytes) addr1(first 4 bytes), addr2(last 4 bytes) - total 2 bytes
- opcode(8 bytes) addr1(8 bytes) - total 2 bytes

Instructions which modify control flow like the jmp instruction and its variants use an address which is offset from the code_segment, while other instruction use addresses offset from the data_segment
### Dissasembling opcodes
Once we figured out how the vm works, now its time to reverse the protected program. First step is to write a dissasembler.
Here is a simple one I wrote
```python3
mapping = ['set', 'cpy', 'add', 'sub', 'mul', 'div', 'xor', 'or', 'and', 'chk', 'shl', 'shr', 'je', 'jne', 'jg', 'jng', 'jl', 'jnl', 'push', 'pop', 'cmp', 'ret']

with open("code", "r") as file:
    data = file.read()

bytes_list = [int(byte, 16) for byte in data.split()]

index=0
while index < len(bytes_list):
    opcode = bytes_list[index]
    arg1 = 0
    arg2 = 0
    if 2 <= opcode <= 12 or opcode == 21:
        arg = bytes_list[index+1]
        arg1=arg>>4
        arg2=arg&0xF
        print(f"cs+{index}: {mapping[opcode-1]} ds:{arg1}, ds:{arg2}")
    elif opcode == 1:
        arg1 = bytes_list[index+1]
        arg2 = bytes_list[index+2]
        print(f"cs+{index}: {mapping[opcode-1]} ds:{arg1}, {arg2}") 
        index+=1
    else:
       arg1 = bytes_list[index+1]
       if 13 <= opcode <= 18:
            print(f"cs+{index}: {mapping[opcode-1]} cs:{arg1}")
       else: print(f"cs+{index}: {mapping[opcode-1]} ds:{arg1}")
    index += 2
```

After succesfully deassembling the opcodes, we can figure out its does some conditional checks on the input, and based on this increments the value to be returned
Inorder to return 4 every condition needs to be true, after going through the assembly and figuring out the xors(if a ^ b = x, x ^ b = a) we find out the requried input is the decimal equivilant of the hex number DEADBEEF
