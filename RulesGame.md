# Game Rules “GameSnake”
1.1. __Game Field__ – The game field is a grid (e.g., NxN).
1.2. __Game Start__ – The game starts from a specific starting position, presumably         in the center, and movement is controlled using the WASD keys.
1.3. __Snake Growth__ – The snake grows in size each time it eats an apple.
1.4. __Objective__ – The goal of the game is to eat apples that appear in random locations.
1.5. __Game Over Conditions__ – The game ends if the player loses control and crashes into a wall with the snake's head or if the snake collides with itself.
# Protocol organization (PC ↔ STM)
2.1. __PC__ – The PC acts as a controller, allowing the user to send movement commands to the STM32.
2.2. __STM32__ – The STM32 handles the game logic, processes movement, and sends the game state to the PC.
2.3. __Communication__ – UART is used for communication.
# Error Handling
### 3.Error Processing
Error handling will be performed using CRC16. (0x01 – Unknown command)
### 4.Game Process Recovery
### 4.1. Sending an Error Code with Recovery
In this case, we can implement an error code with recovery when the STM32 restarts or disconnects. Error code 0x03 – Emergency restart
### 5.Invalid Checksum Handling
Handling with CRC16
All checksum verification will be done using CRC16.
### 6. Loss of Coordinate Packet
### 6.1. Request for Resending
Send a request for retransmission (0x20).
### 6.2. STM32 must resend the last known position.
### 7. Behavior When the Game Stops
The PC must send the "Stop Game" command. STM32 stops the snake's movement and pauses the game.
0x03 – Game Over
The game stops unexpectedly (power failure, software error). An error code is sent using CRC16.
Detect the absence of a response and resend the Start Game (0x01) command. An error code is sent using CRC16.
### 7.1. Handling Delays
If commands are received with a very small interval, we implement a mechanism where, if no information is received for 1 second, the game will pause.
### 8. STM32 Behavior in Case of an Erroneous Game Stop
If STM32 unexpectedly receives a game termination command (specific commands to be defined later):
It must check the last game state (e.g., verify if there is a repeated command).
If the command was received by mistake, STM32 sends a confirmation request to the PC.
If the PC does not confirm within 1 second, the game continues.
If the PC resends the termination command, STM32 ends the game.

