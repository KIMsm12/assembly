Programming Exercises 문제 풀이

# 문제 1. Converting from Big Endian to Little Endian    
**Q : Write a program that uses the variables below and MOV instructions to copy the value from bigEndian to littleEndian, reversing the order of the bytes. The number’s 32-bit value is understood to be 12345678 hexadecimal.**  
```asm
.data
bigEndian BYTE 12h,34h,56h,78h
littleEndian DWORD?
```
**A :**  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

.data
	bigEndian BYTE 12h, 34h, 56h, 78h
	littleEndian DWORD ?

.code
main PROC
	mov ecx, 4
	mov ebx, 3
	mov esi, OFFSET bigEndian
	mov edi, OFFSET littleEndian 

	reverse_loop:
		mov al, [esi]
		mov BYTE PTR [edi + ebx], al
		inc esi
		dec ebx
		loop reverse_loop
	; 잘 되었는지 출력해보기
	mov eax, littleEndian
	call WriteHex
	exit
main ENDP

END main
```

---
# 문제 2. Exchanging Pairs of Array Values   
**Q : Write a program with a loop and indexed addressing that exchanges every pair of values in an array with an even number of elements. Therefore, item i will exchange with item i+1, and item i+2 will exchange with item i+3, and so on.**  
**A :**  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

.data
	arr BYTE 20h, 10h, 40h, 30h, 60h, 50h, 80h, 70h
	arrSize = LENGTHOF arr

.code
main PROC
	mov ecx, arrSize/2
	mov esi, OFFSET arr

	swapLoop:
		mov al, [esi]     
		mov bl, [esi + 1] 
		mov [esi], bl         
		mov [esi + 1], al      
		add esi, 2             
		loop swapLoop  

	mov esi, OFFSET arr
	mov ecx, arrSize
	call DumpMem
	exit
main ENDP

END main
```

---
# 문제 3. Summing the Gaps between Array Values   
**Q : Write a program with a loop and indexed addressing that calculates the sum of all the gaps between successive array elements. The array elements are doublewords, sequenced in nondecreasing order. So, for example, the array {0, 2, 5, 9, 10} has gaps of 2, 3, 4, and 1, whose sum equals 10.**  
**A :**  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

.data
	arr DWORD 0, 2, 5, 9, 10
	arrSize = LENGTHOF arr
	sum DWORD ?

.code
main PROC
	mov ecx, arrSize-1
	mov esi, OFFSET arr
	mov eax, 0

	gaploop:
		mov ebx, [esi+4]
		sub ebx, [esi]
		add eax, ebx
		add esi, 4
		loop gaploop
	mov sum, eax
	call WriteDec
	call Crlf
	exit
main ENDP

END main
```

---
# 문제 4. Copying a Word Array to a DoubleWord array   
**Q : Write a program that uses a loop to copy all the elements from an unsigned Word (16-bit) array into an unsigned doubleword (32-bit) array. **  
**A :**  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

.data
	arr WORD 10h, 20h, 30h, 40h, 50h
	arr2 DWORD LENGTHOF arr DUP(?)

.code
main PROC
	mov esi, OFFSET arr    
	mov edi, OFFSET arr2
	mov ecx, LENGTHOF arr

	copy_loop:
		mov ax, [esi]
		movzx eax, ax
		mov [edi], eax
		add esi, TYPE arr
		add edi, TYPE arr2
		loop copy_loop

	mov ecx, LENGTHOF arr2
	mov esi, OFFSET arr2
	print_loop:
		mov eax, [esi]
		call WriteHex
		call Crlf
		add esi, TYPE arr2
		loop print_loop
	exit
main ENDP

END main
```

---
# 문제 5. Fibonacci Numbers   
**Q : Write a program that uses a loop to calculate the first seven values of the Fibonacci number sequence, described by the following formula: Fib(1) = 1, Fib(2) = 1, Fib(n) = Fib(n – 1) + Fib(n – 2).**  
**A :**  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

.data
	fib DWORD 7 DUP(?)

.code
main PROC
	mov DWORD PTR fib, 1
	mov DWORD PTR fib+4, 1

	mov ecx, 5
	mov esi, OFFSET fib

	calc_fib_loop:
		mov eax, [esi]
		add eax, [esi+4]
		mov [esi+8], eax
		add esi, 4
		loop calc_fib_loop

	mov ecx, 7
	mov esi, OFFSET fib

	print_fib_loop:
		mov eax, [esi]
		call WriteDec
		mov al, ' '
		call WriteChar
		add esi, 4
		loop print_fib_loop

	exit
main ENDP

END main
```

---
# 문제 6. Reverse an Array   
**Q : Use a loop with indirect or indexed addressing to reverse the elements of an integer array in place. Do not copy the elements to any other array. Use the SIZEOF, TYPE, and LENGTHOF operators to make the program as flexible as possible if the array size and type should be changed in the future.**  
**A :**  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

.data
	myArray DWORD 1, 2, 3, 4, 5

.code
main PROC
	mov esi, OFFSET myArray
	mov edi, OFFSET myArray + SIZEOF myArray - TYPE myArray
	mov ecx, LENGTHOF myArray/2

	reverseLoop:
		mov eax, [esi]
		mov ebx, [edi]
		mov [esi], ebx
		mov [edi], eax
		add esi, TYPE myArray
		sub edi, TYPE myArray
		loop reverseLoop
	
	mov ecx, LENGTHOF myArray
	mov esi, OFFSET myArray

	print_loop:
	    mov eax, [esi]
		call WriteDec
		mov al, ' '
		call WriteChar
		add esi, TYPE myArray
		loop print_loop

	exit
main ENDP

END main
```

---
# 문제 7. Copy a String in Reverse Order   
**Q : Write a program with a loop and indirect addressing that copies a string from source to target, reversing the character order in the process. Use the following variables:**  
```asm
source BYTE "This is the source string",0
target BYTE SIZEOF source DUP('#')
```
**A :**  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

.data
	source BYTE "This is the source string.", 0
	target BYTE SIZEOF source DUP('#')

.code
main PROC
	mov ecx, LENGTHOF source - 1 ; 마지막 널 제외
	
	mov esi, OFFSET source      
	add esi, (LENGTHOF source - 2) * TYPE source ; source의 마지막 문자로 이동
	
	mov edi, OFFSET target ; target의 시작 주소로 이동

	reverse_loop:
	    mov al, [esi]
		mov [edi], al
		sub esi, TYPE source
		add edi, TYPE target
		loop reverse_loop

	mov BYTE PTR [edi], 0 ; 널 종료 문자 추가

	mov edx, OFFSET target
	call WriteString
	call Crlf

	exit
main ENDP

END main
```

---
# 문제 8. Shifting the Elements in an Array   
**Q : Using a loop and indexed addressing, write code that rotates the members of a 32-bit integer array forward one position. The value at the end of the array must wrap around to the first position. For example, the array [10,20,30,40] would be transformed into [40,10,20,30].**  
**A :**  
```asm
.386
.MODEL FLAT, STDCALL
.STACK 4096
include Irvine32.inc

.data
	myArray DWORD 10, 20, 30, 40

.code
main PROC
	; 마지막 원소를 EAX 레지스터에 로드
	mov eax, [myArray + (LENGTHOF myArray - 1) * TYPE myArray]

	; 반복 횟수는 배열 길이 - 1
	mov ecx, LENGTHOF myArray - 1

	; ESI = 마지막 원소 주소
	mov esi, OFFSET myArray
	add esi, (LENGTHOF myArray - 1) * TYPE myArray

	shift_loop:
		mov edx, [esi - TYPE myArray] ; 이전 원소 로드
		mov [esi], edx                 ; 현재 위치에 이전 원소 저장
		sub esi, TYPE myArray          ; 다음 원소로 이동(왼쪽으로)
		loop shift_loop
	mov [myArray], eax

	mov ecx, LENGTHOF myArray
	mov esi, OFFSET myArray

	print_loop:
	    mov eax, [esi]
		call WriteDec
		mov al,' '
		call WriteChar
		add esi, TYPE myArray
		loop print_loop

	exit
main ENDP

END main
```

---
