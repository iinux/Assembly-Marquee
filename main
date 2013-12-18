data segment
	osec		db	?
	string1		db	'1.input string$'
	string2		db	'2.begin flash$'
	string3		db	'3.exit$'
	string4		db	'input string please:$'
	buf		db	20
	bufCount	db	?
	bufBody		db	20 dup (?)
	star		db	70 dup('*'),'$'
	lstrSize	db	?
	rstrSize	db	?
	temp		db	?
	flashBuf	db	90 dup(?)
	lstrPos		dw	?
	rstrPos		dw	?
	flashCount	db	?
	pause		db	?
	blank		db	70 dup (32),'$'
data ends

code segment
	assume cs:code,ds:data
	
puts		macro X
		lea dx,X
		mov ah,09h
		int 21h
endm
crlf 		proc near

		mov  dl,0DH 
		mov  ah,02h      
		int  21h  
		mov  dl,0AH   
		mov  ah,02h      
		int  21h
					
		ret

crlf endp
inputString 	proc near
	
		push ax
		push bx
		push cx
		
		lea dx,buf
		mov ah,0ah
		int 21h
		
		mov ah,'$'
		xor cx,cx
		mov cl,bufCount
		lea bx,bufBody
		add bx,cx
		mov [bx],ah
		
		pop cx
		pop bx
		pop ax
		
		ret

inputString endp
flash		proc near
	
		call divideString
		
		mov ax,ds
		mov es,ax
		
flashInit:	mov pause,0
		mov cx,90
		lea si,flashBuf
		mov al,32
loop1:		mov [si],al
		inc si
		loop loop1
		
		dec si
		mov al,'$'
		mov [si],al
		
		lea di,flashBuf
		add di,10
		xor cx,cx
		mov cl,lstrSize
		sub di,cx
		mov lstrPos,di
		lea si,bufBody
		cld
		rep movsb
		
		lea di,flashBuf
		add di,80
		mov rstrPos,di
		xor cx,cx
		mov cl,rstrSize
		cld
		rep movsb
		
		mov flashCount,35
				
timer:		mov ah,2ch
		int 21h
		
		mov ah,06h
		mov dl,0ffh
		int 21h
		
		cmp al,'x'
		jz exitFlash1
exitFlash2:	cmp al,'r'
		jz flashInit
		cmp al,'p'
		jz pause1
pause2:		
		cmp osec,dh
		jnz interval
		jmp timer
		
interval:
		mov osec,dh
		cmp pause,0ffh
		jz timer
		call shift
		call putsFlashBuf
		dec flashCount
		jz exitFlash1
		
		jmp timer
		
pause1:		not pause
		jmp pause2
exitFlash1:	mov ah,2
		mov bh,0
		mov dh,3
		mov dl,0
		int 10h		
		jmp waitSel
over:			
		ret

flash endp
divideString	proc near
	
		xor ax,ax
		mov al,bufCount
		mov temp,2
		div temp
		mov lstrSize,al
		mov al,bufCount
		sub al,lstrSize
		mov rstrSize,al
		
		ret

divideString endp
shift		proc near
	
		mov si,lstrPos
		xor ax,ax
		mov al,lstrSize
		add si,ax
		dec si		
		xor cx,cx
		mov cl,lstrSize		
shiftloop1:	mov al,[si]
		mov [si+1],al
		dec si
		loop shiftloop1		
		mov al,32
		mov [si+1],al		
		inc lstrPos
		
		mov si,rstrPos
		xor cx,cx
		mov cl,rstrSize		
shiftloop2:	mov al,[si]
		mov [si-1],al
		inc si
		loop shiftloop2	
		mov al,32
		mov [si-1],al		
		dec rstrPos		
		
		ret

shift endp
clear		proc near
	
		mov ah,2
		mov bh,0
		mov dh,3
		mov dl,0
		int 10h
		puts blank
		mov ah,2
		mov bh,0
		mov dh,3
		mov dl,0
		int 10h		
		ret

clear endp
putsFlashBuf	proc near
	
		mov ah,2
		mov bh,0
		mov dh,4
		mov dl,0
		int 10h
		puts star
		
		mov ah,2
		mov bh,0
		mov dh,5
		mov dl,0
		int 10h		
		lea si,flashBuf
		add si,10
		mov cx,70
pfBufLoop1:	mov ah,02h
		mov dl,[si]
		inc si
		int 21h
		loop pfBufLoop1
		;puts flashbuf
		
		mov ah,2
		mov bh,0
		mov dh,6
		mov dl,0
		int 10h
		puts star
		
		mov ah,2
		mov bh,0
		mov dh,5
		mov dl,0
		int 10h
		mov ah,2
		mov dl,'*'
		int 21h
		
		mov ah,2
		mov bh,0
		mov dh,5
		mov dl,69
		int 10h
		mov ah,2
		mov dl,'*'
		int 21h		
		ret

putsFlashBuf endp

start:
		mov ax,data
		mov ds,ax
showMenu:	puts string1
		call crlf
		puts string2
		call crlf
		puts string3
		call crlf
		
waitSel:	mov ah,06h
		mov dl,0ffh
		int 21h
		cmp al,'1'
		jz sel1
		cmp al,'2'
		jz sel2
		cmp al,'3'
		jz exit
		jmp waitSel
	
		
sel1:		puts string4
		call inputString
		call clear
		jmp waitSel
sel2:		call flash
		
exit:		mov ah,2
		mov bh,0
		mov dh,7
		mov dl,0
		int 10h		
		mov ah,4ch
		int 21h
code ends
end start
