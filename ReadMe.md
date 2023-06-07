FILE -> re-start1
in define string: 10--> new line,  0 --> null signal to terminate string
-------------------------------------------------------------------------
with add number to eax that string mov to it the words is removed:
{
    segment .data
    var db "hello this is mmd", 0
    mov eax, var
    call print_string ----> output: "hello this is mmd"
    call print_nl
    mov eax, var
    add eax, 6
    call print_string ----> output: "this is mmd"
    call print_nl
    mov eax, var
    add eax, 11
    call print_string ----> output: "is mmd"
    call print_nl

}
-------------------------------------------------------------------------
we can access and change byte of string with upeer method like:
{
    segment .data
    var db "This mmd", 0
    mov eax, var
    add eax, 5
    mov BYTE [eax], 'M'
    mov eax, var
    call print_string

}
-------------------------------------------------------------------------
we push the address of next instruction onto stack 
-------------------------------------------------------------------------
in local vars at first we allocate the space with (sub esp, 4) and then we deAllocate with (add esp, 4)
-------------------------------------------------------------------------

FILE -> re-start4
pushad --> we push all of register {eax, ecx, ebx, orginal {esp, ebp, esi, edi}}

popad --> Pop the registers in revers order , (orginal esp is restord)
-----------
popfd --> we push all of flages

popad --> Pop the flages in revers order
-------------------------------------------------------------------------
if you dont have equal pop with push the app will be crash or you can add 4 to esp for evry push in program
-------------------------------------------------------------------------
################################################################################################################################
FILE -> re-start2
printf:
    we should first set it as external func: extern printf
    we should push the data we want to print in order to stack
    we should push the format as last value to stack
    and then after calling (printf) you should add 4 to esp for every push happend
################################################################################################################################
functions:
    with using call for calling function the address of next instruction after this function is pushed to stack
    eip and esp is set to address of function
    after finishing function we should return to main function with (ret)
    ret is set the eip to address of next instruction and the address will be removed from stack

***important: in embedded systems you might only have 256 bytes for the stack, and if each function takes up 32 bytes then you can only have function calls 8 deep - function 1 calls function 2 who calls function 3 who calls function 4 .... who calls function 8 who calls function 9, but function 9 overwrites memory outside the stack. This might overwrite memory, code, etc.
---------------------------------------------------------------------------------------------------
gef and gdb debugger:
    with (b) you can make braekPoint
    with (r) we start our program
    with (si) we go to next step
    with (c) continue untill the program finish
---------------------------------------------------------------------------------------------------
FILE -> re-start3
this two bottem function is used for passing args to function:
    function prologue:
        when we want start function we start with prologue ----> ebp = extended Base Pointer

    unction epilogue:
        when we want end function we end with epilogue

    porologue:
        1) push ebp
        2) mov ebp, esp

    epilogue:
        1) mov esp, ebp
        2) pop ebp

---------------------------------------------------------------------------------------------------
status of stack:
                    oldebp  0x0
                    ret address  0x4
                    arg2    0x8
                    arg1    0xC
for access to arguments of functions :
    mov eax, [ebp+0Ch] -------> first arg
    mov eax, [ebp+8] ----> sec arg

1) 0x%08X   --> this mean if we didnt have 8 digit put 0 to gain accsess of 8 digit and the big X mean we want use HEX in string
---------------------------------------------------------------------------------------------------
SAVING STATUS OF REGISTERS(choices):
    1) push modified register
    2) push all register
    3) push flags

PROJECT -> re-proj1
################################################################################################################################
CALLING CONVENTIONS(check screenshots for more detial):
32bit calls:
    1) cdecl
    2) std call
    3) this call(thiscall)
    4) Fast call


cdecl:
    the main function clear the stack like functions we write 
    exmp:
        push ebp
        mov ebp, esp
        .
        .
        .
        mov esp, ebp
        ret

std:
    the main function didnt delete the stack and caller of that function should clear that
    exmp:
        push eax
        push format
        call printf ---> printf function didnt clear the stack
        add esp, 8

microsoft 32bit fast call:
    the {ecx} and {edx} are used as 2 first parameter

ABI : Application binarry interface
---------------------------------------------------------------------------------------------------
FILE -> re-start5
for set local var for each function:
    sub esp, (x*4) --> number of vars
    exmp:
        sub esp, 0Ch --> 12
        mov DWORD [ebp-4], 0
        mov DWORD [ebp-8], 1
        mov DWORD [ebp-12], 2
        .
        .
        .
        .
        add esp, 0Ch
---------------------------------------------------------------------------------------------------
lea eax, [esp]: grab the addres of value at esp
**important: (scanf) function is have read_int and store the input in local var
---------------------------------------------------------------------------------------------------
Enter: prolog, leave: epolog
exmp:
    Enter 0,0 --> the first 0 is say how manye local var do you need

**important: we just use leav ,enter is longe than prolog
---------------------------------------------------------------------------------------------------
MACRO IN enter and leave: FILE -> re-start6
ENTER SECTION:
    %push context
    %stacksize flat
    %assign %$localsize 0
    %arg arg1:dword            -----> arg('name of argument') ,  dword('type of arg we pass to it')
    %local arg1:dword, arg2:dword
    enter %$localsize, 0


LEAVE SECTION:
    leave
    ret
%pop
################################################################################################################################
FLOAT STACK: FILE -> re-start7       PROJECT -> re-proj2

*in project2 i change the structure of code from video to making multi func for print data

**important: with normal stack we have float stack that save the float nums.
---------------------------------------------------------------------------------------------------
Floating Point Unit (FPU):
    1) 80Bit register
    2) ST0, ....., ST7
    3) LIFO stack
---------------------------------------------------------------------------------------------------
Float Commands:
    FLD [source]  --> push to float stack
    FILD [source] --> push int to float stack

    FST [destination] --> copy the last data of stack to dest
    FSTP [dest] --> copy and pop the last data of stack to dest
    FIST [dst]
    FISTP [dst]
CHECK MATH OPERATION IMAGE IN SCREENSHOT FOLDER
---------------------------------------------------------------------------------------------------
Data : 32bit-floatStracture.png AND 64bit-floatStracture.png
sign : علامت مثبت و منفی
exponent: جای قرار گرفتن علامت اعشار را نشان میدهد
mantissa: ادغام شده اعداد اعشار و عدد صحیح پشت سر هم
---------------------------------------------------------------------------------------------------
**Avoid form compar to equlivity like -> 0.1+0.1+0.1 == 0.3 ---> this is wrone the answer is 0.299999998
---------------------------------------------------------------------------------------------------
COMPAR INSTRUCTION IN FLOAT -> FILE: re-start8
    FCOM [source] --> compar ST0 and src            FCOMI [source] --> compar ST0 and src(int)
    FCOMP [source] --> compar ST0 and src AND pop it from stack        FICOMP [source] --> int version
    FCOMPP --> compar ST0 and ST1 AND pop them from stack
    FIST --> compar ST0 and 0
################################################################################################################################
Pipelines --> 2.22   IMAGE: Pipeliens_time
disc: when we use pipelines our instructions act in the same time and more time will be saved
---------------------------------------------------------------------------------------------------
Branching: 



translate :
    allocation => تخصیص
    recursive => بازگشتی
    conventions => قرارداد
    assumption => فرض
    instruction => دستورالعمل
    macros => درشت دستور
    procedure => رویه
    tedious => خسته کننده
    

