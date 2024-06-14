#MeteorGame

## **assessment in computer science - portfolio**
## **Explanation of the project - The Meteor game**
The game was developed in the 8086 assembly environment using the emu8086 software.
This game is for one player at a time.
Average run time per game session: 4 minutes.
Type of game: Simple old style arcade game.
the player needs to try to to collect 3 gold coins while avioding collision with foreign objects (meteors).
The code draws the screen and all entities on it through calls to the BIOS.
For example, the BIOS code used here makes it possible to print characters on the screen, determine the position of the cursor and wait for input from the user.
When running the code, the meteors and the dollar move down continuously,
And the player has to move his ship left and right to avoid colliding with the meteors.
When he grabs a coin, the number of coins he has collected is added to the Dollar counter and displayed on the left edge of the screen.

## **Manual**
## Login screen:
The user must press any key on the keyboard.
the screen is showing a briefed manual to the game.
![image](https://github.com/baraksu/MeteorGame/assets/154760489/8b922270-ca8e-4091-a66e-86b1d306e453)

## The game start screen before running:
The user can press escape key any time to end the game.
The user must press the P to start the game.
If the user wishes to end the running of the program at any stage, he must press the escape key.
![image](https://github.com/baraksu/MeteorGame/assets/154760489/2187aa82-d18e-4520-88d0-acb6f1d9a314)

## The game screen while running:
The user should use <> keys to move the spaceship right and left.
the user should make contact with 3 dollars in order to win.
![image](https://github.com/baraksu/MeteorGame/assets/154760489/ba9c761c-8f75-4646-a878-5937d733f6b7)

## The game screen when the player contacts a dollar
The dollar counter increases by one every time the player contacts it.
in the next run of the move loop, the dollar will appear in a random place.
![image](https://github.com/baraksu/MeteorGame/assets/154760489/a50ac468-0d25-4056-9ead-0f2e62962cf8)

## **win screen**
## The game screen when the player contacts 3 dollar
![image](https://github.com/baraksu/MeteorGame/assets/154760489/9dc8bc50-1860-4337-810f-bbac8e18376f)
