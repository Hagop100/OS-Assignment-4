# OS-Assignment-4

Program Description

In this assignment we had two programs running together: shmp2.cpp and shmc2.cpp.
Shmp2.cpp was the parent process and was responsible for generating the shared memory
segment and executing the child processes. First, we begin by instantiating our struct BUS,
which holds the data for our bus number, date, title, and most importantly the number of seats
on the bus. Our child processes will be responsible for selling these seats and so this is integral
to our program. After our parent process begins by creating our shared memory segment, it will
execute three children, which will begin to sell out seats. However, there is an oddity in our
output. The number of seats being reported after a child sells a seat is not decrementing
appropriately. For example, we begin with 20 seats, and then our first line of output will show
that we have 17 seats remaining. However, the desired output would be to have 19 seats
remaining, then 18 seats remaining, etc. This is because there is no synchronization between
the children. They are accessing the shared memory segment at times that are not scheduled
properly. The sleep system calls are largely responsible for this odd interweaving and skipping
of the number of seats. That is because when a child is going to sell a seat, it will sleep for a
random interval before and after. This makes it so that other children can enter and sell a seat
when they are not supposed to. The data is altered correctly however and so when we go to
print the number of seats remaining, we see that multiple children may print out the same
number of seats remaining. There is even a case where the number of seats remaining will be
-2. This is again for the reason stated above. Children will enter and sell seats while other
children have already entered the if statement where we check if the seats remaining is greater
than 0. Then the value will dip below zero and then those children will display it as -2 as the
contents of the struct are appropriately altered.

Shmp2.cpp’s is the parent program and is responsible for calling the shmget system call.
This will create our shared memory segment and is done through our function “shm_init”. Next,
it will enter a for loop to “fork” 3 children and execute them with “execl”. The parent waits for
each child to terminate with the function “wait_and_wrap_up”. It does this by calling the “wait”
system call and checking if the current child process ID is equal to the process ID returned by
“wait”. If it is, then it decrements the current number of active children. Once we reach 0 active
children it will detach the program for the shared memory segment and destroy it using “shmdt”
and “shmctl”, respectively. Lastly, the “rpterror” function simply displays errors if our program
were to encounter any. Shmc2.cpp is the child program and begins by checking if the command
line parameters contain enough arguments. If not, it terminates and displays an error message,
else if continues. It will start by attaching itself to the shared memory segment with “shmat”. It
will save the void pointer returned by this system call to bus_ptr. We will use this variable to
access the contents of our struct. Next, we will call sell_seats(). It will run in a while loop for as
long as we are not all out of seats to sell. We begin by calling “srand” which will set up for our
random number generator “rand”. If we have seats remaining, then we will pause the program
with “sleep”. We will do this before we decrement the number of seats in the bus and after. This
is the reason our children will interleave and sell seats in unpredictable orders. We print the
number of remaining seats at the end of our if statement. When we are at 0 seats remaining we
increment the all_out variable to break us out of the while loop. Lastly, this program also
contains the “rpterror” function which handles the displaying of error messages.
