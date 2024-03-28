.data
  prompt: .asciiz "Enter a string of 10 characters:"
  n_a: .asciiz "N/A\n"
  message: .space 11

.text
#Calculate N value
  li $t0, 26 

#Print prompt
  la $a0, prompt
  li $v0, 4
  syscall

#Read String
  la $a0, message #Load address for storing string 
  li $a1, 11
  li $v0, 8
  syscall

#Initiliaze counters and sum variables 
  li $s0, 0
  li $s1, 0
  li $s2, 0
#Check for null character before loop
  lbu $t5, message($s0) #Load charcater string 
  beqz $t5, exit #Exit if null character encountered 
loop:
  lbu $t1, message($s0) #Load character from string

  blt $t1,' ',check_end
  bgt $t1,'~',check_end
#Define M
  sub $t3, $t0, 10 #Calculate M

#Convert character to base-N value
  blt $t1, $3, is_numeric
  addi $t1, $t1, -87
  bge $t1, $t0, is_uppercase
  j continue  

is_numeric:
  sub $t1, $t1, '0'
  j continue #Jump to continue loop

is_uppercase:
  sub $t1, $t1, 'A' #Convert uppercase letter to base-N value

continue:
#Check even/odd index and accumulate sum 
  andi $t4, $s0, 1
  beqz $t4, add_odd
  add $s1, $s1, $t1 
  j loop 

add_odd:
  add $s2, $s2, $t1 #Add to odd sum 
  j loop

check_end:
#Calculate and print G-H
  sub $t4, $s1, $s2 #G-H
  beqz $t4, print_na
  li $v0, 1
  move $a0, $t4
  syscall
  j exit 

#Print "N/A"
print_na:
  la $a0, n_a
  li $v0, 4
  syscall 
  j exit 

#Exit program 
exit:
  li $v0, 10
  syscall
