# Mips
programming for assignments
	.text
	.align 2
	.globl main
main:	la     	$t0, toss
	move 	$t1, $0		# init $t1 = 0

	li 	$a0, 285824419  # Initialize seed
	jal 	srand
	
L1:	bge 	$t1, 3, exit	# check loop condition
	lw	$t2, 0($t0)	# load # of tosses
############ begin your code ################################
# current code will just print out 3 random numbers generated
li.s $f1,0.0           #pi_estimate
  li $t1,0               #number_in_circlr
  li.s $f5,1.0           #for comparision with distance_squared
  move $t5,$0            #toss name 

for: slt $t7,$t5,$t2     #for loop lessthan $t7=0;if ($t5=i>t2=n) or(i>n) 
  jal rand_range         #creating random number 'x'
  mul.s $f2,$f0,$f0      #multiplying to get x^2
  jal rand_range         #creating random number 'y'
  mul.s $f3,$f0,$f0      #multiplying to get y^2
  add.s $f4,$f2,$f3      #adding distance_squared=x^2+y^2 ; $f4=$f2^2+$f3^2
  c.le.s $f4,$f5         #comparing distance<=1
  bc1t increment         #jumping to increment to increase 

loop: addi $t5,$t5,1     #increasing toss( i+1)
  j for                  #going back to for loop
increment: add $t1,$t1,1 #increments number_in_circle++
  j loop                 #going back to the normal loop

  cvt.s.w $f4,$f4        # converting into float
  li.s $f7,4.0           #declaring 4.0
  mtc1  $t5,$f8          #movr int toss
  cvt.s.w $f8,$f8
  mov.s $f0,$f4

  mul.s $f0,$f0,$f7
 div.s $f0,$f0,$f3
  

############ end your code ###################################
	mov.s  	$f12, $f0 	# move result to print argument
	li   	$v0, 2  	# 2 = print float, 3 = print double
	syscall			# system call for print result
	addi	$a0, $0, 0xA	# ascii code for LF
	addi 	$v0, $0, 0xB	# syscall 11
	syscall
	addi	$t1, $t1, 1	# increment $t1
	add	$t0, $t0, 4	# adjust index
	j	L1		# iterate outer loop
exit:	li   	$v0, 10		# system call for exit
	syscall			# we are out of here

# random float range
rand_range:
	addi	$sp, $sp, -12
	sw	$t0, 0($sp)
	sw 	$t1, 4($sp)
	sw	$ra, 8($sp)
	jal	rand		# $v0 has random number
	mtc1	$v0, $f20	# move the int to $fp reg, mtc1.d for double
	cvt.s.w	$f20, $f20	# convert int to float, cvt.d.w for double
#	la	$t0, rmax
#	lw	$t1, 0($t0)
#	mtc1	$t1, $f21
#	cvt.s.w	$f21, $f21	# f21 = rmax
	l.s	$f21, rmaxf
	div.s	$f22,$f20,$f21	# random = rand()/rmax
	l.s	$f23, LB	# f23 = lb
	l.s	$f24, diff	# f24 = diff
	mul.s	$f25,$f22,$f24	# f25 = f22*f24
	add.s	$f0, $f23, $f25
	lw	$t0, 0($sp)
	lw	$t1, 4($sp)
	lw	$ra, 8($sp)
	addi 	$sp, $sp, 12
	jr 	$ra

# For seeding the random number generator
srand:
	addi	$sp, $sp, -4
	sw 	$t0, 0($sp)
	la 	$t0, rseed 	# Store the given seed
	sw 	$a0, 0($t0)
	lw	$t0, 0($sp)
	addi	$sp, $sp, 4
	jr 	$ra
	
# random number generator
# seed = seed * 1103515245 +12345; 
rand:
	addi	$sp, $sp, -20
	sw	$t0, 0($sp)
	sw	$t1, 4($sp)
	sw	$t2, 8($sp)
	sw	$t5, 12($sp)
	sw	$ra, 16($sp)
	la   	$t5,rseed     	# Get the current seed
	lw   	$t0,0($t5)     
	li   	$t1,1103515245  # seed * 1103515245
	mul  	$t2,$t0,$t1
	add  	$v0,$t2,12345 	# (seed * 1103515245) + 12345
	li   	$t0,0x7FFFFFFF	# ((seed * 1103515245) + 12345)&0x7FFFFFFF
	and  	$v0,$v0,$t0	
	sw   	$v0,0($t5)      # Save the seed
	lw	$t0, 0($sp)
	lw	$t1, 4($sp)
	lw	$t2, 8($sp)
	lw	$t5, 12($sp)
	lw	$ra, 16($sp)
	addi	$sp, $sp, 20
	jr   $ra

	.data
	.align 0
pi:	.float	0.0
toss:	.word 100, 1000, 10000
rseed: 	.word 1
rmax: 	.word 2147483647
rmaxf:	.float 2147483647.0
LB:	.float -1.0
UB:	.float 1.0
diff:	.float 2.0
