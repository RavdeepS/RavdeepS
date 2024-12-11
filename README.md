.data
prompt_continue:   .asciz "Do you wish to continue? y for yes, n for no > "
prompt_digit:      .asciz "Enter a digit > "
newline:           .asciz "\n"
sum_msg:           .asciz "Sum = "

    .text
    .globl _start

_start:
    # Initialize sum to 0
    xor %rax, %rax      # sum = 0 (rax is used to store the sum)

input_loop:
    # Print "Do you wish to continue?"
    mov $4, %rax        # syscall number for sys_write (4)
    mov $1, %rdi        # file descriptor (stdout)
    lea prompt_continue(%rip), %rsi
    mov $39, %rdx       # length of the prompt string
    syscall

    # Read user input for continue response
    mov $0, %rax        # syscall number for sys_read (0)
    mov $0, %rdi        # file descriptor (stdin)
    lea response(%rip), %rsi
    mov $1, %rdx        # length of one byte to read
    syscall

    # Check if response is 'n' (ASCII value of 'n' = 110)
    movb response(%rip), %al
    cmpb $110, %al      # compare with ASCII value of 'n'
    je exit_program     # If input is 'n', exit the program

    # Otherwise, continue with the digit input
    mov $4, %rax        # syscall number for sys_write (4)
    mov $1, %rdi        # file descriptor (stdout)
    lea prompt_digit(%rip), %rsi
    mov $15, %rdx       # length of the prompt string
    syscall

    # Read user input for digit (syscall 0)
    mov $0, %rax        # syscall number for sys_read (0)
    mov $0, %rdi        # file descriptor (stdin)
    lea digit_input(%rip), %rsi
    mov $1, %rdx        # length of one byte to read
    syscall

    # Convert ASCII digit to integer (subtract 48)
    movb digit_input(%rip), %al
    sub $48, %al        # ASCII to integer conversion
    add %al, %rax       # sum += digit

    # Repeat the loop
    jmp input_loop

exit_program:
    # Print "Sum = "
    mov $4, %rax        # syscall number for sys_write (4)
    mov $1, %rdi        # file descriptor (stdout)
    lea sum_msg(%rip), %rsi
    mov $6, %rdx        # length of the "Sum = " string
    syscall

    # Print the sum
    mov $1, %rax        # syscall number for sys_write (1)
    mov %rax, %rdi      # Print the sum (as integer)
    syscall

    # Print a newline for formatting
    mov $4, %rax        # syscall number for sys_write (4)
    mov $1, %rdi        # file descriptor (stdout)
    lea newline(%rip), %rsi
    mov $1, %rdx        # length of newline
    syscall

    # Exit program
    mov $60, %rax       # syscall number for exit (60)
    xor %rdi, %rdi      # exit status 0
    syscall

    .data
response: .byte 0       # Store user response (1 byte)
digit_input: .byte 0    # Store user digit input (1 byte)
