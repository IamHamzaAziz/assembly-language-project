# Guessing Game (x86 Assembly - EMU8086)

This is a console-based Guessing Game developed in x86 Assembly language for the EMU8086 emulator. Players have to guess a randomly generated number within a specified range and are given a limited number of chances.

---

## Features

* **Random Number Generation:** The game generates a random number between 0 and 9 for the player to guess.
* **Three Chances:** Players are given 3 attempts to guess the correct number.
* **Feedback:** Provides immediate feedback whether the guess is right or wrong.
* **Play Again Option:** After each round, players can choose to play again or quit.

---

## How to Play

1.  The game will present a heading and initial instructions.
2.  You will be prompted to "GUESS THE NUMBER:".
3.  Enter a single digit (0-9) and press Enter.
4.  The game will tell you if your guess was "RIGHT GUESS" or "WRONG GUESS".
5.  If wrong, you'll be prompted to "TRY AGAIN" for your next chance.
6.  You have 3 chances. If you fail to guess the number within these chances, the correct number will be revealed.
7.  After each round (win or lose), you'll be asked "WANT TO PLAY AGAIN (Y FOR YES)?". Press 'Y' (or 'y') to play another round, or any other key to exit.

---

## How to Run (using EMU8086)

1.  **Open EMU8086**: Launch the EMU8086 emulator.
2.  **Load the Code**:
    * Click on "File" -> "Open".
    * Navigate to the directory where you saved the assembly code (`.asm` file).
    * Select the file and click "Open".
3.  **Compile/Emulate**:
    * Click the "Emulate" button (usually a green triangle icon) or press `F5`.
    * This will assemble the code and open the emulator window.
4.  **Run**:
    * In the emulator window, click the "Run" button (or press `F6`).
    * The game will start in the console window.

---

## Technical Details (Assembly Language)

### .MODEL SMALL

This directive specifies the **small memory model**, meaning the code and data segments are each limited to 64KB. This is suitable for smaller programs like this guessing game.

### .STACK 100H

Reserves 100H (256 decimal) bytes for the **stack**. The stack is used for temporary data storage and for managing procedure calls and returns.

### .DATA

This segment defines the **data** used by the program, including:

* **`HEADING`**: The welcome message for the game.
* **`M1`, `M2`, `M3`**: Instructional messages for the player.
* **`LINE`**: The prompt for the user to enter their guess.
* **`RIGHTGUESS`**: Message displayed on a correct guess.
* **`WRONGGUESS`**: Message displayed on an incorrect guess.
* **`TRYAGAIN`**: Message encouraging the player to try again.
* **`AGAIN`**: Prompt to ask the user if they want to play again.
* **`NUMBER`**: Prefix for displaying the correct number if the player fails.

### .CODE

This segment contains the **executable instructions** of the program.

* **`MAIN PROC`**: The main procedure where program execution begins.
    * **Data Segment Initialization**: `MOV AX, @DATA` and `MOV DS, AX` are used to initialize the `DS` (Data Segment) register with the address of the data segment, allowing the program to access the defined variables.
    * **Displaying Messages**: Uses `MOV AH, 9` and `INT 21H` (DOS function to display a string) to print the various messages defined in the `.DATA` segment. `LEA DX, variable_name` loads the effective address of the string into `DX`.
    * **`NEWLINE` Procedure Call**: `CALL NEWLINE` is used extensively to move the cursor to the next line for better readability.
    * **`REPEAT` Label**: This is the entry point for each new game round.
    * **Random Number Generation**:
        * `MOV AH, 2CH` and `INT 21H` use the **Get System Time** DOS function. The `DL` register will contain hundredths of seconds.
        * `MOV AL, DL`, `MOV AH, 0`, `MOV CL, 20`, `DIV CL`: The hundredths of seconds value (`DL`) is used as a seed. It's moved to `AL`, `AH` is cleared, and then divided by 20. The remainder (`AH`) would be a more suitable random number, but here the quotient (`AL`) is used and then `INC BL` ensures the number is not 0 (making the range 1-19).
        * `MOV BL, AL` and `INC BL`: Stores the generated random number in `BL`.
    * **Guessing Loop (`L1`)**:
        * `MOV CX, 3`: Initializes `CX` to 3, representing the 3 chances the player gets.
        * **User Input**: `MOV AH, 1` and `INT 21H` read a single character from the keyboard. The input character's ASCII value is stored in `AL`.
        * **ASCII to Numeric Conversion**: `SUB AL, 48` converts the ASCII character of the digit (e.g., '0' is 48) into its corresponding numerical value.
        * **Comparison**: `CMP BL, AL` compares the generated random number (`BL`) with the user's guessed number (`AL`).
        * **Conditional Jump (`JE RIGHT`)**: If the numbers are equal, the program jumps to the `RIGHT` label.
        * **Wrong Guess Handling**: If the guess is wrong, `WRONGGUESS` and `TRYAGAIN` messages are displayed.
        * **`LOOP L1`**: Decrements `CX` and jumps back to `L1` if `CX` is not zero.
    * **Displaying Correct Number (if failed)**: If the loop finishes without a correct guess, the `NUMBER` message is displayed, and the random number (`BL`) is converted back to its ASCII character (`ADD DL, 48`) and printed using `MOV AH, 2` and `INT 21H`.
    * **`RIGHT` Label**: Handles the case where the user guesses correctly, displaying `RIGHTGUESS`.
    * **`WANNA` Label**: Prompts the user to play again.
        * **User Input for Play Again**: Reads a character.
        * **Comparison for 'Y'**: `CMP AL, 89` compares the input with the ASCII value of 'Y'.
        * **`JE REPEAT`**: If 'Y' is pressed, the program jumps back to the `REPEAT` label to start a new game.
* **`MAIN ENDP`**: Marks the end of the `MAIN` procedure.

### `NEWLINE PROC`

This is a reusable **procedure** to move the cursor to the beginning of the next line.

* `MOV DL, 10` and `INT 21H`: Prints the **Line Feed (LF)** character (ASCII 10), which moves the cursor down one line.
* `MOV DL, 13` and `INT 21H`: Prints the **Carriage Return (CR)** character (ASCII 13), which moves the cursor to the beginning of the current line.
* `RET`: Returns control to the calling procedure.

### `NEWLINE ENDP`

Marks the end of the `NEWLINE` procedure.
