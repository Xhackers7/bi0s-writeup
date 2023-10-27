# Challenge
We need to write an eBPF program that will attach to a given pid, and accepts a list of blacklisted syscalls. If an illegal syscall is made it must kill the process

# EBPF
eBPF is a technology that allows developers to write and load code in the kernel without breaking it
It does this by compiling the code into eBPF bytecode which is then verified and jit compiled and loaded into the kernel

# Approach
I used bcc(a python framework for making eBPF programs) 
I hooked all the blacklisted syscalls to an eBPF function named filter_syscalls, when called it checks if the pid of the process with a target_pid
If true then it kills the process by sending signal 9
