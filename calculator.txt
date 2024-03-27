
.386
.model flat,stdcall
.stack 4096
ExitProcess proto, dwExitCode:dword

include irvine32.inc

.data
	again byte "Press 1 to go again or 0 to end the program: ", 0
	answer byte "Answer: ", 0
	remainder byte " with a remainder of ", 0
	divError byte "Error: Division by zero", 0
	usage byte "Enter 'a' for addition, 's' for subtraction, 'd' for division', 'm' for multiplication", 0
	invalidOperation byte "Invalid operation", 0
	operation byte ?
    decVal1 dword ?
    decVal2 dword ?
	result dword ?
	negNum1 dword 1
	negNum2 dword 1

.code
main proc
	top:
		;displaying how to use this
		mov edx, offset usage
		call writestring
		call crlf

		;getting the operation type
		getOperation:
			call readchar
			mov operation, al
			call writechar
			call crlf

		;comparing the operations (the if-else block)
		cmp operation, 'a'
		je addition
		cmp operation, 's'
		je subtraction
		cmp operation, 'm'
		je multiplication
		cmp operation, 'd'
		je compare
		mov edx, offset invalidOperation
		call writestring
		call crlf
		jmp getOperation

		addition:
			call getNums
			call displayAns
			mov eax, decVal1
			add eax, decVal2
			call writeint
			jmp endProgram

		subtraction:
			call getNums
			call displayAns
			mov eax, decVal1
			sub eax, decVal2
			call writeint
			jmp endProgram

		multiplication:
			call getNums
			call displayAns
			mov eax, decVal1
			mul decVal2
			call writeint
			jmp endProgram

		compare:
			call getNums
			mov eax, decVal1
			test decVal1, eax
			js num1Neg
			mov eax, decVal2
			test decVal2, eax
			js num2Neg
			jmp division

		num1Neg:
			mov negNum1, -1
			neg decVal1
			mov eax, decVal2
			test decVal2, eax
			js num2Neg
			jmp division
		num2Neg:
			mov negNum1, -1
			neg decVal2

		division:
			cmp decVal2, 0
			je divisionError
			call displayAns
			mov eax, decVal1
			xor edx, edx ;clears the edx register and resets everythin to 0
			div decVal2
			mov result, edx
			mul negNum1
			mul negNum2
			call writeint
			mov edx, result
			cmp edx, 0
			jz endDivision ;jumps to endDivision if the remainder is zero and doesn't need to be displayed'
			mov result, edx
			mov eax, result
			mul negNum1
			mul negNum2
			mov edx, offset remainder
			call writestring
			call writeint
		endDivision:
			jmp endProgram

		divisionError:
			mov edx, offset divError
			call writestring

	endProgram:
		call crlf
		mov edx, offset again
        call writestring
		call readdec
		call crlf
		cmp eax, 1
		je top
		invoke ExitProcess, 0
main endp

displayAns proc
	mov edx, offset answer
	call writestring
	ret
displayAns endp

getNums proc
	call readint
	mov decVal1, eax
	call readint
	mov decVal2, eax
	ret
getNums endp

end main
