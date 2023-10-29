# Challenge
A binary is given with source code, after running the binary it asks an array then asks you if you want to update it, if no it exits and prints the array, if yes it asks for the index and new value
# Approach
After debugging the program we find that the return address is located 2 indices before the start of our array, which means we could provide the index with -2 and the value with the decimal value of the address of the win function.
