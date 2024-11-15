# PLTEC
Pheonix Language To Executable Converter

## What is PLTEC
PLTEC or Pheonix Language To Executable Convert is a flexible tool designed to convert a custom language provided by the user into Low level programming languages like .ASM (Assembly) or even a executable

## How does it work?
PLTEC is a complex tool but its working i very simple. The program comes included with a language set (JSON) file which has key-value pairs. The keys are the function names and the values are Their Assembly code. It has various sections like -> dataSECTION, bssSECTION, etc. The user can provide or use the default Language (JSON) file which has the following format -> 

    {
      "[Executable type (in uppercase)]": "",
      "[x86 or x64]": "",
      "SET": {
        "[The include syntax]/*DVALUE/INCLUDEINS/S[The seperator you want. Common - ' ']": "includeFuncs",
        [Some more includes if you like],
        "[The include file name]/INCLUDE": {
          "[The function syntax]": "[The function it needs to call in the language SET]",
          [Some more funcs and stuff]
        },
        [Some more include files if you want]
      }
    }

## Explanation
The above format is what is required to be followed to run this program successfully. \
The first key-value pairs are the executable format and BITS type [Commonly x86 used]. x86 means 32-bit and x64 means 64-bit but most modern OS support both \
The next key should be 'SET' and it should be a JSON object. All the key-value pairs inside it are the actual functions. 

In 'SET' the following keys are optional -> 
1. A Instruction Syntax followed by /INCLUDEINS (Include Instruction, means that the specified instruction is a instruction used to import specific functions that are not available globally.) 
2. A Name followed by /INCLUDE (Include File, means that if the user use the include instruction and provide this specific name, the program then imports the functions inside the specific JSON object and the same JSON object inside the Language SET.) 

This allows the program to take less storage as only the functions, that the user imported are present. 

In 'SET' the following keys are required -> 
1. A Instruction key 

## The format for all instruction keys. (Including Include Instruction keys)
The format for all instruction keys is the follow -> \
"[The base syntax][Value Specifiers]/S[Seperator]" \
Explanation: \
The base syntax: The base syntax is a syntax, that the user needs to write so the program understands that the use is trying to run this specific function. 

Value Specifiers: The value specifiers act as parameters to the function that was assgined to the key be the user. Please take a look at the DEF_LanguageSET.json file to understand the required parameters. 

Seperator: The entire key should have no spaces. No spaces in general. The seperator tells the program a singular character that will be used to seperate, Base syntax, and the Value Specifiers. 

## All Value Specifiers
/DNAME: Data name, \
/DVALUE: Data value, \
/DSIZE: Data size, \
/INCLUDEINS: include instruction. 

## Examples
### Example Language.json
    {
        "ELF": "",
        "x86": "",
        "SET": {
            "#include:/*DVALUE/INCLUDEINS/S ": "includeFuncs",
            "BasicIO/INCLUDE": {
                "print:/*DVALUE/*DNAME/S ": "printINS",
                "input:/*DNAME/*DSIZE/S ": "inputINS",
                "clean:/S": "cleanupINS",
                "return:/*DVALUE/S ": "returnINS"
            }
        }
    }

### Example Code before convertion using the above file

    #include: BasicIO
    print: Hello HelloVar
    clean:
    return: 0

### Example Assembly code generated via the above syntax

    section .data
    HelloVar_pri db "Hello"
    HelloVar_pri_LEN equ $ - HelloVar_pri
    
    section .text
    global _start
    
    _start:
    mov ecx, HelloVar_pri
    mov edx, HelloVar_pri_LEN
    call print
    call cleanup32
    mov ebx, 0
    call return
    
    print:
    mov eax, 4
    mov ebx, 1
    int 80h
    
    input:
    mov eax, 3
    mov ebx, 1
    int 80h
    
    return:
    mov eax, 1
    int 80h
    
    cleanup32:
    xor eax, eax
    xor ebx, ebx
    xor ecx, ecx
    xor edx, edx
    xor esi, esi
    xor ebp, ebp
    ret

### Please try to understand the source code and then you would have a greater idea of what to write in Lang.json.

# Running PLTEC
## Basic Question asked

Path: The path to the file to convert \
Output Path (Don't include extention): The path where you want to have the output files. Do not include extention of the output file (The word after '.' in file) \
Path for Lang.json: The path to the Language.json file (Custom code syntax JSON file). Leave to use Default language syntax \
Extra Commands (Seperate via ','): Extra commands seperated by comma. Extra commands ->
1. -debug: Provides full detail about what the program is doing.
2. -o: Creates a object file with the Assembly file.
3. 3. -object: Same as -o.
