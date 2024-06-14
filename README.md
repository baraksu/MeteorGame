# MeteorGame
.MODEL small
.STACK 100h

.DATA
; Homescreen
homescreen1 db 13,10,' ___  __  ______  _____  ______   ____  _____   ______    _   ___  __ _______  $'
homescreen2 db 13,10,' |  \/  || |____||_   _|| |____| / __ \ |  __|  |  ___|  /_\  |  \/  || |____| $'
homescreen3 db 13,10,' | |\/| || |___    | |  | |___  | |  | || |\\   | | __  // \\ | |\/| || |___   $'
homescreen4 db 13,10,' | |  | || |___|   | |  | |___| | |  | || | \\  | |_| |/ --- \| |  | || |___|  $'
homescreen5 db 13,10,' |_|  |_||_|_____  |_|  |_|_____ \_\/_/ |_| ||  |_____||_/ \_||_|  |_||_|_____ $'

namepresentation db 13,10,'                                by Neria Tsafrir                          $'
lines db 13,10,'  ____________________________________________________________________________    $'
downtab db 13,10,'$'
pleaseuse db 13,10,'                                  PLEASE USE:                         $'
pleaseuse1 db 13,10,'                   _____                              _____           $'
pleaseuse2 db 13,10,'                  |     |                            |     |          $'
pleaseuse3 db 13,10,'                  |  <  |                            |  >  |          $'
pleaseuse4 db 13,10,'                  ||   ||                            ||   ||          $'
pleaseuse5 db 13,10,'                            TO MOVE THE PLAYER                      $'
pressanykey db 13,10,'            PLEASE PRESS P KEYBOARD KEY TO START MOVING THE METEOR              $'

youwin1 db 13,10,'            ___   __  ____   _   _  _        _  ____  ___ __   _  $'
youwin2 db 13,10,'             \ \ / / / __ \ | | | | \\      // / __ \ | \\| | | | $'
youwin3 db 13,10,'              \ V / | |  | || | | |  \\    // | |  | || |\\ | |_| $'
youwin4 db 13,10,'               | |  | |  | || |_| |   \\/\//  | |  | || | \ |  _  $'
youwin5 db 13,10,'               |_|   \_\/_/ |_____|    \/\/    \_\/_/ |_| |_| |_| $'
goodjob db 13,10,'                       GOOD JOB! YOU WON THE GAME!                       $'
presstoend db 13,10,'                         PRESS ANY KEY TO EXIT                        $'

gameover1 db 13,10,' ______    _   ___  __ _______    ____  _      _ ______ _____   _   $'
gameover2 db 13,10,' |  ___|  /_\  |  \/  || |____|  / __ \ \\    //| |____||  __| | |  $'
gameover3 db 13,10,' | | __  // \\ | |\/| || |___   | |  | | \\  // | |___  | |\\  |_|  $'
gameover4 db 13,10,' | |_| |/ --- \| |  | || |___|  | |  | |  \\//  | |___| | | \\  _   $'
gameover5 db 13,10,' |_____||_/ \_||_|  |_||_|_____  \_\/_/    \/   |_|_____|_| || |_|  $'

bx_saver dw ?
rnd db ?

x_ship db 19
y_ship db 13
color_ship db 14

x_meteor db 29
y_meteor db 2
color_meteor db 11  ; Light Cyan

x_meteor2 db 9
y_meteor2 db 2
color_meteor2 db 12  ; Light Red

x_dollar_meteor db 19
y_dollar_meteor db 2  

color_dollar_meteor db 14 ; Yellow
dollar_count db 0
dollar_msg db 'Dollars: 0$'

.CODE
proc DrawDollarMeteor
    ;Draw dollar-meteor in cursor position
    mov ah, 9
    mov al, '$'      ; AL = character to display
    mov bh, 0h      
    mov bl, [color_dollar_meteor]  ; BL = Foreground color of the dollar-meteor
    mov cx, 1  ; number of times to write character
    int 10h      ; BIOS -> show the character
    ret
endp DrawDollarMeteor

proc Cursor_location_dollar_meteor
    ;Place the cursor on the screen at the dollar-meteor's location
    mov bh, 0
    mov dh, [y_dollar_meteor]  ; DH = row
    mov dl, [x_dollar_meteor]  ; DL = column
    mov ah, 2
    int 10h
    ret
endp Cursor_location_dollar_meteor

proc DrawDollarCount
    ;Draw the dollar count at the top-left corner
    mov ah, 2
    mov bh, 0
    mov dh, 0    ; Row
    mov dl, 0    ; Column
    int 10h

    mov ah, 9
    lea dx, dollar_msg
    int 21h
    ret
endp DrawDollarCount

proc update_dollar_count
    ;Update dollar count message
    mov al, [dollar_count]
    add al, '0'
    mov dollar_msg+9, al ; Update the number in the message
    call DrawDollarCount
    ret
endp update_dollar_count

proc DrawMeteor2
    ; Draw player in cursor position
    mov ah, 9
    mov al, 42      ;AL = character to display
    mov bh, 0h	
    mov bl, [color_meteor2]  ; BL =  Foreground
    mov cx, 1  ; number of times to write character
    int 10h      ; BIOS -> show the character
    ret
endp DrawMeteor2

proc Cursor_location_meteor2
    ; Place the cursor on the screen at the new meteor's location
    mov bh, 0
    mov dh, [y_meteor2] ; Row
    mov dl, [x_meteor2] ; Column
    mov ah, 2
    int 10h
    ret
endp Cursor_location_meteor2

proc DrawMeteor
    ; draw player in cursor position
    mov ah, 9
    mov al, 42      ;AL = character to display
    mov bh, 0h	 ;BH=Page
    mov bl, [color_meteor]  ; BL =  Foreground
    mov cx, 1  ; number of times to write character
    int 10h      ; BIOS -> show the character
    ret
endp DrawMeteor

proc Cursor_location_meteor
    ; Place the cursor on the screen
    mov bh, 0
    mov dh, [y_meteor]  ; Row
    mov dl, [x_meteor] ; Column
    mov ah, 2
    int 10h
    ret
endp Cursor_location_meteor

proc delay
    pusha
    mov cx, 00h   ; High Word
    mov dx, 00h   ; Low Word
    mov ah, 86h   ; Wait
    int 15h
    popa
    ret
endp delay

proc random
    ; Generates a random number and keeps it in rnd
    pusha
    ; Get the system timer tick count
    mov ah, 00h
    int 1Ah
    ; Use the lower byte of the timer as a seed
    mov al, dl
    xor ax, [bx_saver]
    add [bx_saver], ax
    ; Generate a number within the range 0-38
    and al, 27h
    mov [rnd], al
    popa
    ret
endp random

proc RightKeyPress
    pusha
    mov [color_ship], 0   ; black color
    call Cursor_location_ship
    call DrawShip   ; draw black character
    add [x_ship], 1
    mov [color_ship], 13
    call Cursor_location_ship   ; move to new location
    call DrawShip   ; Draw color character
    popa
    ret
endp RightKeyPress

proc LeftKeyPress
    pusha
    mov [color_ship], 0   ; black color
    call Cursor_location_ship
    call DrawShip   ; draw black character
    sub [x_ship], 1
    mov [color_ship], 13
    call Cursor_location_ship   ; move to new location
    call DrawShip   ; Draw color character
    popa
    ret
endp LeftKeyPress

proc DrawShip
    ; Draw ship in cursor position
    mov ah, 9
    mov al, 2      ; character to display
    mov bh, 0h      ; BH=Page
    mov bl, [color_ship]  ; BL = Foreground color of the player
    mov cx, 1  ; number of times to write character
    int 10h      ; BIOS -> show the character
    ret
endp DrawShip

proc Cursor_location_ship
    ; Place the cursor on the screen at the player's location
    mov bh, 0
    mov dh, [y_ship]  ; DH = row
    mov dl, [x_ship]  ; DL = column
    mov ah, 2
    int 10h
    ret
endp Cursor_location_ship

homescreen proc     
    ; Clear the screen
    mov ah, 00h
    mov al, 03h
    int 10h
    
    mov ah, 9
    lea dx, homescreen1
    int 21h
    lea dx, homescreen2
    int 21h
    lea dx, homescreen3
    int 21h
    lea dx, homescreen4
    int 21h
    lea dx, homescreen5
    int 21h
    lea dx, downtab
    int 21h
    lea dx, downtab
    int 21h
    lea dx, namepresentation
    int 21h
    lea dx, downtab
    int 21h
    lea dx, lines
    int 21h
    lea dx, downtab
    int 21h
    lea dx, pleaseuse
    int 21h
    lea dx, downtab
    int 21h
    lea dx, pleaseuse1
    int 21h
    lea dx, pleaseuse2
    int 21h
    lea dx, pleaseuse3
    int 21h
    lea dx, pleaseuse4
    int 21h
    lea dx, downtab
    int 21h
    lea dx, pleaseuse5
    int 21h
    lea dx, downtab
    int 21h
    lea dx, downtab
    int 21h
    lea dx, pressanykey
    int 21h
    ret
homescreen endp

winscreen proc   
    ; Clear the screen
    mov ah, 00h
    mov al, 03h
    int 10h
    
    mov ah, 00h
    mov al, 03h
    int 10h
    mov ah, 09h
    mov dx, offset youwin1
    int 21h
    mov dx, offset youwin2
    int 21h
    mov dx, offset youwin3
    int 21h
    mov dx, offset youwin4
    int 21h
    mov dx, offset youwin5
    int 21h
    mov dx, offset downtab
    int 21h
    mov dx, offset downtab
    int 21h
    mov dx, offset goodjob
    int 21h
    mov dx, offset presstoend
    int 21h
    ret
winscreen endp

gameoverscreen proc     
    ; Clear the screen
    mov ah, 00h
    mov al, 03h
    int 10h
    
    mov ah, 9
    lea dx, gameover1
    int 21h
    lea dx, gameover2
    int 21h
    lea dx, gameover3
    int 21h
    lea dx, gameover4
    int 21h
    lea dx, gameover5
    int 21h
    lea dx, downtab
    int 21h
    lea dx, presstoend
    int 21h
    ret
gameoverscreen endp
;-----------------------------------------------------------------------------------
; Set up data segment and display homescreen
start:
    mov ax, @data
    mov ds, ax        
    call homescreen

; Wait for user input, except when the input is null
homescreenwait:
    mov ah, 00
    int 16h
    cmp al, 0
    je homescreenwait

; Set the video mode to 13h (320x200 256-color graphics)
    mov al, 13h
    mov ah, 0
    int 10h

; Set the cursor location for the first meteor
    call Cursor_location_meteor

; Draw the first meteor
    call DrawMeteor

; Set the cursor location for the second meteor
    call Cursor_location_meteor2

; Draw the second meteor
    call DrawMeteor2

; Set the cursor location for the dollar meteor
    call Cursor_location_dollar_meteor

; Draw the dollar meteor
    call DrawDollarMeteor

; Update the dollar count
    call update_dollar_count

WaitForCharacter:
; Wait for user input
    mov ah, 0h
    int 16h

; Check if the user pressed the 'escape' key
    cmp al, 27
    je TheEnd

; Check if the user pressed the 'p' key
    cmp al, 'p'
    je Move
    jmp WaitForCharacter

Move:
; Set the color of the first meteor to the background color
    mov [color_meteor], 0

; Set the cursor location for the first meteor
    call Cursor_location_meteor

; Draw the first meteor
    call DrawMeteor

; Set the color of the second meteor to the background color
    mov [color_meteor2], 0

; Set the cursor location for the second meteor
    call Cursor_location_meteor2

; Draw the second meteor
    call DrawMeteor2

; Set the color of the dollar meteor to the background color
    mov [color_dollar_meteor], 0

; Set the cursor location for the dollar meteor
    call Cursor_location_dollar_meteor

; Draw the dollar meteor
    call DrawDollarMeteor

; Increment the y-coordinate of the first meteor
    inc [y_meteor]

; Check if the first meteor has reached the bottom of the screen
    cmp [y_meteor], 25
    je changePlace

; Increment the y-coordinate of the second meteor
    inc [y_meteor2]

; Check if the second meteor has reached the bottom of the screen
    cmp [y_meteor2], 25
    je changePlace2

; Increment the y-coordinate of the dollar meteor
    inc [y_dollar_meteor]

; Check if the dollar meteor has reached the bottom of the screen
    cmp [y_dollar_meteor], 25
    je changePlaceDollar

; Set the cursor location to the current position of the first meteor
    call Cursor_location_meteor

; Get the character at the current cursor position
    mov bh, 0h
    mov ah, 08h
    int 10h

; Check if the character is the 'delete' character (indicating the edge of the screen)
    cmp al, 127
    je TheEnd

; Set the cursor location to the current position of the second meteor
    call Cursor_location_meteor2

; Get the character at the current cursor position
    mov bh, 0h
    mov ah, 08h
    int 10h

; Check if the character is the 'delete' character (indicating the edge of the screen)
    cmp al, 127
    je TheEnd

; Set the cursor location to the current position of the dollar meteor
    call Cursor_location_dollar_meteor; Get the character at the current cursor position
    mov bh, 0h
    mov ah, 08h
    int 10h

; Check if the character is the 'delete' character (indicating the edge of the screen)
    cmp al, 127
    je TheEnd

; Check for collision with the ship
    mov ah, [y_ship]
    cmp ah, [y_meteor]
    jne noCollision
    mov ah, [x_ship]
    cmp ah, [x_meteor]
    jne noCollision
    jmp TheEnd

noCollision:
; Check for collision with the ship
    mov ah, [y_ship]
    cmp ah, [y_meteor2]
    jne noCollision2
    mov ah, [x_ship]
    cmp ah, [x_meteor2]
    jne noCollision2
    jmp TheEnd

noCollision2:
; Check for collision with the ship
    mov ah, [y_ship]
    cmp ah, [y_dollar_meteor]
    jne noCollision3
    mov ah, [x_ship]
    cmp ah, [x_dollar_meteor]
    jne noCollision3

; Dollar meteor collected
    inc [dollar_count]
    call update_dollar_count
    cmp [dollar_count], 3
    je WinGame

    call changePlaceDollar
    jmp Move

noCollision3:
; Set the cursor location to the current position of the first meteor
    call Cursor_location_meteor

; Set the color of the first meteor to yellow
    mov [color_meteor], 11

; Draw the first meteor
    call DrawMeteor

; Set the cursor location to the current position of the second meteor
    call Cursor_location_meteor2

; Set the color of the second meteor to cyan
    mov [color_meteor2], 12

; Draw the second meteor
    call DrawMeteor2

; Set the cursor location to the current position of the dollar meteor
    call Cursor_location_dollar_meteor

; Set the color of the dollar meteor to yellow
    mov [color_dollar_meteor], 14

; Draw the dollar meteor
    call DrawDollarMeteor

; Set the cursor location to the current position of the ship
    call Cursor_location_ship

; Draw the ship
    call DrawShip

; Check and redraw the dollar counter if needed
    call update_dollar_count

; Check for user input
    mov ah, 1h
    int 16h
    jz noKey

; Get the user input
    mov ah, 0h
    int 16h

; Check if the user pressed the 'escape' key
    cmp al, 27
    je theEnd

; Check if the user pressed the right arrow key
    cmp ah, 4dh
    je RightKey

; Check if the user pressed the left arrow key
    cmp ah, 4bh
    je LeftKey

noKey:
; Delay for a short period of time
    call delay
    jmp Move

RightKey:
; Handle the right arrow key press
    call RightKeyPress
    jmp Move

LeftKey:
; Handle the left arrow key press
    call LeftKeyPress
    jmp Move

changePlace:
; Change the position of the first meteor
    call random
    mov al, [rnd]
    mov [x_meteor], al
    mov [y_meteor], 2
    jmp Move

changePlace2:
; Change the position of the second meteor
    call random
    mov al, [rnd]
    mov [x_meteor2], al
    mov [y_meteor2], 2
    jmp Move

changePlaceDollar:
; Change the position of the dollar meteor
    call random
    mov al, [rnd]
    mov [x_dollar_meteor], al
    mov [y_dollar_meteor], 2
    jmp Move

WinGame:
; Display the win screen
    call winscreen
    mov ax, 4ch
    int 21h

TheEnd:
; Display the game over screen
    call gameoverscreen
    mov ax, 4ch
    int 21h

END start
