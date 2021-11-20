## <h2>Description of the application (the features):

1.	Dead table: At the right side of the game window there is a table that calculate the number of beaten pieces.

2.	Last move table: at the right side of the game window above the dead table there is a table that show the last move from where to where and which piece moved.

3.	Music: we add calm music that you can listen during the game you can pause it from specified button 

4.	Warning message: there is a message that appear at the middle of the window tell you this move is not possible for this piece or this move will make check, or it won’t save your king from current check.

5.	Possible moves: the game show you that all possible moves you can play to each piece you pick and that the shown possible moves do not cause check to your king.

6.	Possible moves: if there is check, the game will show you only all the possible moves to protect the king.

7.	GUI: we used GUI in our game by using library <SDL>

8.	Menu: we put menu contains of (New game: to start a new game – Load game: to load existing game–Exit game)

9.	Saved games: If you want to exit the game, there is a window appear tell you if you want to save the game and continue it in any time later or if you want to exit without saving or you want to continue playing.

10.	Load game: this part is the store of your last game played if the user wants to save if there isn’t any saved game there is a message appears tell you that.

11.	There are 2 buttons for undo and redo it can undo to the first of the game and you cannot redo if you played a game.

12.	There is an option to start a new game after you finish your game, or you can exit the game.





## <h2>Description of all data structures:

We define a lot of arrays:

1-There are 2 arrays ( game_pieces ) one for white pieces and the another for black pieces each one is contain of 16 elements each element is a structure for a piece in the game 

each element in these arrays define a piece movement in the game it has valid.x and valid.y and present.x and present.y if the player mad a wrong move the piece return to it 

valid co-ordinates and there is the name of the piece to check its move by the name

2-There are 6 arrays ( buttons_shape ) We define in it all information for this button as ( the two coordinates,  the height of this button, the width of this button ) and this 

structure for menu buttons (New game – load game – User manual – Exit game ) and game buttons (White king’s turn – black king’s turn -Undo – Redo) and promotion buttons ( Choose 

Promotion White King - Choose Promotion Black King – Queen – Knight – Rook – Bishop ) and exit buttons ( Are you sure, Sir - Continue Playing - Save and Exit - Exit Without 

Saving ) and dead table buttons ( the number of beaten pieces for each shape)

3-There is (done_game_array ) array and it used for store every right move in the game each element is a structure define on it all the information about this done move that 

each element in the array has ( the cooardinates of the last position at the game board and the game window, the cooardinates of the present coordinates at the game board and 

the game window, the value on the board game after and before the move, if this move is castling or not, if this move is promotion, current piece in the window, if this is first 

move or not, the beaten piece in this round). 

4-There are 2 matrices 8*8 ( board_game – board_main ) board game this is the most important data structure in the game we 8 and define on it the white pieces as (-1) and the 

black pieces as (1) and the empty place as (0) and it is the most important part in the code because we make all functions using it and board main it defined at the new game






## <h2>Main functions: 

**( menu_setup – menu_process_input – render_menu )**

These functions are in charge of appearing the menu with 4 labels and take the input from the user and render the menu.

**( white_player_input – black_player_input )**

These functions are in charge of taking orders from user from 2 players , check if the move is right or wrong then update the game states and change the dead table count and 

pieces places and there is the undo and redo orders in it if the player want to undo or redo the function reset the data of the pieces according to the last move then it 


controls the promotion if the pawn ready to promote.

**( check_possible_moves )**

This function is in charge of checking the game over or not by check mate or stalemate as if the king was in check it can be checkmate or if it wasn’t in check it can be 

stalemate. if there are possible moves to protect it the game resumes. And this function show the yellow mark of the possible moves for each piece you pick in the rendering mode 

but only shows the valid moves which don’t check your own king.

**( check_vaildity )**

This function is in charge of checking if the move you played is right or not by checking the name of the piece and using the game_board matrix to see its movement it has 3 uses 

one to check the validity of the move in the game. if it is valid it changes the game_board value according to it and change the beaten pieces places to -100 second use to check 

the possible moves then it changes the game_board values only but very fast and bring it to its right values third use to check if the king in checkmate or not without changing 

the game_board values.

**( check_mate_func )**

This function is in charge of check if the king in danger or not by check if any one of the 16 enemy pieces threaten the king by using (check_validity) function and check if the 

game over or not even it checkmate or stalemate.

**( return_board )**

This function is responsible to return the game board to the last case if the move isn’t right.

( setup_promotion – promotion_input – render_promotion ) 

These functions are in charge of appearing the promotion menu and taking the input from the user then changing the promoted piece name and texture according to the player 

choice.

**(setup_exit_menu – input_exit_menu – render_exit_menu)**

These functions are in charge of appearing the exit menu and take input from player if he wants to exit with save or not or continue playing.

![Image of Yaktocat](https://github.com/MohamedMamdouh18/basic-games/blob/main/Chess%20Game/Capture1.PNG)
![Image of Yaktocat](https://github.com/MohamedMamdouh18/basic-games/blob/main/Chess%20Game/Capture2.PNG)

