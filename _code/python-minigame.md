---
title: "My Python Minigame"
excerpt: "Just a simple noughts and crosses game."
header:
  teaser: python.png
sidebar:
  - title: "My Python Minigame"
    image: python.png
    text: "Just a simple noughts and crosses game written in Python 2.7."
comments: true
---

## TresEnRaya

I've always been curious as to the process of designing games using code, so while I was learning Python I decided to try out a simple one!

"Noughts and crosses" or "tic tac toe" is a fun and quick game to play. It is not perfect, and works only on the terminal. But it does it's job. Try it out for yourself! There is barely any commenting as the names of the functions are quite self-explanatory. For a better experience I would recommend running it in iPython or jupyter notebook.

```python
#!/usr/bin/python

import random
from __future__ import print_function
from IPython.display import clear_output

def display_board(board):
    clear_output()
    print('   |   |')
    print(' ' + board[7] + ' | ' + board[8] + ' | ' + board[9])
    print('   |   |')
    print('-----------')
    print('   |   |')
    print(' ' + board[4] + ' | ' + board[5] + ' | ' + board[6])
    print('   |   |')
    print('-----------')
    print('   |   |')
    print(' ' + board[1] + ' | ' + board[2] + ' | ' + board[3])
    print('   |   |')

def player_input():
    marker = ''
    while not (marker == 'O' or marker == 'X'):
        marker = raw_input('Player 1: do you want to be X or O? ').upper()
        # the .upper() is to make the input (X or O) upper case
        
    if marker == 'X':
        # return tuple, which is like a list but can't be changed
        return ('X','O')
    else:
        return ('O','X')

def place_marker(board, marker, position):
    board[position] = marker

def win_check(board,mark):
    
    return ((board[7] == mark and board[8] == mark and board[9] == mark) or
    (board[4] == mark and board[5] == mark and board[6] == mark) or
    (board[1] == mark and board[2] == mark and board[3] == mark) or
    (board[7] == mark and board[4] == mark and board[1] == mark) or
    (board[8] == mark and board[5] == mark and board[2] == mark) or
    (board[9] == mark and board[6] == mark and board[3] == mark) or
    (board[7] == mark and board[5] == mark and board[3] == mark) or
    (board[9] == mark and board[5] == mark and board[1] == mark))

def choose_first():
    if random.randint(0,1) == 0:
        return 'Player 1'
    else:
        return 'Player 2'

def space_check(board, position):
    return board[position] == ' '

def full_board_check(board):
    
    for i in range(1,10): 
    # up to 10, as 10 is not included
        if space_check(board,i):
            return False
            # as board isn't full yet
    return True

def player_choice(board):
    
    position = ' '
    while position not in '1 2 3 4 5 6 7 8 9'.split() or not space_check(board,int(position)):
        position = raw_input('Choose next position: (1-9)')
        
    return int(position)

def replay():
    return raw_input('New round?').lower().startswith('y')

print ("Welcome to TRES EN RAYA!")

# continually runs the game until something is False:
# bellow is the actual "motor" of the game, a big while loop!
while True:
    
    theBoard = [' ']*10
    # player input
    player1_marker, player2_marker = player_input()
    turn = choose_first()
    print(turn + 'will go first!')
    # game_on will be False if its a tie or someone won
    game_on = True
    while game_on:

        if turn == "Player 1":
            # bellow will print board which is the empty list [' ']*10
            display_board(theBoard)
            # bellow set position to the player choice
            position = player_choice(theBoard)
            # bellow we put the player 1 input (either X or O) in the board
            place_marker(theBoard,player1_marker,position)
            
            if win_check(theBoard,player1_marker):
                display_board(theBoard)
                print('Congratulations, Player 1 WON!')
                game_on = False
            # to check if there is a tie    
            else:
                if full_board_check(theBoard):
                    display_board(theBoard)
                    print('Draw!')
                    break
                else:
                    turn = "Player 2"
                
        else:
            # for player 2's turn
            # bellow will print board which is the empty list [' ']*10
            display_board(theBoard)
            # bellow set position to the player choice
            position = player_choice(theBoard)
            # bellow we put the player 1 input (either X or O) in the board
            place_marker(theBoard,player2_marker,position)
            
            if win_check(theBoard,player2_marker):
                display_board(theBoard)
                print('Congratulations, Player 2 WON!')
                game_on = False
            # to check if there is a tie    
            else:
                if full_board_check(theBoard):
                    display_board(theBoard)
                    print('Draw!')
                    break
                else:
                    turn = "Player 1"
                    
    if not replay():
        break
```

What's next? Maybe hangman! However, I don't think it's worth developing any games using Python. C# is the language for that!

Be sure to check out my [Github profile](https://github.com/nickpettican) for more on code.

Gracias <i class="fa fa-hand-peace-o"></i>!
