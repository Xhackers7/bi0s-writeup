# Challenge
A binary is given with source code, after running the binary it asks for your name and says you're not an admin. After inspecting the source code we find out a variable in the stack named isAdmin is set to 0.
# Approach
The condition that gatekeeps us checks if isAdmin is 0 so all we have to do is input a large enough string that overwrites isAdmin to something other than 0
