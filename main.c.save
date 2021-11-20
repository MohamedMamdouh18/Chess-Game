#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <Windows.h>
#include <SDL.h>
#include <SDL_image.h>
#include <SDL_ttf.h>
#include <SDL_mixer.h>

#define ON 1
#define OFF 0
#define block_size 75
#define window_width 975
#define window_height 750
#define FPS 240
#define frame_time 1000/FPS
#define menu_labels 3
#define pieces 32
#define game_labels 5
#define promotion_labels 6
#define exit_labels 4
#define dead_table 10
#define game_over_labels 5

typedef struct game_pieces
{
    int present_x ;
    int present_y ;
    int valid_x ;
    int valid_y ;
    int first_move ;
    char *name ;
}game_pieces ;
game_pieces white_pieces[pieces/2] ;
game_pieces black_pieces[pieces/2] ;

typedef struct buttons_shape
{
    int x ;
    int y ;
    int height ;
    int width ;
}buttons_shape;
buttons_shape menu_label_struct[menu_labels] ;
buttons_shape game_label_struct[game_labels] ;
buttons_shape promotion_label_struct[promotion_labels] ;
buttons_shape exit_label_struct[exit_labels] ;
buttons_shape dead_table_struct[dead_table] ;
buttons_shape game_over_menu_struct[game_over_labels] ;

typedef struct done_moves
{
    int from_x ;
    int from_y ;
    int to_x ;
    int to_y ;
    int board_to_x;
    int board_to_y;
    int board_from_x;
    int board_from_y;
    int board_from_value ;
    int board_to_value ;
    int castling ;
    int promotion ;
    int current_piece ;
    int first_move ;
    int eaten_piece ;
}done_moves ;
done_moves the_done_array[500] ;
int undo_moves = 0 ;
int redo_moves = 0 ;
int castling = OFF ;
int redo_promotion = OFF ;

int board_game[pieces/4][pieces/4] = {{1,1,1,1,1,1,1,1} ,
                                        {1,1,1,1,1,1,1,1} ,
                                        {0,0,0,0,0,0,0,0} ,
                                        {0,0,0,0,0,0,0,0} ,
                                        {0,0,0,0,0,0,0,0} ,
                                        {0,0,0,0,0,0,0,0} ,
                                        {-1,-1,-1,-1,-1,-1,-1,-1} ,
                                        {-1,-1,-1,-1,-1,-1,-1,-1}};

int board_main[pieces/4][pieces/4] = {{1,1,1,1,1,1,1,1} ,
                                        {1,1,1,1,1,1,1,1} ,
                                        {0,0,0,0,0,0,0,0} ,
                                        {0,0,0,0,0,0,0,0} ,
                                        {0,0,0,0,0,0,0,0} ,
                                        {0,0,0,0,0,0,0,0} ,
                                        {-1,-1,-1,-1,-1,-1,-1,-1} ,
                                        {-1,-1,-1,-1,-1,-1,-1,-1}};

int open_window() ;
void loading_images_fonts_surfaces() ;
void setup_game_labels() ;
void no_saved_setup() ;
void make_textures() ;
void load_setup() ;
void white_player_input() ;
void black_player_input() ;
void destroy_game() ;
void update() ;
void rendering() ;
int check_validity(int from_x , int from_y , int to_x , int to_y , char* name , int first_move) ;

void return_board(int board_replac[][8]) ;
int check_mate_func(game_pieces enemy_pieces[] , game_pieces the_king) ;
int check_possible_moves(game_pieces enemy_pieces[] , game_pieces ally_pieces , game_pieces ally_king) ;
int counting_dead_positions() ;

int eaten_piece ;
int checking = OFF ;
int turn = OFF ;
int check_end_game = OFF ;
int check_mate_before_moving = OFF ;
int check_mate_after_moving = OFF ;
int game_not_over = ON ;
int check_king_move = OFF ;
int not_stalemate = ON ;

void setup_promotion() ;
void render_promotion() ;
void promotion_input() ;
void destroy_promotion() ;

void setup_exit_menu() ;
void render_exit_menu() ;
void input_exit_menu() ;
void destroy_exit_menu() ;

SDL_Surface* right_move_surface ;
SDL_Texture* right_move_texture ;
void make_render_textures(int x , int y) ;

void menu_setup() ;
void menu_process_input();
void render_menu() ;
void menu_process_input();
void destroy_menu() ;

int game_states = OFF ;
int white_player = ON ;
TTF_Font* font ;
SDL_Window* window = NULL ;
SDL_Renderer* my_render = NULL ;
SDL_Surface* pieces_surfaces[12] ;
SDL_Surface* board_surface ;
SDL_Texture* pieces_textures[pieces] ;
SDL_Texture* board_texture ;
int button_down = OFF ;
int current_white_piece ;
int current_black_piece ;
int show_moves = OFF ;
SDL_Surface* game_label_surfaces[game_labels] ;
SDL_Texture* game_label_textures[game_labels] ;
SDL_Color game_label_color[game_labels] = {{255 , 255 , 255} , {0, 0 , 0} , {255 , 0 , 0} , {0 , 102 ,0}} ;
int game_label_selected[2] = {0 , 0} ;
char* game_label_names[game_labels] ={"White King's Turn" , "Black King's Turn" , "Undo" , "Redo" , "Game Over" } ;

SDL_Surface* dead_table_surface2 ;
SDL_Texture* dead_table_texture2 ;
int dead_table_count [dead_table] = {0 ,0 ,0 ,0 ,0 ,0 ,0 ,0 ,0 ,0 } ;
char dead_table_str[10] ;
SDL_Surface* dead_table_count_surface[10] ;
SDL_Texture* dead_table_count_texture[10] ;

////////////////////////////////////////////////////////////////////////////////////////////////////

//////////////////////////////////promotion variables //////////////////////////////////////////////////////

char* promotion_label_names[promotion_labels] = {"Choose Promotion" , "Choose Promotion"
                                                    , "Queen" , "Knight" , "Rook" , "Bishop"} ;
SDL_Surface* promotion_label_surfaces[promotion_labels] ;
SDL_Texture* promotion_label_textures[promotion_labels] ;
SDL_Color promotion_label_color[4] = {{255, 255 , 255},{0 , 0 , 0} , {233 , 71 , 0}} ;
int promotion_selected[4] = {0 , 0 , 0 , 0} ;
////////////////////////////////////////////////////////////////////////////////////////////////////

//////////////////////////////////menu variables //////////////////////////////////////////////////////
int menu_state = ON ;
TTF_Font* font_2 ;
SDL_Surface* menu_label_surfaces[menu_labels] ;
SDL_Texture* menu_label_textures[menu_labels] ;
SDL_Color menu_color[menu_labels] = {{0, 0 , 0},{230 , 50 , 0}} ;
int menu_label_selected[menu_labels] = {0 , 0 , 0} ;
char* labels[menu_labels] ={"New Game" , "Load Game" , "Exit Game"} ;

////////////////////////////////////////////////////////////////////////////////////////////////////

//////////////////////////////////exit menu variables //////////////////////////////////////////////////////
char* exit_label_names[exit_labels] = {"Are you sure, Sir?" , "Continue Playing" , "Save and Exit"
                                                    , "Exit Without Saving"} ;
SDL_Surface* exit_label_surfaces[exit_labels] ;
SDL_Texture* exit_label_textures[exit_labels] ;
SDL_Color exit_label_color[2] = {{255, 255 , 255} , {0 , 0 , 0}} ;
int exit_menu_state = OFF ;
int exit_label_selected[exit_labels - 1] = {0 , 0 , 0} ;
int load_game = OFF ;
////////////////////////////////////////////////////////////////////////////////////////////////////

SDL_Cursor* cursor1;
SDL_Cursor* cursor2;
int play_again = ON ;

char* board_moves [8][8] = {{"A8", "B8" , "C8" ,"D8" , "E8" , "F8" , "G8" , "H8"} ,
                                {"A7", "B7" , "C7" ,"D7" , "E7" , "F7" , "G7" , "H7"} ,
                                {"A6", "B6" , "C6" ,"D6" , "E6" , "F6" , "G6" , "H6"} ,
                                {"A5", "B5" , "C5" ,"D5" , "E5" , "F5" , "G5" , "H5"} ,
                                {"A4", "B4" , "C4" ,"D4" , "E4" , "F4" , "G4" , "H4"} ,
                                {"A3", "B3" , "C3" ,"D3" , "E3" , "F3" , "G3" , "H3"} ,
                                {"A2", "B2" , "C2" ,"D2" , "E2" , "F2" , "G2" , "H2"} ,
                                {"A1", "B1" , "C1" ,"D1" , "E1" , "F1" , "G1" , "H1"} };

SDL_Surface* from_tile_surface;
SDL_Surface* to_tile_surface;
SDL_Texture* from_tile_texture;
SDL_Texture* to_tile_texture;
SDL_Surface* from_to_table_surface ;
SDL_Texture* from_to_table_texture ;
SDL_Texture* last_piece_moved_texture;

////////////////////////////////////////////////////////////////////////////////////////////////////
//////////////////////////////////game over menu //////////////////////////////////////////////////////

int game_over_menu = ON ;
char* game_over_menu_labels[game_over_labels] ={"White King Won" , "Black King Won" , "Stalemate" , "Play Again ?" , "Exit Game"} ;
SDL_Color game_over_menu_color[5] = {{255, 255 , 255} , {0 , 0 , 0} , {160 , 160 , 160} , {0 , 204 , 0} , {255 , 255 , 0}}  ;
SDL_Surface* game_over_surfaces[game_over_labels] ;
SDL_Texture* game_over_textures[game_over_labels] ;

void game_over_menu_setup();
void input_game_over_menu() ;
void render_game_over_menu() ;
void destroy_game_over_menu() ;

int game_over_label_selected[2] = {0 , 0} ;

////////////////////////////////////////////////////////////////////////////////////////////////////
Mix_Music* music;
int music_state = ON ;
SDL_Surface* music_shape_surface[2];
SDL_Texture* music_shape_texture;


int main(int argc , char* argv[])
{
    HWND windowHandle = GetConsoleWindow();
	ShowWindow(windowHandle,SW_HIDE);

    Mix_OpenAudio(MIX_DEFAULT_FREQUENCY , MIX_DEFAULT_FORMAT , 2 , 4096) ;
    music = Mix_LoadMUS("Resources/backgroundmusic.mp3") ;
    Mix_PlayMusic(music , -1) ;
    Mix_VolumeMusic(6);


    game_states = open_window() ;
    loading_images_fonts_surfaces() ;

    menu_setup() ;
    while(menu_state)
    {
        menu_process_input();
        render_menu();
        SDL_Delay(frame_time) ;
    }

    destroy_menu() ;
    setup_game_labels() ;

    while(play_again)
    {

        for(int i = 0 ; i < 8 ; i ++)
        {
            for(int j = 0 ; j < 8 ; j ++)
            {
                board_game[i][j] = board_main[i][j] ;
            }
        }

        if(load_game)
        {
            load_setup() ;
        }
        else
        {
            no_saved_setup() ;
        }

        make_textures() ;

        cursor1 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_ARROW);
        SDL_SetCursor(cursor1);

        while(game_states)
        {
            if(white_player)
            {
                if(turn)
                {
                    checking = ON ;
                    check_mate_before_moving = check_mate_func(black_pieces , white_pieces[12]) ;
                    check_king_move = ON ;
                    if(check_mate_before_moving)
                    {
                        check_end_game = ON ;
                        for(int i = 0 ; i < 16 ; i ++)
                        {
                            game_not_over = check_possible_moves(black_pieces , white_pieces[i] , white_pieces[12]) ;
                            if(game_not_over)
                            {
                                break ;
                            }
                        }

                        check_end_game = OFF ;
                        if(!game_not_over)
                        {
                            game_states = OFF ;
                            game_over_menu = ON ;
                        }
                    }
                    else
                    {
                        check_end_game = ON ;
                        for(int i = 0 ; i < 16 ; i ++)
                        {
                            not_stalemate = check_possible_moves(black_pieces , white_pieces[i] , white_pieces[12]) ;
                            if(not_stalemate)
                            {
                                break ;
                            }
                        }

                        check_end_game = OFF ;
                        if(!not_stalemate)
                        {
                            game_states = OFF ;
                            game_over_menu = ON ;
                        }

                        not_stalemate = !counting_dead_positions() ;
                        if(!not_stalemate)
                        {
                            game_states = OFF ;
                            game_over_menu = ON ;
                        }
                    }

                    checking = OFF ;
                    check_king_move = OFF ;
                    turn = OFF ;
                }

                white_player_input();
            }
            else
            {
                if(turn)
                {
                    checking = ON ;
                    check_mate_before_moving = check_mate_func(white_pieces , black_pieces[4]) ;
                    check_king_move = ON ;

                    if(check_mate_before_moving)
                    {
                        check_end_game = ON ;
                        for(int i = 0 ; i < 16 ; i ++)
                        {
                            game_not_over = check_possible_moves(white_pieces , black_pieces[i] , black_pieces[4]) ;
                            if(game_not_over)
                            {
                                break ;
                            }
                        }
                        check_end_game = OFF ;
                        if(!game_not_over)
                        {
                            game_states = OFF ;
                            game_over_menu = ON ;
                        }
                    }
                    else
                    {
                        check_end_game = ON ;
                        for(int i = 0 ; i < 16 ; i ++)
                        {
                            not_stalemate = check_possible_moves(white_pieces , black_pieces[i] , black_pieces[4]) ;
                            if(not_stalemate)
                            {
                                break ;
                            }
                        }
                        check_end_game = OFF ;
                        if(!not_stalemate)
                        {
                            game_states = OFF ;
                            game_over_menu = ON ;
                        }

                        not_stalemate = !counting_dead_positions() ;
                        if(!not_stalemate)
                        {
                            game_states = OFF ;
                            game_over_menu = ON ;
                        }
                    }

                    check_king_move = OFF ;
                    checking = OFF ;
                    turn = OFF ;
                }

                black_player_input();
            }
            SDL_Delay(frame_time) ;
            rendering() ;
        }



        game_over_menu_setup() ;
        while(game_over_menu)
        {
            input_game_over_menu() ;
            render_game_over_menu() ;
            SDL_Delay(frame_time) ;
        }
        destroy_game_over_menu() ;
    }

    destroy_game() ;
    return 0 ;
}

int open_window()
{
    if(SDL_Init(SDL_INIT_EVERYTHING) != 0)
    {
        printf("failed intializing") ;
        return OFF ;
    }
    SDL_Init(SDL_INIT_EVERYTHING) ;
    TTF_Init() ;

    window = SDL_CreateWindow("Epic Chess Game V1.0" , SDL_WINDOWPOS_CENTERED , SDL_WINDOWPOS_CENTERED ,
                              window_width , window_height , SDL_WINDOW_OPENGL );
    if(!(window))
    {
        printf("failed opening window") ;
        return OFF ;
    }
    my_render = SDL_CreateRenderer(window , -1 , 0) ;
    if(!(my_render))
    {
        printf("failed making my_render") ;
        return OFF ;
    }
    return ON ;
}

void loading_images_fonts_surfaces()
{
    TTF_Init() ;
    IMG_Init(IMG_INIT_PNG) ;

    font = TTF_OpenFont("Resources/ITCKRIST.TTF" , 300) ;
    font_2 = TTF_OpenFont("Resources/vgaoem.fon" , 300) ;

    music_shape_surface[0] = IMG_Load("Resources/musicon.png") ;
    music_shape_surface[1] = IMG_Load("Resources/musicoff.png") ;
    music_shape_texture = SDL_CreateTextureFromSurface(my_render , music_shape_surface[0]) ;

    pieces_surfaces[0] = IMG_Load("Resources/RookBlack.png") ;
    pieces_surfaces[1] = IMG_Load("Resources/KnightBlack.png") ;
    pieces_surfaces[2] = IMG_Load("Resources/BishopBlack.png") ;
    pieces_surfaces[3] = IMG_Load("Resources/QueenBlack.png") ;
    pieces_surfaces[4] = IMG_Load("Resources/KingBlack.png") ;
    pieces_surfaces[5] = IMG_Load("Resources/PawnBlack.png") ;
    pieces_surfaces[6] = IMG_Load("Resources/PawnWhite.png") ;
    pieces_surfaces[7] = IMG_Load("Resources/RookWhite.png") ;
    pieces_surfaces[8] = IMG_Load("Resources/KnightWhite.png") ;
    pieces_surfaces[9] = IMG_Load("Resources/BishopWhite.png") ;
    pieces_surfaces[10] = IMG_Load("Resources/QueenWhite.png") ;
    pieces_surfaces[11] = IMG_Load("Resources/KingWhite.png") ;

    from_to_table_surface = IMG_Load("Resources/from_to_table.png") ;

    right_move_surface = IMG_Load("Resources/rightmove.png") ;
    dead_table_surface2 = IMG_Load("Resources/deadtable2.png") ;

    board_surface = IMG_Load("Resources/BoardChess.png") ;

    SDL_Surface *icon = IMG_Load("Resources/game_icon.png") ;
    SDL_SetWindowIcon(window, icon);
}



int counting_dead_positions()
{
    int  wbishop = 0 , bbishop = 0 , wknight = 0 , bknight = 0 , allpieces = 0 ;
    int bbishop_number , wbishop_number ;

    for(int i = 0 ; i < 16 ; i ++)
    {
        if(white_pieces[i].present_x != -100)
        {
            allpieces ++ ;
            if(!strcmp(white_pieces[i].name , "bishop"))
            {
                wbishop++ ;
                wbishop_number = i ;
            }
            else if(!strcmp(white_pieces[i].name , "knight"))
            {
                wknight++ ;
            }
        }
        if(black_pieces[i].present_x != -100)
        {
            allpieces ++ ;
            if(!strcmp(black_pieces[i].name , "bishop"))
            {
                bbishop++ ;
                bbishop_number = i ;
            }
            else if(!strcmp(black_pieces[i].name , "knight"))
            {
                bknight++ ;
            }
        }
    }

     if( (allpieces == 2  ||( allpieces == 3 &&(wbishop == 1 || bbishop == 1)) )||
         ( allpieces == 3 && (wknight == 1 || bknight == 1)) ||
          (allpieces == 4 && wbishop == 1 && bbishop == 1 && ((wbishop_number == 13 && bbishop_number == 2 )
                                                              || (wbishop_number == 10 && bbishop_number == 5)) ))
                                                              {
                                                                  return ON ;
                                                              }
    return OFF ;


}
//////////////////////////////////menu functions //////////////////////////////////////////////////////
void menu_setup()
{
    for(int i = 0 ; i < menu_labels ; i ++)
    {
        menu_label_surfaces[i] = TTF_RenderText_Solid(font , labels[i] , menu_color[0]) ;
    }
    for(int i = 0 ; i < menu_labels ; i ++)
    {
        menu_label_textures[i] = SDL_CreateTextureFromSurface(my_render , menu_label_surfaces[i]) ;
    }
    for(int i = 0 ; i < menu_labels ; i ++)
    {
        menu_label_struct[i].x =290  ;
        menu_label_struct[i].y = i * 130 + 130 ;
        menu_label_struct[i].width =  400;
        menu_label_struct[i].height =  100;
    }
}

void menu_process_input()
{
    SDL_Event event ;
    SDL_WaitEvent(&event) ;
    switch(event.type)
    {
        case SDL_QUIT :
            menu_state = OFF ;
            game_states = OFF ;
            play_again = OFF ;
        case SDL_MOUSEMOTION :
            for(int i = 0; i < menu_labels; i ++) {
                    if(event.button.x>=menu_label_struct[i].x && event.button.x<=menu_label_struct[i].x+menu_label_struct[i].width
                       && event.button.y>=menu_label_struct[i].y && event.button.y<=menu_label_struct[i].y+menu_label_struct[i].height)
                    {
                        if(!menu_label_selected[i])
                        {
                            menu_label_selected[i] = 1;
                            SDL_FreeSurface(menu_label_surfaces[i]);
                            menu_label_surfaces[i] = TTF_RenderText_Solid(font_2,labels[i],menu_color[1]);
                            menu_label_textures[i] = SDL_CreateTextureFromSurface(my_render , menu_label_surfaces[i]) ;

                            cursor2 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_HAND);
                            SDL_SetCursor(cursor2);

                        }

                    }
                    else
                    {
                        if(menu_label_selected[i])
                        {
                            menu_label_selected[i] = 0;
                            SDL_FreeSurface(menu_label_surfaces[i]);
                            menu_label_surfaces[i] = TTF_RenderText_Solid(font,labels[i],menu_color[0]);
                            menu_label_textures[i] = SDL_CreateTextureFromSurface(my_render , menu_label_surfaces[i]) ;

                            int check_cursor = OFF ;
                            for(int i = 0; i < menu_labels; i ++)
                            {
                                if(menu_label_selected[i] == 1)
                                {
                                    check_cursor = ON ;
                                }
                            }
                            if(!check_cursor )
                            {
                                cursor1 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_ARROW);
                                SDL_SetCursor(cursor1);
                            }
                        }
                    }

                }
                break;
        case SDL_MOUSEBUTTONDOWN :
                    if(event.button.x>=menu_label_struct[2].x && event.button.x<=menu_label_struct[2].x+menu_label_struct[2].width
                       && event.button.y>=menu_label_struct[2].y && event.button.y<=menu_label_struct[2].y+menu_label_struct[2].height)
                    {
                        menu_state = OFF ;
                        game_states = OFF ;
                        play_again = OFF ;
                    }
                    if(event.button.x>=menu_label_struct[0].x && event.button.x<=menu_label_struct[0].x+menu_label_struct[0].width
                       && event.button.y>=menu_label_struct[0].y && event.button.y<=menu_label_struct[0].y+menu_label_struct[0].height)
                    {
                        menu_state = OFF ;
                        game_states = ON ;
                        load_game = OFF ;
                        FILE *save_file ;
                        save_file = fopen("Resources/saved_game.bin" , "rb") ;
                        fclose(save_file) ;
                    }
                    if(event.button.x>=menu_label_struct[1].x && event.button.x<=menu_label_struct[1].x+menu_label_struct[1].width
                       && event.button.y>=menu_label_struct[1].y && event.button.y<=menu_label_struct[1].y+menu_label_struct[1].height)
                    {
                        FILE *save_file ;
                        save_file = fopen("Resources/saved_game.bin" , "rb") ;
                        if (fread(&white_pieces[0] , sizeof(white_pieces[0]) , 1 , save_file))
                        {
                            menu_state = OFF ;
                            game_states = ON ;
                            load_game = ON ;
                        }
                        else
                        {
                            SDL_ShowSimpleMessageBox(SDL_MESSAGEBOX_INFORMATION,
                                                    "No Saved Game",
                                                    "There is no saved game from before. You can start a new game, Sir",
                                                    NULL);
                        }
                        fclose(save_file) ;

                    }

                    if(event.button.x>=850 && event.button.x<=950
                       && event.button.y>=25 && event.button.y<=125)
                       {
                           if(Mix_PlayingMusic() == 1 && music_state)
                           {
                               Mix_PauseMusic() ;
                               music_shape_texture = SDL_CreateTextureFromSurface(my_render , music_shape_surface[1]) ;
                               music_state = OFF ;
                           }
                           else if(Mix_PausedMusic() == 1 && !music_state)
                           {
                               Mix_ResumeMusic() ;
                               music_shape_texture = SDL_CreateTextureFromSurface(my_render , music_shape_surface[0]) ;
                               music_state = ON ;
                           }
                       }
    }

}

void render_menu()
{
    SDL_SetRenderDrawColor(my_render , 225 , 225 , 225 , 255) ;
    SDL_RenderClear(my_render) ;

    SDL_Rect menu_label_rects[menu_labels] ;
    for(int i = 0 ; i < menu_labels ; i ++)
   {
       menu_label_rects[i].x = menu_label_struct[i].x ;
       menu_label_rects[i].y = menu_label_struct[i].y ;
       menu_label_rects[i].w = menu_label_struct[i].width ;
       menu_label_rects[i].h = menu_label_struct[i].height ;
   }

   for(int i = 0 ; i < menu_labels ; i ++)
   {
       SDL_RenderCopy(my_render , menu_label_textures[i] , NULL , &menu_label_rects[i]) ;
   }

   SDL_Rect music_shape = {850 , 25 , 100 ,100} ;
   SDL_RenderCopy(my_render , music_shape_texture , NULL , &music_shape) ;

   SDL_RenderPresent(my_render) ;
}
//////////////////////////////////////////////////////////////////////////////////////////////////////
////////////////////////////////// setup game //////////////////////////////////////////////////////

void setup_game_labels()
{
    game_label_surfaces[0] = TTF_RenderText_Solid(font , game_label_names[0] , game_label_color[0]) ;
    game_label_surfaces[1] = TTF_RenderText_Solid(font , game_label_names[1] , game_label_color[1]) ;
    game_label_surfaces[2] = TTF_RenderText_Solid(font , game_label_names[2] , game_label_color[2]) ;
    game_label_surfaces[3] = TTF_RenderText_Solid(font , game_label_names[3] , game_label_color[2]) ;
    game_label_surfaces[4] = TTF_RenderText_Solid(font_2 , game_label_names[4] , game_label_color[2]) ;

    for(int i = 0 ; i < 5 ; i ++)
    {
       dead_table_count_surface[i] = TTF_RenderText_Solid(font , dead_table_count[i] , game_label_color[0]) ;
       dead_table_count_surface[i+5] = TTF_RenderText_Solid(font , dead_table_count[i+5] , game_label_color[1]) ;
    }

    for(int i = 0 ; i < 10 ; i ++)
    {
        dead_table_count_texture[i] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[i]) ;
    }


    game_label_textures[0] = SDL_CreateTextureFromSurface(my_render , game_label_surfaces[0]) ;
    game_label_textures[1] = SDL_CreateTextureFromSurface(my_render , game_label_surfaces[1]) ;
    game_label_textures[2] = SDL_CreateTextureFromSurface(my_render , game_label_surfaces[2]) ;
    game_label_textures[3] = SDL_CreateTextureFromSurface(my_render , game_label_surfaces[3]) ;
    game_label_textures[4] = SDL_CreateTextureFromSurface(my_render , game_label_surfaces[4]) ;

    for(int i = 2 ; i < 4 ; i ++)
    {
        game_label_struct[i].x =430  ;
        game_label_struct[i].width =170  ;
        game_label_struct[i].height =60  ;

    }
    game_label_struct[0].x =0 ;
    game_label_struct[0].y =20 ;
    game_label_struct[0].width =400 ;
    game_label_struct[0].height =110  ;

    game_label_struct[1].x =0  ;
    game_label_struct[1].y =20 ;
    game_label_struct[1].width =400  ;
    game_label_struct[1].height =110  ;

    game_label_struct[4].x =0  ;
    game_label_struct[4].y =20 ;
    game_label_struct[4].width =400  ;
    game_label_struct[4].height =110  ;

    game_label_struct[2].y =10 ;
    game_label_struct[3].y =80 ;

    for(int i = 0 ; i < 5 ; i ++)
    {
        dead_table_struct[i].x = 765;
        dead_table_struct[i + dead_table/2].x = 915;
        dead_table_struct[i].y = 375 + i *block_size ;
        dead_table_struct[i + dead_table/2].y = 375 + i *block_size ;
        dead_table_struct[i].height = block_size ;
        dead_table_struct[i].width = 30 ;
        dead_table_struct[i+dead_table/2].height = block_size ;
        dead_table_struct[i+dead_table/2].width = 30 ;
    }
}
void no_saved_setup()
{

    for(int i = 0 ; i < pieces/4 ; i ++)
    {
        black_pieces[i].present_x = i * block_size ;
        black_pieces[i].present_y = block_size * 2 ;
        black_pieces[i].first_move = ON ;

        white_pieces[i].present_x = i * block_size ;
        white_pieces[i].present_y = block_size * 8 ;
        white_pieces[i].name = "wpawn" ;
        white_pieces[i].first_move = ON ;

        black_pieces[i + pieces/4].present_x = i * block_size ;
        black_pieces[i + pieces/4].present_y = block_size * 3 ;
        black_pieces[i + pieces/4].name = "bpawn" ;
        black_pieces[i + pieces/4].first_move = ON ;

        white_pieces[i + pieces/4].present_x = i * block_size ;
        white_pieces[i + pieces/4].present_y = block_size * 9 ;
        white_pieces[i + pieces/4].first_move = ON ;
    }

    black_pieces[0].name = "rook" ;
    black_pieces[1].name = "knight" ;
    black_pieces[2].name = "bishop" ;
    black_pieces[3].name = "queen" ;
    black_pieces[4].name = "king" ;
    black_pieces[5].name = "bishop" ;
    black_pieces[6].name = "knight" ;
    black_pieces[7].name = "rook" ;

    white_pieces[8].name = "rook" ;
    white_pieces[9].name = "knight" ;
    white_pieces[10].name = "bishop" ;
    white_pieces[11].name = "queen" ;
    white_pieces[12].name = "king" ;
    white_pieces[13].name = "bishop" ;
    white_pieces[14].name = "knight" ;
    white_pieces[15].name = "rook" ;
}

void load_setup()
{
            FILE *save_file ;
            save_file = fopen("Resources/saved_game.bin" , "rb") ;
            for(int i = 0 ; i < pieces/2 ; i ++)
            {
                fread(&white_pieces[i] , sizeof(white_pieces[i]) , 1 , save_file) ;
                fread(&black_pieces[i] , sizeof(black_pieces[i]) , 1 , save_file) ;
            }

            for(int i = 0 ; i < 8 ; i ++)
            {
                for(int j = 0 ; j < 8 ; j ++)
                {
                    fread(&board_game[i][j] , sizeof(board_game[i][j]) , 1 , save_file) ;
                }
            }
            fread(&white_player , sizeof(white_player) , 1 , save_file) ;

            for(int i = 0 ; i < dead_table ; i ++)
            {
                fread(&dead_table_count[i] , sizeof(dead_table_count[i]) , 1 , save_file) ;
            }

            fclose(save_file) ;

}


void make_textures()
{
    from_to_table_texture = SDL_CreateTextureFromSurface(my_render , from_to_table_surface) ;

    for(int i = 0 ; i < dead_table/2 ; i ++)
    {
       sprintf(dead_table_str , "%d", dead_table_count[i]);
       dead_table_count_surface[i] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;

       sprintf(dead_table_str , "%d", dead_table_count[i+5]);
       dead_table_count_surface[i+dead_table/2] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
    }

    for(int i = 0 ; i < dead_table ; i ++)
    {
        dead_table_count_texture[i] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[i]) ;
    }

    right_move_texture = SDL_CreateTextureFromSurface(my_render , right_move_surface) ;
    board_texture = SDL_CreateTextureFromSurface(my_render , board_surface) ;
    dead_table_texture2 = SDL_CreateTextureFromSurface(my_render , dead_table_surface2) ;

    for(int i = 0 ; i < pieces / 2 ; i ++)
    {
        if(!strcmp(black_pieces[i].name , "bpawn"))
        {
            pieces_textures[i] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[5]) ;
        }
        else if(!strcmp(black_pieces[i].name , "rook"))
        {
            pieces_textures[i] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[0]) ;
        }
        else if(!strcmp(black_pieces[i].name , "knight"))
        {
            pieces_textures[i] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[1]) ;
        }
        else if(!strcmp(black_pieces[i].name , "bishop"))
        {
            pieces_textures[i] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[2]) ;
        }
        else if(!strcmp(black_pieces[i].name , "queen"))
        {
            pieces_textures[i] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[3]) ;
        }
        else if(!strcmp(black_pieces[i].name , "king"))
        {
            pieces_textures[i] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[4]) ;
        }

        if(!strcmp(white_pieces[i].name , "wpawn"))
        {
            pieces_textures[i + 16] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[6]) ;
        }
        else if(!strcmp(white_pieces[i].name , "rook"))
        {
            pieces_textures[i + 16] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[7]) ;
        }
        else if(!strcmp(white_pieces[i].name , "knight"))
        {
            pieces_textures[i + 16] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[8]) ;
        }
        else if(!strcmp(white_pieces[i].name , "bishop"))
        {
            pieces_textures[i + 16] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[9]) ;
        }
        else if(!strcmp(white_pieces[i].name , "queen"))
        {
            pieces_textures[i + 16] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[10]) ;
        }
        else if(!strcmp(white_pieces[i].name , "king"))
        {
            pieces_textures[i + 16] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[11]) ;
        }
    }


}
//////////////////////////////////////////////////////////////////////////////////////////////////////
//////////////////////////////////taking input from white player//////////////////////////////////////////////////////

void white_player_input()
{
    SDL_Event event ;
    SDL_PollEvent(&event) ;
    switch(event.type)
    {
        case SDL_QUIT :
            exit_menu_state = ON ;
            setup_exit_menu() ;
            while(exit_menu_state)
            {
                input_exit_menu() ;
                render_exit_menu() ;
                SDL_Delay(frame_time) ;
            }
            destroy_exit_menu() ;

            cursor1 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_ARROW);
            SDL_SetCursor(cursor1);

            break ;
        case (SDL_MOUSEBUTTONDOWN) :
            for(int i = 0 ; i < pieces/2 ; i ++)
            {
                if(event.button.x > white_pieces[i].present_x && event.button.x < white_pieces[i].present_x + block_size &&
                   event.button.y > white_pieces[i].present_y && event.button.y < white_pieces[i].present_y + block_size)
                {   show_moves = ON ;
                    if (button_down == ON)
                    {
                        button_down = OFF ;
                    }
                    else
                    {
                        button_down = ON ;
                        current_white_piece = i ;
                        white_pieces[i].valid_x = white_pieces[i].present_x ;
                        white_pieces[i].valid_y = white_pieces[i].present_y ;
                    }
                }
            }


            if(event.button.x>=850 && event.button.x<=950
                       && event.button.y>=25 && event.button.y<=125)
                {
                    if(Mix_PlayingMusic() == 1 && music_state)
                    {
                        Mix_PauseMusic() ;
                        music_shape_texture = SDL_CreateTextureFromSurface(my_render , music_shape_surface[1]) ;
                        music_state = OFF ;
                    }
                    else if(Mix_PausedMusic() == 1 && !music_state)
                    {
                        Mix_ResumeMusic() ;
                        music_shape_texture = SDL_CreateTextureFromSurface(my_render , music_shape_surface[0]) ;
                        music_state = ON ;
                    }
                }

                        /////////////////////////////////undo////////////////////////////////////////////
            if(event.button.x >= game_label_struct[2].x && event.button.x <= game_label_struct[2].x + game_label_struct[2].width
                       && event.button.y >= game_label_struct[2].y && event.button.y <= game_label_struct[2].y + game_label_struct[2].height
                       && undo_moves > 0)
                    {
                        if (the_done_array[undo_moves - 1].promotion)
                        {
                            pieces_textures[the_done_array[undo_moves - 1].current_piece] =
                            SDL_CreateTextureFromSurface(my_render , pieces_surfaces[5]) ;
                            black_pieces[the_done_array[undo_moves - 1].current_piece].name = "bpawn" ;
                        }

                        if(the_done_array[undo_moves - 1].castling)
                        {
                            if(the_done_array[undo_moves - 1].board_to_x - the_done_array[undo_moves - 1].board_from_x == 2)
                            {
                                black_pieces[7].present_x = 7 * block_size ;
                                black_pieces[7].first_move = ON  ;
                                board_game[0][5] = 0 ;
                                board_game[0][7] = 1 ;
                            }
                            else if(the_done_array[undo_moves - 1].board_to_x - the_done_array[undo_moves - 1].board_from_x == -2)
                            {
                                black_pieces[0].present_x = 0  ;
                                black_pieces[0].first_move = ON  ;
                                board_game[0][3] = 0 ;
                                board_game[0][0] = 1 ;
                            }
                        }

                        if(the_done_array[undo_moves - 1].board_to_value != 0)
                        {
                            white_pieces[the_done_array[undo_moves - 1].eaten_piece].present_x = the_done_array[undo_moves - 1].to_x  ;
                            white_pieces[the_done_array[undo_moves - 1].eaten_piece].present_y = the_done_array[undo_moves - 1].to_y  ;
                            if(!strcmp(white_pieces[the_done_array[undo_moves - 1].eaten_piece].name , "wpawn"))
                            {
                                dead_table_count[0] -- ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[0] ) ;

                                dead_table_count_surface[0] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                                dead_table_count_texture[0] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[0]) ;

                            }
                            else if(!strcmp(white_pieces[the_done_array[undo_moves - 1].eaten_piece].name , "knight"))
                            {
                                dead_table_count[1] -- ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[1] ) ;

                                dead_table_count_surface[1] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                                dead_table_count_texture[1] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[1]) ;

                            }
                            else if(!strcmp(white_pieces[the_done_array[undo_moves - 1].eaten_piece].name , "bishop"))
                            {
                                dead_table_count[2] -- ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[2] ) ;

                                dead_table_count_surface[2] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                                dead_table_count_texture[2] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[2]) ;

                            }
                            else if(!strcmp(white_pieces[the_done_array[undo_moves - 1].eaten_piece].name , "rook"))
                            {
                                dead_table_count[3] -- ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[3] ) ;

                                dead_table_count_surface[3] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                                dead_table_count_texture[3] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[3]) ;

                            }
                            else if(!strcmp(white_pieces[the_done_array[undo_moves - 1].eaten_piece].name , "queen"))
                            {
                                dead_table_count[4] -- ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[4] ) ;

                                dead_table_count_surface[4] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                                dead_table_count_texture[4] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[4]) ;

                            }
                        }

                        black_pieces[the_done_array[undo_moves - 1].current_piece].present_x = the_done_array[undo_moves - 1].from_x ;
                        black_pieces[the_done_array[undo_moves - 1].current_piece].present_y = the_done_array[undo_moves - 1].from_y ;
                        black_pieces[the_done_array[undo_moves - 1].current_piece].first_move = the_done_array[undo_moves - 1].first_move ;


                        board_game[the_done_array[undo_moves - 1].board_from_y][the_done_array[undo_moves - 1].board_from_x] =
                        the_done_array[undo_moves - 1].board_from_value ;
                        board_game[the_done_array[undo_moves - 1].board_to_y][the_done_array[undo_moves - 1].board_to_x] =
                        the_done_array[undo_moves - 1].board_to_value ;

                        if(undo_moves > 1)
                        {
                            from_tile_surface = TTF_RenderText_Solid(font , board_moves[the_done_array[undo_moves - 2].board_from_y]
                                                                             [the_done_array[undo_moves - 2].board_from_x]
                                                                             , menu_color[0]) ;
                            to_tile_surface = TTF_RenderText_Solid(font , board_moves[the_done_array[undo_moves - 2].board_to_y]
                                                                             [the_done_array[undo_moves - 2].board_to_x] , menu_color[0]) ;

                            from_tile_texture = SDL_CreateTextureFromSurface(my_render , from_tile_surface) ;
                            to_tile_texture = SDL_CreateTextureFromSurface(my_render , to_tile_surface) ;


                            last_piece_moved_texture = pieces_textures[the_done_array[undo_moves - 2].current_piece + 16] ;
                        }

                        undo_moves -- ;
                        redo_moves ++ ;
                        turn = !(turn) ;
                        show_moves = OFF ;
                        white_player = !(white_player) ;
                    }

            /////////////////////////////////redo////////////////////////////////////////////
            if(event.button.x >= game_label_struct[3].x && event.button.x <= game_label_struct[3].x + game_label_struct[3].width
                       && event.button.y >= game_label_struct[3].y && event.button.y <= game_label_struct[3].y + game_label_struct[3].height
                       && redo_moves > 0)
                    {
                        if (the_done_array[undo_moves].promotion)
                        {
                            redo_promotion = ON ;
                            the_done_array[undo_moves].promotion = ON ;
                                setup_promotion() ;
                                while(the_done_array[undo_moves].promotion)
                                {
                                    promotion_input() ;
                                    render_promotion() ;
                                    SDL_Delay(frame_time) ;
                                }

                                cursor1 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_ARROW);
                                SDL_SetCursor(cursor1);

                                the_done_array[undo_moves].promotion = ON ;
                                redo_promotion = OFF ;
                                destroy_promotion() ;
                                redo_moves = 1 ;
                        }

                        if(the_done_array[undo_moves].castling)
                        {
                            if(the_done_array[undo_moves].board_to_x - the_done_array[undo_moves ].board_from_x == 2)
                            {
                                white_pieces[15].present_x = 5 * block_size ;
                                white_pieces[15].first_move = OFF  ;
                                board_game[7][5] = -1 ;
                                board_game[7][7] = 0 ;
                            }
                            else if(the_done_array[undo_moves ].board_to_x - the_done_array[undo_moves].board_from_x == -2)
                            {
                                white_pieces[8].present_x = 3 * block_size  ;
                                white_pieces[8].first_move = OFF  ;
                                board_game[7][3] = -1 ;
                                board_game[7][0] = 0 ;
                            }
                        }

                        board_game[the_done_array[undo_moves ].board_to_y][the_done_array[undo_moves ].board_to_x] =
                        the_done_array[undo_moves ].board_from_value ;
                        board_game[the_done_array[undo_moves ].board_from_y][the_done_array[undo_moves ].board_from_x] =
                        the_done_array[undo_moves ].board_to_value ;

                        if(the_done_array[undo_moves ].board_to_value != 0)
                        {
                            black_pieces[the_done_array[undo_moves ].eaten_piece].present_x = -100  ;
                            black_pieces[the_done_array[undo_moves ].eaten_piece].present_y = -100  ;
                            board_game[the_done_array[undo_moves ].board_from_y][the_done_array[undo_moves ].board_from_x] = 0 ;

                            if(!strcmp(black_pieces[the_done_array[undo_moves].eaten_piece].name , "bpawn"))
                            {
                                dead_table_count[5] ++ ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[5] ) ;

                                dead_table_count_surface[5] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                                dead_table_count_texture[5] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[5]) ;

                            }
                            else if(!strcmp(black_pieces[the_done_array[undo_moves].eaten_piece].name , "knight"))
                            {
                                dead_table_count[6] ++ ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[6] ) ;

                                dead_table_count_surface[6] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                                dead_table_count_texture[6] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[6]) ;

                            }
                            else if(!strcmp(black_pieces[the_done_array[undo_moves].eaten_piece].name , "bishop"))
                            {
                                dead_table_count[7] ++ ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[7] ) ;

                                dead_table_count_surface[7] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                                dead_table_count_texture[7] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[7]) ;

                            }
                            else if(!strcmp(black_pieces[the_done_array[undo_moves].eaten_piece].name , "rook"))
                            {
                                dead_table_count[8] ++ ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[8] ) ;

                                dead_table_count_surface[8] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                                dead_table_count_texture[8] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[8]) ;

                            }
                            else if(!strcmp(black_pieces[the_done_array[undo_moves].eaten_piece].name , "queen"))
                            {
                                dead_table_count[9] ++ ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[9] ) ;

                                dead_table_count_surface[9] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                                dead_table_count_texture[9] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[9]) ;

                            }
                        }

                        white_pieces[the_done_array[undo_moves ].current_piece ].present_x =
                        the_done_array[undo_moves ].to_x ;
                        white_pieces[the_done_array[undo_moves ].current_piece].present_y =
                        the_done_array[undo_moves ].to_y ;
                        white_pieces[the_done_array[undo_moves ].current_piece ].first_move =
                        the_done_array[undo_moves ].first_move ;

                        from_tile_surface = TTF_RenderText_Solid(font , board_moves[the_done_array[undo_moves].board_from_y]
                                                                         [the_done_array[undo_moves].board_from_x]
                                                                         , menu_color[0]) ;
                        to_tile_surface = TTF_RenderText_Solid(font , board_moves[the_done_array[undo_moves].board_to_y]
                                                                         [the_done_array[undo_moves].board_to_x] , menu_color[0]) ;

                        from_tile_texture = SDL_CreateTextureFromSurface(my_render , from_tile_surface) ;
                        to_tile_texture = SDL_CreateTextureFromSurface(my_render , to_tile_surface) ;


                        last_piece_moved_texture = pieces_textures[the_done_array[undo_moves].current_piece + 16] ;



                        show_moves = OFF ;
                        undo_moves ++ ;
                        redo_moves -- ;
                        turn = !(turn) ;
                        white_player = !(white_player) ;
                    }

            break ;
        case (SDL_MOUSEMOTION) :
            if(button_down == ON && (event.button.y <= 750 && event.button.y >= 160) &&
                (event.button.x <= 590 && event.button.x >= 0))
            {
                white_pieces[current_white_piece].present_x = (event.button.x - block_size/2);
                white_pieces[current_white_piece].present_y = (event.button.y - block_size/2) ;
            }
            for(int i = 2; i < 4; i ++) {
                    if( event.button.x>=game_label_struct[i].x
                        && event.button.x<=game_label_struct[i].x+game_label_struct[i].width&&
                        event.button.y>=game_label_struct[i].y
                        && event.button.y<=game_label_struct[i].y+game_label_struct[i].height)
                    {
                        if(!game_label_selected[i])
                        {
                            game_label_selected[i] = 1;
                            SDL_FreeSurface(game_label_surfaces[i]);
                            game_label_surfaces[i] = TTF_RenderText_Solid(font_2,game_label_names[i],game_label_color[3]);
                            game_label_textures[i] = SDL_CreateTextureFromSurface(my_render , game_label_surfaces[i]) ;

                            cursor2 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_HAND);
                            SDL_SetCursor(cursor2);
                        }

                    }
                    else
                    {
                        if(game_label_selected[i])
                        {
                            game_label_selected[i] = 0;
                            SDL_FreeSurface(game_label_surfaces[i]);
                            game_label_surfaces[i] = TTF_RenderText_Solid(font,game_label_names[i],game_label_color[2]);
                            game_label_textures[i] = SDL_CreateTextureFromSurface(my_render , game_label_surfaces[i]) ;


                            int check_cursor = OFF ;
                            for(int i = 2; i < 4; i ++)
                            {
                                if(game_label_selected[i] == 1)
                                {
                                    check_cursor = ON ;
                                }
                            }
                            if(!check_cursor )
                            {
                                cursor1 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_ARROW);
                                SDL_SetCursor(cursor1);
                            }
                        }
                    }
                }
            break ;
        case(SDL_MOUSEBUTTONUP) :
               if (button_down == ON)
                {
                    if (white_pieces[current_white_piece].valid_x == ((event.button.x)/block_size)*block_size &&
                        white_pieces[current_white_piece].valid_y == ((event.button.y)/block_size)*block_size)
                    {
                        button_down = OFF ;
                        white_pieces[current_white_piece].present_x = ((event.button.x)/block_size)*block_size ;
                        white_pieces[current_white_piece].present_y = ((event.button.y )/block_size)*block_size;
                    }
                    else
                    {


                        int valid_move = check_validity((white_pieces[current_white_piece].valid_x)/block_size ,
                                                        (white_pieces[current_white_piece].valid_y) / block_size - 2 ,
                                                        (event.button.x)/block_size , (event.button.y)/block_size - 2 ,
                                                        white_pieces[current_white_piece].name,
                                                        white_pieces[current_white_piece].first_move) ;
                        if(valid_move)
                        {
                            button_down = OFF ;
                            white_pieces[current_white_piece].present_x = ((event.button.x)/block_size)*block_size ;
                            white_pieces[current_white_piece].present_y = ((event.button.y )/block_size)*block_size ;

                            checking = ON ;
                            check_mate_after_moving = check_mate_func(black_pieces ,white_pieces[12]) ;
                            checking = OFF ;

                            if(!check_mate_after_moving)
                            {
                                white_pieces[current_white_piece].present_x = ((event.button.x)/block_size)*block_size ;
                                white_pieces[current_white_piece].present_y = ((event.button.y )/block_size)*block_size;

                                the_done_array[undo_moves].first_move = white_pieces[current_white_piece].first_move ;

                                white_pieces[current_white_piece].first_move = OFF ;

                                the_done_array[undo_moves].current_piece = current_white_piece ;
                                the_done_array[undo_moves].from_x = white_pieces[current_white_piece].valid_x ;
                                the_done_array[undo_moves].from_y = white_pieces[current_white_piece].valid_y ;
                                the_done_array[undo_moves].to_x = white_pieces[current_white_piece].present_x ;
                                the_done_array[undo_moves].to_y = white_pieces[current_white_piece].present_y ;
                                the_done_array[undo_moves].promotion = OFF ;



                                from_tile_surface = TTF_RenderText_Solid(font , board_moves[white_pieces[current_white_piece].valid_y/block_size-2]
                                                                         [white_pieces[current_white_piece].valid_x/block_size]
                                                                         , menu_color[0]) ;
                                to_tile_surface = TTF_RenderText_Solid(font , board_moves[white_pieces[current_white_piece].present_y/block_size-2]
                                                                         [white_pieces[current_white_piece].present_x/block_size] , menu_color[0]) ;

                                from_tile_texture = SDL_CreateTextureFromSurface(my_render , from_tile_surface) ;
                                to_tile_texture = SDL_CreateTextureFromSurface(my_render , to_tile_surface) ;

                                last_piece_moved_texture = pieces_textures[current_white_piece + 16] ;

                                if(castling)
                                {
                                    the_done_array[undo_moves].castling = ON ;
                                    castling = OFF ;
                                }
                                else
                                {
                                    the_done_array[undo_moves].castling = OFF ;
                                }



                                if(!(strcmp( white_pieces[current_white_piece].name, "wpawn")) &&
                                    (white_pieces[current_white_piece].present_y / block_size) - 2 == 0 )
                                {
                                    the_done_array[undo_moves].promotion = ON ;
                                    setup_promotion() ;
                                    while(the_done_array[undo_moves].promotion)
                                    {
                                        promotion_input() ;
                                        render_promotion() ;
                                        SDL_Delay(frame_time) ;
                                    }

                                    cursor1 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_ARROW);
                                    SDL_SetCursor(cursor1);

                                    the_done_array[undo_moves].promotion = ON ;
                                    destroy_promotion() ;
                                }
                                undo_moves ++ ;
                                redo_moves = 0 ;
                                turn = ON ;
                                show_moves = OFF ;
                                white_player = !(white_player) ;
                            }
                            else
                            {
                                if(castling)
                                {
                                    if(the_done_array[undo_moves].board_to_x - the_done_array[undo_moves].board_from_x == 2)
                                    {
                                        white_pieces[15].present_x = 7 * block_size ;
                                        white_pieces[15].first_move = ON  ;
                                        board_game[7][5] = 0 ;
                                        board_game[7][7] = -1 ;
                                    }
                                    else if(the_done_array[undo_moves].board_to_x - the_done_array[undo_moves].board_from_x == -2)
                                    {
                                        white_pieces[8].present_x = 0  ;
                                        white_pieces[8].first_move = ON  ;
                                        board_game[7][3] = 0 ;
                                        board_game[7][0] = -1 ;
                                    }
                                    castling = OFF ;
                                }

                                if(the_done_array[undo_moves].board_to_value != 0)
                                {
                                    black_pieces[the_done_array[undo_moves].eaten_piece].present_x = the_done_array[undo_moves].board_to_x * block_size  ;
                                    black_pieces[the_done_array[undo_moves].eaten_piece].present_y = (the_done_array[undo_moves].board_to_y + 2 ) * block_size ;

                                    if(!strcmp(black_pieces[the_done_array[undo_moves].eaten_piece].name , "bpawn"))
                                    {
                                        dead_table_count[5] -- ;
                                        sprintf(dead_table_str , "%d" ,dead_table_count[5] ) ;

                                        dead_table_count_surface[5] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                                        dead_table_count_texture[5] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[5]) ;

                                    }
                                    else if(!strcmp(black_pieces[the_done_array[undo_moves].eaten_piece].name , "knight"))
                                    {
                                        dead_table_count[6] -- ;
                                        sprintf(dead_table_str , "%d" ,dead_table_count[6] ) ;

                                        dead_table_count_surface[6] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                                        dead_table_count_texture[6] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[6]) ;

                                    }
                                    else if(!strcmp(black_pieces[the_done_array[undo_moves].eaten_piece].name , "bishop"))
                                    {
                                        dead_table_count[7] -- ;
                                        sprintf(dead_table_str , "%d" ,dead_table_count[7] ) ;

                                        dead_table_count_surface[7] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                                        dead_table_count_texture[7] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[7]) ;

                                    }
                                    else if(!strcmp(black_pieces[the_done_array[undo_moves].eaten_piece].name , "rook"))
                                    {
                                        dead_table_count[8] -- ;
                                        sprintf(dead_table_str , "%d" ,dead_table_count[8] ) ;

                                        dead_table_count_surface[8] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                                        dead_table_count_texture[8] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[8]) ;

                                    }
                                    else if(!strcmp(black_pieces[the_done_array[undo_moves].eaten_piece].name , "queen"))
                                    {
                                        dead_table_count[9] -- ;
                                        sprintf(dead_table_str , "%d" ,dead_table_count[9] ) ;

                                        dead_table_count_surface[9] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                                        dead_table_count_texture[9] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[9]) ;
                                    }
                                }

                                white_pieces[current_white_piece].present_x = white_pieces[current_white_piece].valid_x ;
                                white_pieces[current_white_piece].present_y = white_pieces[current_white_piece].valid_y ;

                                board_game[the_done_array[undo_moves].board_from_y][the_done_array[undo_moves].board_from_x] =
                                the_done_array[undo_moves].board_from_value ;
                                board_game[the_done_array[undo_moves].board_to_y][the_done_array[undo_moves].board_to_x] =
                                the_done_array[undo_moves].board_to_value ;

                                if(check_mate_before_moving)
                                {
                                    SDL_ShowSimpleMessageBox(SDL_MESSAGEBOX_INFORMATION,
                                                    "DANGER",
                                                    "Your King Still in Danger, White King",
                                                    NULL);
                                }
                                else
                                {
                                    SDL_ShowSimpleMessageBox(SDL_MESSAGEBOX_INFORMATION,
                                                    "DANGER",
                                                    "Your King Will Be in Danger, White King",
                                                    NULL);
                                }
                            }
                        }
                        else
                        {
                            button_down = OFF ;
                            white_pieces[current_white_piece].present_x = white_pieces[current_white_piece].valid_x ;
                            white_pieces[current_white_piece].present_y = white_pieces[current_white_piece].valid_y ;
                            SDL_ShowSimpleMessageBox(SDL_MESSAGEBOX_INFORMATION,
                                                    "Invalid Move",
                                                    "This Move Is Invalid. Please make another one, White King",
                                                    NULL);
                        }
                    }

                }

            break ;
    }
}
//////////////////////////////////////////////////////////////////////////////////////////////////////
//////////////////////////////////taking input from black player//////////////////////////////////////////////////////

void black_player_input()
{
    SDL_Event event ;
    SDL_PollEvent(&event) ;
    switch(event.type)
    {
        case SDL_QUIT :
            exit_menu_state = ON ;
            setup_exit_menu() ;
            while(exit_menu_state)
            {
                input_exit_menu() ;
                render_exit_menu() ;
                SDL_Delay(frame_time) ;
            }
            destroy_exit_menu() ;

            cursor1 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_ARROW);
            SDL_SetCursor(cursor1);

            break ;
        case (SDL_MOUSEBUTTONDOWN) :
            for(int i = 0 ; i < pieces/2 ; i ++)
            {
                if(event.button.x > black_pieces[i].present_x && event.button.x < black_pieces[i].present_x + block_size &&
                   event.button.y > black_pieces[i].present_y && event.button.y < black_pieces[i].present_y + block_size)
                {
                    show_moves = ON ;
                    if (button_down == ON)
                    {
                        button_down = OFF ;
                    }
                    else
                    {
                        button_down = ON ;
                        current_black_piece = i ;
                        black_pieces[i].valid_x = black_pieces[i].present_x ;
                        black_pieces[i].valid_y = black_pieces[i].present_y ;
                    }
                }
            }


            if(event.button.x>=850 && event.button.x<=950
                && event.button.y>=25 && event.button.y<=125)
            {
                    if(Mix_PlayingMusic() == 1 && music_state)
                    {
                        Mix_PauseMusic() ;
                        music_shape_texture = SDL_CreateTextureFromSurface(my_render , music_shape_surface[1]) ;
                        music_state = OFF ;
                    }
                    else if(Mix_PausedMusic() == 1 && !music_state)
                    {
                        Mix_ResumeMusic() ;
                        music_shape_texture = SDL_CreateTextureFromSurface(my_render , music_shape_surface[0]) ;
                        music_state = ON ;
                    }
            }
            /////////////////////////////////////////////undo//////////////////////////////////////

            if(event.button.x >= game_label_struct[2].x && event.button.x <= game_label_struct[2].x + game_label_struct[2].width
                       && event.button.y >= game_label_struct[2].y && event.button.y <= game_label_struct[2].y + game_label_struct[2].height
                        && undo_moves > 0)
                    {
                        if (the_done_array[undo_moves - 1].promotion)
                        {
                            pieces_textures[the_done_array[undo_moves - 1].current_piece + 16] =
                            SDL_CreateTextureFromSurface(my_render , pieces_surfaces[6]) ;
                            white_pieces[the_done_array[undo_moves - 1].current_piece].name = "wpawn" ;
                        }

                        if(the_done_array[undo_moves - 1].castling)
                        {
                            if(the_done_array[undo_moves - 1].board_to_x - the_done_array[undo_moves - 1].board_from_x == 2)
                            {
                                white_pieces[15].present_x = 7 * block_size ;
                                white_pieces[15].first_move = ON  ;
                                board_game[7][5] = 0 ;
                                board_game[7][7] = -1 ;
                            }
                            else if(the_done_array[undo_moves - 1].board_to_x - the_done_array[undo_moves - 1].board_from_x == -2)
                            {
                                white_pieces[8].present_x = 0  ;
                                white_pieces[8].first_move = ON  ;
                                board_game[7][3] = 0 ;
                                board_game[7][0] = -1 ;
                            }
                        }


                        if(the_done_array[undo_moves - 1].board_to_value != 0)
                        {
                            black_pieces[the_done_array[undo_moves - 1].eaten_piece].present_x = the_done_array[undo_moves - 1].to_x  ;
                            black_pieces[the_done_array[undo_moves - 1].eaten_piece].present_y =the_done_array[undo_moves - 1].to_y  ;

                            if(!strcmp(black_pieces[the_done_array[undo_moves - 1].eaten_piece].name , "bpawn"))
                            {
                                dead_table_count[5] -- ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[5] ) ;

                                dead_table_count_surface[5] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                                dead_table_count_texture[5] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[5]) ;

                            }
                            else if(!strcmp(black_pieces[the_done_array[undo_moves - 1].eaten_piece].name , "knight"))
                            {
                                dead_table_count[6] -- ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[6] ) ;

                                dead_table_count_surface[6] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                                dead_table_count_texture[6] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[6]) ;

                            }
                            else if(!strcmp(black_pieces[the_done_array[undo_moves - 1].eaten_piece].name , "bishop"))
                            {
                                dead_table_count[7] -- ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[7] ) ;

                                dead_table_count_surface[7] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                                dead_table_count_texture[7] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[7]) ;

                            }
                            else if(!strcmp(black_pieces[the_done_array[undo_moves - 1].eaten_piece].name , "rook"))
                            {
                                dead_table_count[8] -- ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[8] ) ;

                                dead_table_count_surface[8] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                                dead_table_count_texture[8] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[8]) ;

                            }
                            else if(!strcmp(black_pieces[the_done_array[undo_moves - 1].eaten_piece].name , "queen"))
                            {
                                dead_table_count[9] -- ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[9] ) ;

                                dead_table_count_surface[9] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                                dead_table_count_texture[9] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[9]) ;

                            }
                        }

                        white_pieces[the_done_array[undo_moves - 1].current_piece].present_x = the_done_array[undo_moves - 1].from_x ;
                        white_pieces[the_done_array[undo_moves - 1].current_piece].present_y = the_done_array[undo_moves - 1].from_y ;
                        white_pieces[the_done_array[undo_moves - 1].current_piece].first_move = the_done_array[undo_moves - 1].first_move ;

                        board_game[the_done_array[undo_moves - 1].board_from_y][the_done_array[undo_moves - 1].board_from_x] =
                        the_done_array[undo_moves - 1].board_from_value ;
                        board_game[the_done_array[undo_moves - 1].board_to_y][the_done_array[undo_moves - 1].board_to_x] =
                        the_done_array[undo_moves - 1].board_to_value ;


                        if(undo_moves > 1)
                        {
                            from_tile_surface = TTF_RenderText_Solid(font , board_moves[the_done_array[undo_moves - 2].board_from_y]
                                                                             [the_done_array[undo_moves - 2].board_from_x]
                                                                             , menu_color[0]) ;
                            to_tile_surface = TTF_RenderText_Solid(font , board_moves[the_done_array[undo_moves - 2].board_to_y]
                                                                             [the_done_array[undo_moves - 2].board_to_x] , menu_color[0]) ;

                            from_tile_texture = SDL_CreateTextureFromSurface(my_render , from_tile_surface) ;
                            to_tile_texture = SDL_CreateTextureFromSurface(my_render , to_tile_surface) ;

                            last_piece_moved_texture = pieces_textures[the_done_array[undo_moves - 2].current_piece] ;
                        }


                        show_moves = OFF ;
                        undo_moves -- ;
                        redo_moves ++ ;
                        turn = !(turn) ;
                        white_player = !(white_player) ;
                    }

                    /////////////////////////////////////////////redo//////////////////////////////////////
            if(event.button.x >= game_label_struct[3].x && event.button.x <= game_label_struct[3].x + game_label_struct[3].width
                       && event.button.y >= game_label_struct[3].y && event.button.y <= game_label_struct[3].y + game_label_struct[3].height
                       && redo_moves > 0)
                    {
                        if (the_done_array[undo_moves].promotion)
                        {
                            redo_promotion = ON ;
                            the_done_array[undo_moves].promotion = ON ;
                            setup_promotion() ;
                            while(the_done_array[undo_moves].promotion)
                            {
                                promotion_input() ;
                                render_promotion() ;
                                SDL_Delay(frame_time) ;
                            }

                            cursor1 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_ARROW);
                            SDL_SetCursor(cursor1);

                            the_done_array[undo_moves].promotion = ON ;
                            redo_promotion = OFF ;
                            destroy_promotion() ;
                            redo_moves = 1 ;
                        }

                        if(the_done_array[undo_moves ].castling)
                        {
                            if(the_done_array[undo_moves ].board_to_x - the_done_array[undo_moves ].board_from_x == 2)
                            {
                                black_pieces[7].present_x = 5 * block_size ;
                                black_pieces[7].first_move = OFF  ;
                                board_game[0][5] = 1 ;
                                board_game[0][7] = 0 ;
                            }
                            else if(the_done_array[undo_moves ].board_to_x - the_done_array[undo_moves ].board_from_x == -2)
                            {
                                black_pieces[0].present_x = 3 * block_size  ;
                                black_pieces[0].first_move = OFF  ;
                                board_game[0][3] = 1 ;
                                board_game[0][0] = 0 ;
                            }
                        }

                        board_game[the_done_array[undo_moves ].board_to_y][the_done_array[undo_moves ].board_to_x] =
                        the_done_array[undo_moves ].board_from_value ;
                        board_game[the_done_array[undo_moves ].board_from_y][the_done_array[undo_moves ].board_from_x] =
                        the_done_array[undo_moves ].board_to_value ;

                        if(the_done_array[undo_moves ].board_to_value != 0)
                        {
                            white_pieces[the_done_array[undo_moves ].eaten_piece].present_x = -100  ;
                            white_pieces[the_done_array[undo_moves ].eaten_piece].present_y = -100  ;
                            board_game[the_done_array[undo_moves ].board_from_y][the_done_array[undo_moves ].board_from_x] = 0 ;

                            if(!strcmp(white_pieces[the_done_array[undo_moves].eaten_piece].name , "wpawn"))
                            {
                                dead_table_count[0] ++ ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[0] ) ;

                                dead_table_count_surface[0] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                                dead_table_count_texture[0] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[0]) ;

                            }
                            else if(!strcmp(white_pieces[the_done_array[undo_moves].eaten_piece].name , "knight"))
                            {
                                dead_table_count[1] ++ ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[1] ) ;

                                dead_table_count_surface[1] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                                dead_table_count_texture[1] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[1]) ;

                            }
                            else if(!strcmp(white_pieces[the_done_array[undo_moves].eaten_piece].name , "bishop"))
                            {
                                dead_table_count[2] ++ ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[2] ) ;

                                dead_table_count_surface[2] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                                dead_table_count_texture[2] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[2]) ;

                            }
                            else if(!strcmp(white_pieces[the_done_array[undo_moves].eaten_piece].name , "rook"))
                            {
                                dead_table_count[3] ++ ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[3] ) ;

                                dead_table_count_surface[3] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                                dead_table_count_texture[3] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[3]) ;

                            }
                            else if(!strcmp(white_pieces[the_done_array[undo_moves].eaten_piece].name , "queen"))
                            {
                                dead_table_count[4] ++ ;
                                sprintf(dead_table_str , "%d" ,dead_table_count[4] ) ;

                                dead_table_count_surface[4] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                                dead_table_count_texture[4] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[4]) ;

                            }
                        }


                        black_pieces[the_done_array[undo_moves ].current_piece ].present_x =
                        the_done_array[undo_moves ].to_x ;
                        black_pieces[the_done_array[undo_moves ].current_piece].present_y =
                        the_done_array[undo_moves ].to_y ;
                        black_pieces[the_done_array[undo_moves ].current_piece ].first_move =
                        the_done_array[undo_moves ].first_move ;


                        from_tile_surface = TTF_RenderText_Solid(font , board_moves[the_done_array[undo_moves].board_from_y]
                                                                             [the_done_array[undo_moves].board_from_x]
                                                                             , menu_color[0]) ;
                        to_tile_surface = TTF_RenderText_Solid(font , board_moves[the_done_array[undo_moves].board_to_y]
                                                                             [the_done_array[undo_moves].board_to_x] , menu_color[0]) ;

                        from_tile_texture = SDL_CreateTextureFromSurface(my_render , from_tile_surface) ;
                        to_tile_texture = SDL_CreateTextureFromSurface(my_render , to_tile_surface) ;

                        last_piece_moved_texture = pieces_textures[the_done_array[undo_moves].current_piece] ;

                        show_moves = OFF ;
                        undo_moves ++ ;
                        redo_moves -- ;
                        turn = !(turn) ;
                        white_player = !(white_player) ;
                    }

            break ;




        case (SDL_MOUSEMOTION) :
            if(button_down == ON && (event.button.y <= 750 && event.button.y >= 160) &&
               (event.button.x <= 590 && event.button.x >= 0))
            {
                black_pieces[current_black_piece].present_x = (event.button.x - block_size/2);
                black_pieces[current_black_piece].present_y = (event.button.y - block_size/2) ;
            }
            for(int i = 2; i < 4; i ++) {
                    if(event.button.x>=game_label_struct[i].x
                       && event.button.x<=game_label_struct[i].x+game_label_struct[i].width&&
                        event.button.y>=game_label_struct[i].y
                        && event.button.y<=game_label_struct[i].y+game_label_struct[i].height)
                    {
                        if(!game_label_selected[i])
                        {
                            game_label_selected[i] = 1;
                            SDL_FreeSurface(game_label_surfaces[i]);
                            game_label_surfaces[i] = TTF_RenderText_Solid(font_2 ,game_label_names[i],game_label_color[3]);
                            game_label_textures[i] = SDL_CreateTextureFromSurface(my_render , game_label_surfaces[i]) ;

                            cursor2 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_HAND);
                            SDL_SetCursor(cursor2);

                        }

                    }
                    else
                    {
                        if(game_label_selected[i])
                        {
                            game_label_selected[i] = 0;
                            SDL_FreeSurface(game_label_surfaces[i]);
                            game_label_surfaces[i] = TTF_RenderText_Solid(font,game_label_names[i],game_label_color[2]);
                            game_label_textures[i] = SDL_CreateTextureFromSurface(my_render , game_label_surfaces[i]) ;

                            int check_cursor = OFF ;
                            for(int i = 2; i < 4; i ++)
                            {
                                if(game_label_selected[i] == 1)
                                {
                                    check_cursor = ON ;
                                }
                            }
                            if(!check_cursor )
                            {
                                cursor1 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_ARROW);
                                SDL_SetCursor(cursor1);
                            }
                        }
                    }
                }
            break ;




        case(SDL_MOUSEBUTTONUP) :
                if (button_down == ON)
                {
                    if (black_pieces[current_black_piece].valid_x == ((event.button.x)/block_size)*block_size &&
                        black_pieces[current_black_piece].valid_y == ((event.button.y)/block_size)*block_size)
                    {
                        button_down = OFF ;
                        black_pieces[current_black_piece].present_x = ((event.button.x)/block_size)*block_size ;
                        black_pieces[current_black_piece].present_y = ((event.button.y )/block_size)*block_size;
                    }
                    else
                    {


                        int valid_move = check_validity((black_pieces[current_black_piece].valid_x)/block_size ,
                                                        (black_pieces[current_black_piece].valid_y) / block_size - 2 ,
                                                        (event.button.x)/block_size , (event.button.y)/block_size - 2 ,
                                                        black_pieces[current_black_piece].name ,
                                                        black_pieces[current_black_piece].first_move) ;

                        if(valid_move)
                        {
                            button_down = OFF ;

                            black_pieces[current_black_piece].present_x = ((event.button.x)/block_size)*block_size ;
                            black_pieces[current_black_piece].present_y = ((event.button.y )/block_size)*block_size;

                            checking = ON ;
                            check_mate_after_moving = check_mate_func(white_pieces ,black_pieces[4]) ;
                            checking = OFF ;

                            if(!check_mate_after_moving)
                            {


                                black_pieces[current_black_piece].present_x = ((event.button.x)/block_size)*block_size ;
                                black_pieces[current_black_piece].present_y = ((event.button.y )/block_size)*block_size;

                                the_done_array[undo_moves].first_move = black_pieces[current_black_piece].first_move ;

                                black_pieces[current_black_piece].first_move = OFF ;

                                the_done_array[undo_moves].current_piece = current_black_piece ;
                                the_done_array[undo_moves].from_x = black_pieces[current_black_piece].valid_x ;
                                the_done_array[undo_moves].from_y = black_pieces[current_black_piece].valid_y ;
                                the_done_array[undo_moves].to_x = black_pieces[current_black_piece].present_x ;
                                the_done_array[undo_moves].to_y = black_pieces[current_black_piece].present_y ;
                                the_done_array[undo_moves].promotion = OFF ;


                                from_tile_surface = TTF_RenderText_Solid(font , board_moves[black_pieces[current_black_piece].valid_y/block_size-2]
                                                                         [black_pieces[current_black_piece].valid_x/block_size]
                                                                         , menu_color[0]) ;
                                to_tile_surface = TTF_RenderText_Solid(font , board_moves[black_pieces[current_black_piece].present_y/block_size-2]
                                                                         [black_pieces[current_black_piece].present_x/block_size] , menu_color[0]) ;

                                from_tile_texture = SDL_CreateTextureFromSurface(my_render , from_tile_surface) ;
                                to_tile_texture = SDL_CreateTextureFromSurface(my_render , to_tile_surface) ;

                                last_piece_moved_texture = pieces_textures[current_black_piece] ;

                                if(castling)
                                {
                                    the_done_array[undo_moves].castling = ON ;
                                    castling = OFF ;
                                }
                                else
                                {
                                    the_done_array[undo_moves].castling = OFF ;
                                }


                                if(!(strcmp( black_pieces[current_black_piece].name, "bpawn")) &&
                                    (black_pieces[current_black_piece].present_y / block_size) - 2 == 7 )
                                {
                                    the_done_array[undo_moves].promotion = ON ;
                                    setup_promotion() ;
                                    while(the_done_array[undo_moves].promotion)
                                    {
                                        promotion_input() ;
                                        render_promotion() ;
                                        SDL_Delay(frame_time) ;
                                    }

                                    cursor1 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_ARROW);
                                    SDL_SetCursor(cursor1);

                                    the_done_array[undo_moves].promotion = ON ;
                                    destroy_promotion() ;
                                }
                                undo_moves ++ ;
                                redo_moves = 0 ;
                                turn = ON ;
                                show_moves = OFF ;
                                white_player = !(white_player) ;
                            }
                            else
                            {
                                if(castling)
                                {
                                    if(the_done_array[undo_moves].board_to_x - the_done_array[undo_moves].board_from_x == 2)
                                    {
                                        black_pieces[7].present_x = 7 * block_size ;
                                        black_pieces[7].first_move = ON  ;
                                        board_game[0][5] = 0 ;
                                        board_game[0][7] = 1 ;
                                    }
                                    else if(the_done_array[undo_moves].board_to_x - the_done_array[undo_moves].board_from_x == -2)
                                    {
                                        black_pieces[0].present_x = 0  ;
                                        black_pieces[0].first_move = ON  ;
                                        board_game[0][3] = 0 ;
                                        board_game[0][0] = 1 ;
                                    }
                                    castling = OFF ;
                                }

                                if(the_done_array[undo_moves].board_to_value != 0)
                                {
                                    white_pieces[the_done_array[undo_moves].eaten_piece].present_x = the_done_array[undo_moves].board_to_x * block_size  ;
                                    white_pieces[the_done_array[undo_moves].eaten_piece].present_y = (the_done_array[undo_moves].board_to_y + 2 ) * block_size ;

                                    if(!strcmp(white_pieces[the_done_array[undo_moves].eaten_piece].name , "wpawn"))
                                    {
                                        dead_table_count[0] -- ;
                                        sprintf(dead_table_str , "%d" ,dead_table_count[0] ) ;

                                        dead_table_count_surface[0] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                                        dead_table_count_texture[0] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[0]) ;

                                    }
                                    else if(!strcmp(white_pieces[the_done_array[undo_moves].eaten_piece].name , "knight"))
                                    {
                                        dead_table_count[1] -- ;
                                        sprintf(dead_table_str , "%d" ,dead_table_count[1] ) ;

                                        dead_table_count_surface[1] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                                        dead_table_count_texture[1] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[1]) ;

                                    }
                                    else if(!strcmp(white_pieces[the_done_array[undo_moves].eaten_piece].name , "bishop"))
                                    {
                                        dead_table_count[2] -- ;
                                        sprintf(dead_table_str , "%d" ,dead_table_count[2] ) ;

                                        dead_table_count_surface[2] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                                        dead_table_count_texture[2] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[2]) ;

                                    }
                                    else if(!strcmp(white_pieces[the_done_array[undo_moves].eaten_piece].name , "rook"))
                                    {
                                        dead_table_count[3] -- ;
                                        sprintf(dead_table_str , "%d" ,dead_table_count[3] ) ;

                                        dead_table_count_surface[3] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                                        dead_table_count_texture[3] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[3]) ;

                                    }
                                    else if(!strcmp(white_pieces[the_done_array[undo_moves].eaten_piece].name , "queen"))
                                    {
                                        dead_table_count[4] -- ;
                                        sprintf(dead_table_str , "%d" ,dead_table_count[4] ) ;

                                        dead_table_count_surface[4] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                                        dead_table_count_texture[4] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[4]) ;

                                    }
                                }

                                black_pieces[current_black_piece].present_x = black_pieces[current_black_piece].valid_x ;
                                black_pieces[current_black_piece].present_y = black_pieces[current_black_piece].valid_y ;

                                board_game[the_done_array[undo_moves].board_from_y][the_done_array[undo_moves].board_from_x] =
                                the_done_array[undo_moves].board_from_value ;
                                board_game[the_done_array[undo_moves].board_to_y][the_done_array[undo_moves].board_to_x] =
                                the_done_array[undo_moves].board_to_value ;

                                if(check_mate_before_moving)
                                {
                                    SDL_ShowSimpleMessageBox(SDL_MESSAGEBOX_INFORMATION,
                                                    "DANGER",
                                                    "Your King Still in Danger, Black King",
                                                    NULL);
                                }
                                else
                                {
                                    SDL_ShowSimpleMessageBox(SDL_MESSAGEBOX_INFORMATION,
                                                    "DANGER",
                                                    "Your King Will Be in Danger, Black King",
                                                    NULL);
                                }
                            }

                        }
                        else
                        {
                            button_down = OFF ;
                            black_pieces[current_black_piece].present_x = black_pieces[current_black_piece].valid_x ;
                            black_pieces[current_black_piece].present_y = black_pieces[current_black_piece].valid_y ;
                            SDL_ShowSimpleMessageBox(SDL_MESSAGEBOX_INFORMATION,
                                                    "Invalid Move",
                                                    "This Move Is Invalid. Please make another one, Black King",
                                                    NULL);
                        }

                    }

                }
            break ;
    }
}
//////////////////////////////////////////////////////////////////////////////////////////////////////

void rendering()
{
    SDL_SetRenderDrawColor(my_render , 153 , 204 , 255 , 255) ;
    SDL_RenderClear(my_render) ;

    SDL_Rect the_game_labels[game_labels] ;
    for(int i = 0 ; i < game_labels ; i ++)
    {
       the_game_labels[i].x = game_label_struct[i].x ;
       the_game_labels[i].y = game_label_struct[i].y ;
       the_game_labels[i].w = game_label_struct[i].width ;
       the_game_labels[i].h = game_label_struct[i].height ;
    }


    for(int i = 2; i < game_labels-1 ; i ++)
    {
        SDL_RenderCopy(my_render , game_label_textures[i] , NULL , &the_game_labels[i]) ;
    }

    if(!game_not_over || !not_stalemate)
    {
        SDL_RenderCopy(my_render , game_label_textures[4] , NULL , &the_game_labels[0]) ;
    }
    else
    {
        if(white_player)
        {
            SDL_RenderCopy(my_render , game_label_textures[0] , NULL , &the_game_labels[0]) ;
        }
        else
        {
            SDL_RenderCopy(my_render , game_label_textures[1] , NULL , &the_game_labels[1]) ;
        }
    }

    SDL_Rect music_shape = {850 , 25 , 100 ,100} ;
    SDL_RenderCopy(my_render , music_shape_texture , NULL , &music_shape) ;


    SDL_Rect from_to_table_rect = {window_width - 225 , 150 , 225 , 150} ;
    SDL_RenderCopy(my_render , from_to_table_texture , NULL , &from_to_table_rect) ;
    SDL_SetRenderDrawColor(my_render , 0 , 0 , 0 , 255) ;
    SDL_RenderDrawRect(my_render , &from_to_table_rect) ;

    SDL_Rect from_tile = {window_width - 150 , 225 , 70 , 70} ;
    SDL_Rect to_tile = {window_width -75 , 225 , 70 , 70} ;
    SDL_RenderCopy(my_render , from_tile_texture , NULL , &from_tile) ;
    SDL_RenderCopy(my_render , to_tile_texture , NULL , &to_tile) ;
    SDL_Rect last_piece_moved_rect = {window_width - 225 , 225 , 75 , 75} ;
    SDL_RenderCopy(my_render , last_piece_moved_texture , NULL , &last_piece_moved_rect) ;

    SDL_Rect the_board = {0 , 150 , 600 , window_height - 150} ;
    SDL_RenderCopy(my_render , board_texture , NULL , &the_board ) ;
    SDL_SetRenderDrawColor(my_render , 0 , 0 , 0 , 255) ;
    SDL_RenderDrawRect(my_render , &the_board) ;

    check_king_move = ON ;
    if(white_player && show_moves)
    {
        check_possible_moves(black_pieces , white_pieces[current_white_piece] , white_pieces[12]) ;
    }
    else if (!white_player && show_moves)
    {
        check_possible_moves( white_pieces ,black_pieces[current_black_piece] , black_pieces[4]) ;
    }
    check_king_move = OFF ;


    SDL_Rect the_pieces[pieces] ;
    for (int i = 0 ; i < pieces/2 ; i ++)
    {
        the_pieces[i].x = black_pieces[i].present_x ;
        the_pieces[i + pieces/2].x = white_pieces[i].present_x ;

        the_pieces[i].y = black_pieces[i].present_y ;
        the_pieces[i + pieces/2].y = white_pieces[i].present_y ;

        the_pieces[i].w = block_size ;
        the_pieces[i + pieces/2].w = block_size ;

        the_pieces[i].h = block_size ;
        the_pieces[i + pieces/2].h = block_size ;
    }

    for(int i = 0 ; i < pieces ; i ++ )
    {
        SDL_RenderCopy(my_render , pieces_textures[i] , NULL , &the_pieces[i]) ;
    }

    SDL_Rect dead_tabel = { 675  , 375 , 300 , 375} ;
    SDL_RenderCopy(my_render , dead_table_texture2 , NULL , &dead_tabel) ;

    SDL_Rect dead_table_rects[dead_table] ;
    for(int i = 0 ; i < dead_table ; i ++)
    {
        dead_table_rects[i].x = dead_table_struct[i].x ;
        dead_table_rects[i].y = dead_table_struct[i].y ;
        dead_table_rects[i].w = dead_table_struct[i].width ;
        dead_table_rects[i].h = dead_table_struct[i].height ;
    }
    for(int i = 0 ; i < 10 ; i ++)
    {
        SDL_RenderCopy(my_render , dead_table_count_texture[i] , NULL , &dead_table_rects[i]) ;
    }

    SDL_RenderPresent(my_render) ;
}


void make_render_textures(int x , int y )
{
    SDL_Rect right_move_rect;
    right_move_rect.x = x * block_size ;
    right_move_rect.y = y * block_size ;
    right_move_rect.w = 75 ;
    right_move_rect.h = 75 ;
    SDL_RenderCopy(my_render , right_move_texture , NULL , &right_move_rect) ;
}


int check_possible_moves(game_pieces enemy_pieces[] , game_pieces ally_pieces , game_pieces ally_king)
{
    checking = ON ;
    int valid_move = OFF ;
    int another_move = OFF ;

    int board_replac[8][8] ;
    for(int i = 0 ; i < 8 ; i ++)
    {
        for (int j = 0 ; j < 8 ; j ++)
        {
            board_replac[i][j] = board_game[i][j] ;
        }
    }

    int from_x , from_y ;

    if(check_end_game)
    {
        from_x = ally_pieces.present_x/block_size ;
        from_y = ally_pieces.present_y/block_size-2 ;
    }
    else
   {
       from_x = ally_pieces.valid_x/block_size ;
        from_y = ally_pieces.valid_y/block_size-2 ;
   }

    if(!(strcmp(ally_pieces.name , "wpawn")))
    {
        valid_move = check_validity(from_x ,from_y ,
                                        from_x ,
                                        from_y-2 ,ally_pieces.name , ally_pieces.first_move) ;
        if(valid_move)
        {
            another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
            return_board(board_replac) ;
            if(another_move)
            {
                if(check_end_game)
                {
                    return ON ;
                }
                else
                {
                    make_render_textures( from_x , from_y ) ;
                }
            }
        }

        if(from_x-1 < 8 && from_x-1 >= 0 )
        {
            valid_move = check_validity(from_x ,from_y ,from_x-1 ,
                                            from_y-1 ,ally_pieces.name , ally_pieces.first_move) ;
            if(valid_move)
            {
                another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                return_board(board_replac) ;
                if(another_move)
                {
                    if(check_end_game)
                    {
                        return ON ;
                    }
                    else
                    {
                     make_render_textures( from_x-1 , from_y+1 ) ;
                    }
                }
            }
        }

        if(from_x+1 < 8 && from_x+1 >= 0 )
        {
            valid_move = check_validity(from_x ,from_y ,
                                            from_x+1 ,
                                            from_y-1 ,ally_pieces.name , ally_pieces.first_move) ;
            if(valid_move)
            {
                another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                return_board(board_replac) ;
                if(another_move)
                {
                    if(check_end_game)
                    {
                        return ON ;
                    }
                    else
                    {
                     make_render_textures( from_x+1 , from_y +1) ;
                    }
                }
            }
        }

        valid_move = check_validity(from_x ,from_y ,
                                                from_x ,
                                                from_y-1 ,ally_pieces.name , ally_pieces.first_move) ;
        if(valid_move)
        {
                another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                return_board(board_replac) ;
               if(another_move)
                {
                    if(check_end_game)
                    {
                        return ON ;
                    }
                    else
                    {
                     make_render_textures( from_x , from_y+1 ) ;
                    }
                }
        }

    }
    else if(!(strcmp(ally_pieces.name , "bpawn")))
    {
        valid_move = check_validity(from_x ,from_y ,
                                    from_x ,
                                    from_y+2 ,ally_pieces.name , ally_pieces.first_move) ;
        if(valid_move)
        {
            another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
            return_board(board_replac) ;
            if(another_move)
            {
                    if(check_end_game)
                    {
                        return ON ;
                    }
                    else
                    {
                        make_render_textures( from_x , from_y+4 ) ;
                    }
            }

        }

        if(from_x-1 < 8 && from_x-1 >= 0 )
        {
            valid_move = check_validity(from_x ,from_y ,
                                        from_x-1 ,
                                            from_y+1 ,ally_pieces.name , ally_pieces.first_move) ;
            if(valid_move)
            {
                another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                return_board(board_replac) ;

                if(another_move)
                {
                    if(check_end_game)
                    {
                        return ON ;
                    }
                    else
                    {
                     make_render_textures( from_x-1 , from_y+3 ) ;
                    }
                }
            }
        }

        if(from_x+1 < 8 && from_x+1 >= 0 )
        {
            valid_move = check_validity(from_x ,from_y ,
                                            from_x+1 ,
                                            from_y+1 ,ally_pieces.name , ally_pieces.first_move) ;
            if(valid_move)
            {
                another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                return_board(board_replac) ;
                if(another_move)
                {
                    if(check_end_game)
                    {
                        return ON ;
                    }
                    else
                    {
                        make_render_textures( from_x+1 , from_y+3 ) ;
                    }
                }
            }
        }


        valid_move = check_validity(from_x ,from_y ,
                                                from_x ,
                                                from_y+1 ,ally_pieces.name , ally_pieces.first_move) ;
        if(valid_move)
        {
            another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
            return_board(board_replac) ;
            if(another_move)
                {
                    if(check_end_game)
                    {
                        return ON ;
                    }
                    else
                    {
                     make_render_textures( from_x , from_y+3 ) ;
                    }
                }
        }
    }


    else if(!(strcmp(ally_pieces.name , "king")))
    {
        for(int x = -1 ; x < 2 ; x ++ )
        {
            for(int y = -1 ; y < 2 ; y ++ )
            {
                if(from_x+x < 8 && from_y+y < 8
                       &&from_x+x >= 0 && from_y+y >= 0)
                {
                    valid_move = check_validity(from_x ,from_y ,
                                            from_x+x ,
                                            from_y+y ,ally_pieces.name , ally_pieces.first_move) ;

                    if(valid_move)
                    {
                            ally_king.present_x = (from_x+x) * block_size ;
                            ally_king.present_y = (from_y+y+2) *block_size ;

                            another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;

                            return_board(board_replac) ;
                            if(another_move)
                            {
                                if(check_end_game)
                                {
                                    return ON ;
                                }
                                else
                                {
                                    make_render_textures( from_x+x , from_y+y+2 ) ;
                                }
                            }

                    }
                }
            }
        }
        if(!check_end_game)
        {
            valid_move = check_validity(from_x ,from_y ,
                                            from_x+2 ,
                                            from_y ,ally_pieces.name , ally_pieces.first_move) ;
            if(valid_move)
            {
                return_board(board_replac) ;
                make_render_textures( from_x+2 , from_y +2) ;

            }
            valid_move = check_validity(from_x ,from_y ,
                                                from_x-2 ,
                                                from_y ,ally_pieces.name , ally_pieces.first_move) ;
            if(valid_move)
            {
                return_board(board_replac) ;
                make_render_textures(from_x-2, from_y +2) ;

            }
        }



    }
    else if(!(strcmp(ally_pieces.name , "rook")))
    {
        for(int x = -7 ; x < 8 ; x ++)
        {
            if(from_x+x < 8 &&from_x+x >= 0 )
            {
                valid_move = check_validity(from_x ,from_y ,
                                            from_x+x ,
                                            from_y ,ally_pieces.name , ally_pieces.first_move) ;
                if(valid_move)
                {
                    another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                        return_board(board_replac) ;

                        if(another_move)
                        {
                                if(check_end_game)
                                {
                                    return ON ;
                                }
                                else
                                {
                                    make_render_textures( from_x+x , from_y+2 ) ;
                                }

                        }
                }
            }
            if(from_y+x < 8 && from_y+x >= 0 )
            {
                valid_move = check_validity(from_x ,from_y ,
                                        from_x ,
                                        from_y+x ,ally_pieces.name , ally_pieces.first_move) ;
                if(valid_move)
                {
                    another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                    return_board(board_replac) ;
                    if(another_move)
                    {
                        if(check_end_game)
                        {
                            return ON ;
                        }
                        else
                        {
                         make_render_textures( from_x , from_y+x+2 ) ;
                        }
                    }
                }
            }

        }
    }
    else if(!(strcmp(ally_pieces.name , "knight")))
    {
        for(int x = -2 ; x < 3 ; x ++)
        {
            for(int y = -2 ; y < 3 ; y ++)
            {
                if(from_x+x < 8 && from_y+y < 8
                       &&from_x+x >= 0 && from_y+y >= 0
                       && x != 0 && y != 0 && (abs(x) == 2 * abs(y) || abs(y) == 2 * abs(x)) )
                {
                    valid_move = check_validity(from_x ,from_y ,
                                                from_x+x ,
                                                from_y+y ,ally_pieces.name , ally_pieces.first_move) ;
                    if(valid_move)
                    {
                        another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                        return_board(board_replac) ;
                        if(another_move)
                        {
                            if(check_end_game)
                            {
                                return ON ;
                            }
                            else
                            {
                                 make_render_textures( from_x+x , from_y+y+2 ) ;
                            }
                        }
                    }
                }
            }
        }
    }
    else if(!(strcmp(ally_pieces.name , "bishop")))
    {
        for(int x = -7 ; x < 8 ; x ++)
        {
            if(from_x+x < 8 && from_y+x < 8
                       &&from_x+x >= 0 && from_y+x >= 0)
                        {
                        valid_move = check_validity(from_x ,from_y ,
                                                    from_x+x ,
                                                    from_y+x ,ally_pieces.name , ally_pieces.first_move) ;
                        if(valid_move)
                        {
                            another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                            return_board(board_replac) ;
                           if(another_move)
                            {
                                if(check_end_game)
                                {
                                    return ON ;
                                }
                                else
                                {
                                     make_render_textures( from_x+x , from_y+x+2 ) ;
                                }
                            }
                        }
                    }
            if(from_x-x < 8 && from_y+x < 8
                       &&from_x-x >= 0 && from_y+x >= 0)
                    {
                        valid_move = check_validity(from_x ,from_y ,
                                                    from_x-x ,
                                                    from_y+x ,ally_pieces.name , ally_pieces.first_move) ;
                        if(valid_move)
                        {
                            another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                            return_board(board_replac) ;
                            if(another_move)
                            {
                                if(check_end_game)
                                {
                                    return ON ;
                                }
                                else
                                {
                                 make_render_textures( from_x-x , from_y+x+2 ) ;
                                }
                            }
                        }
                    }
            if(from_x+x < 8 && from_y-x < 8
                       &&from_x+x >= 0 && from_y-x >= 0)
                    {
                        valid_move = check_validity(from_x ,from_y ,
                                                    from_x+x ,
                                                    from_y-x ,ally_pieces.name , ally_pieces.first_move) ;
                        if(valid_move)
                        {
                            another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                            return_board(board_replac) ;
                            if(another_move)
                            {
                                if(check_end_game)
                                {
                                    return ON ;
                                }
                                else
                                {
                                 make_render_textures( from_x+x , from_y-x+2 ) ;
                                }
                            }

                        }
                    }
            if(from_x-x < 8 && from_y-x < 8
                       &&from_x-x >= 0 && from_y-x >= 0)
                    {
                        valid_move = check_validity(from_x ,from_y ,
                                                    from_x-x ,
                                                    from_y-x ,ally_pieces.name , ally_pieces.first_move) ;
                        if(valid_move)
                        {
                            another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                            return_board(board_replac) ;
                           if(another_move)
                            {
                                if(check_end_game)
                                {
                                    return ON ;
                                }
                                else
                                {
                                 make_render_textures( from_x-x , from_y-x+2 ) ;
                                }
                            }
                        }
                    }
        }
    }
    else if(!(strcmp(ally_pieces.name , "queen")))
    {
        for(int x = -7 ; x < 8 ; x ++)
        {
            if(from_x+x < 8 &&from_x+x >= 0 )
            {
                valid_move = check_validity(from_x ,from_y ,
                                            from_x+x ,
                                            from_y ,ally_pieces.name , ally_pieces.first_move) ;
                if(valid_move)
                {
                    another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                        return_board(board_replac) ;

                        if(another_move)
                            {
                                if(check_end_game)
                                {
                                    return ON ;
                                }
                                else
                                {
                                    make_render_textures( from_x+x , from_y+2 ) ;
                                }

                        }
                }
            }
            if(from_y+x < 8 && from_y+x >= 0 )
            {
                valid_move = check_validity(from_x ,from_y ,
                                        from_x ,
                                        from_y+x ,ally_pieces.name , ally_pieces.first_move) ;
                if(valid_move)
                {
                    another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                    return_board(board_replac) ;
                    if(another_move)
                    {
                        if(check_end_game)
                        {
                            return ON ;
                        }
                        else
                        {
                         make_render_textures( from_x , from_y+x+2 ) ;
                        }
                    }
                }
            }
        }
        for(int x = -7 ; x < 8 ; x ++)
        {
            if(from_x+x < 8 && from_y+x < 8
                       &&from_x+x >= 0 && from_y+x >= 0)
                        {
                        valid_move = check_validity(from_x ,from_y ,
                                                    from_x+x ,
                                                    from_y+x ,ally_pieces.name , ally_pieces.first_move) ;
                        if(valid_move)
                        {
                            another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                            return_board(board_replac) ;
                           if(another_move)
                            {
                                if(check_end_game)
                                {
                                    return ON ;
                                }
                                else
                                {
                                     make_render_textures( from_x+x , from_y+x+2 ) ;
                                }
                            }
                        }
                    }
            if(from_x-x < 8 && from_y+x < 8
                       &&from_x-x >= 0 && from_y+x >= 0)
                    {
                        valid_move = check_validity(from_x ,from_y ,
                                                    from_x-x ,
                                                    from_y+x ,ally_pieces.name , ally_pieces.first_move) ;
                        if(valid_move)
                        {
                            another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                            return_board(board_replac) ;
                            if(another_move)
                            {
                                if(check_end_game)
                                {
                                    return ON ;
                                }
                                else
                                {
                                 make_render_textures( from_x-x , from_y+x+2 ) ;
                                }
                            }
                        }
                    }
            if(from_x+x < 8 && from_y-x < 8
                       &&from_x+x >= 0 && from_y-x >= 0)
                    {
                        valid_move = check_validity(from_x ,from_y ,
                                                    from_x+x ,
                                                    from_y-x ,ally_pieces.name , ally_pieces.first_move) ;
                        if(valid_move)
                        {
                            another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                            return_board(board_replac) ;
                            if(another_move)
                            {
                                if(check_end_game)
                                {
                                    return ON ;
                                }
                                else
                                {
                                 make_render_textures( from_x+x , from_y-x+2 ) ;
                                }
                            }

                        }
                    }
            if(from_x-x < 8 && from_y-x < 8
                       &&from_x-x >= 0 && from_y-x >= 0)
                    {
                        valid_move = check_validity(from_x ,from_y ,
                                                    from_x-x ,
                                                    from_y-x ,ally_pieces.name , ally_pieces.first_move) ;
                        if(valid_move)
                        {
                            another_move = !(check_mate_func(enemy_pieces ,ally_king)) ;
                            return_board(board_replac) ;
                           if(another_move)
                            {
                                if(check_end_game)
                                {
                                    return ON ;
                                }
                                else
                                {
                                 make_render_textures( from_x-x , from_y-x+2 ) ;
                                }
                            }
                        }
                    }
        }

    }
    checking = OFF ;
    return OFF ;
}


//////////////////////////////////check validity of their moves//////////////////////////////////////////////////////
int check_validity(int from_x , int from_y , int to_x , int to_y , char* name , int first_move)
{
    if(board_game[from_y][from_x] == 0)
    {
        return OFF ;
    }

    if(board_game[from_y][from_x] == board_game[to_y][to_x])
    {
        return OFF ;
    }

    if(!(strcmp(name , "wpawn")))
    {
            if(!((board_game[to_y][to_x] == 1 &&((to_x == from_x + 1 && to_y == from_y - 1) || (to_x == from_x - 1 && to_y == from_y - 1)))
                    ||(to_y == from_y - 2 && to_x == from_x && first_move && board_game[to_y][to_x] != 1 && board_game[to_y + 1][to_x] != 1
                    && board_game[to_y + 1][to_x] != -1) ||
                    (to_y == from_y - 1 && to_x == from_x && board_game[to_y][to_x] != 1)))
            {
                return OFF ;
            }

    }
    else if(!(strcmp(name , "bpawn")))
    {
            if(!((board_game[to_y][to_x] == -1 &&((to_x == from_x + 1 && to_y == from_y + 1) || (to_x == from_x - 1 && to_y == from_y + 1)))
                    ||(to_y == from_y + 2 && to_x == from_x && first_move && board_game[to_y][to_x] != -1 && board_game[to_y -1][to_x] != -1
                    && board_game[to_y - 1][to_x] != 1 ) ||
                    (to_y == from_y + 1 && to_x == from_x && board_game[to_y][to_x] != -1)))
            {
                return OFF ;
            }

    }
    else if(!(strcmp(name , "king")))
    {
        if(!(((from_y == to_y + 1 || from_y == to_y - 1) && (from_x == to_x || from_x == to_x + 1 || from_x == to_x - 1)) ||
              (from_y == to_y && (from_x == to_x - 1 || from_x == to_x + 1)) ||
             //castling condition
              (( (to_x - from_x == 2 && ((to_y == 0 &&black_pieces[7].present_x != -100 && board_game[from_y][from_x + 1] ==0)
                || (to_y == 7 &&black_pieces[15].present_x != -100 && board_game[from_y][from_x + 1] ==0) )
                  || to_x - from_x == -2  && ((to_y == 0 &&black_pieces[0].present_x != -100 &&board_game[from_y][from_x - 1] ==0 &&
                                                board_game[from_y][from_x - 3] ==0)
                                              || (to_y == 7 &&black_pieces[8].present_x != -100 && board_game[from_y][from_x - 1] ==0 &&
                                                   board_game[from_y][from_x - 3] ==0)) )
                  && first_move && from_y == to_y && board_game[to_y][to_x] == 0 &&
               !(check_mate_before_moving)) ) )) //castling condition
        {
            return OFF ;
        }
    }
    else if(!(strcmp(name , "rook")))
    {
        if(!(from_x == to_x || from_y == to_y))
        {
            return OFF ;
        }
        if(from_x == to_x )
        {
            if(from_y > to_y)
            {
                for(int i = from_y - 1 ; i > to_y ; i -- )
                {
                    if(board_game[i][from_x] != 0)
                    {
                        return OFF ;
                    }
                }
            }
            else if(from_y < to_y)
            {
                for(int i = from_y + 1 ; i < to_y ; i ++ )
                {
                    if(board_game[i][to_x] != 0)
                    {
                        return OFF ;
                    }
                }
            }
        }
        else if(from_y == to_y )
        {
            if(from_x > to_x)
            {
                for(int i = from_x - 1 ; i > to_x ; i -- )
                {
                    if(board_game[to_y][i] != 0)
                    {
                        return OFF ;
                    }
                }
            }
            else if(from_x < to_x)
            {
                for(int i = from_x + 1 ; i < to_x ; i ++ )
                {
                    if(board_game[to_y][i] != 0)
                    {
                        return OFF ;
                    }
                }
            }
        }
    }
    else if(!(strcmp(name , "knight")))
    {
        if(!((from_y == to_y + 2&&(from_x == to_x - 1 || from_x == to_x + 1)) ||
             (from_y == to_y + 1&&(from_x == to_x - 2 || from_x == to_x + 2)) ||
             (from_y == to_y - 1&&(from_x == to_x - 2 || from_x == to_x + 2)) ||
             (from_y == to_y - 2&&(from_x == to_x - 1 || from_x == to_x + 1))))
        {
            return OFF ;
        }
    }
    else if(!(strcmp(name , "bishop")))
    {
        if(!(abs(from_x - to_x) == abs(from_y - to_y)))
        {
            return OFF ;
        }
        if(to_x > from_x)
        {
            if(to_y > from_y)
            {
                for(int i = 1 ; i < abs(from_x - to_x) ; i ++)
                {
                    if(board_game[from_y + i][from_x + i] != 0)
                    {
                        return OFF ;
                    }
                }
            }
            else if(to_y < from_y)
            {
                for(int i = 1 ; i < abs(from_x - to_x) ; i ++)
                {
                    if(board_game[from_y - i][from_x + i] != 0)
                    {
                        return OFF ;
                    }
                }
            }
        }
        else if(to_x < from_x)
        {
            if(to_y > from_y)
            {
                for(int i = 1 ; i < abs(from_x - to_x) ; i ++)
                {
                    if(board_game[from_y + i][from_x - i] != 0)
                    {
                        return OFF ;
                    }
                }
            }
            else if(to_y < from_y)
            {
                for(int i = 1 ; i < abs(from_x - to_x) ; i ++)
                {
                    if(board_game[from_y - i][from_x - i] != 0)
                    {
                        return OFF ;
                    }
                }
            }
        }
    }
    else if(!(strcmp(name , "queen")))
    {
        if(!(((from_y == to_y + 1 || from_y == to_y - 1) && (from_x == to_x || from_x == to_x + 1 || from_x == to_x - 1)) ||
              (from_y == to_y && (from_x == to_x - 1 || from_x == to_x + 1)) ||
              (from_x == to_x || from_y == to_y) || (abs(from_x - to_x) == abs(from_y - to_y))))
           {
               return OFF ;
           }

        if((from_x == to_x || from_y == to_y))
        {
                if(from_x == to_x )
            {
                if(from_y > to_y)
                {
                    for(int i = from_y - 1 ; i > to_y ; i -- )
                    {
                        if(board_game[i][from_x] != 0)
                        {
                            return OFF ;
                        }
                    }
                }
                else if(from_y < to_y)
                {
                    for(int i = from_y + 1 ; i < to_y ; i ++ )
                    {
                        if(board_game[i][to_x] != 0)
                        {
                            return OFF ;
                        }
                    }
                }
            }
            else if(from_y == to_y )
            {
                if(from_x > to_x)
                {
                    for(int i = from_x - 1 ; i > to_x ; i -- )
                    {
                        if(board_game[to_y][i] != 0)
                        {
                            return OFF ;
                        }
                    }
                }
                else if(from_x < to_x)
                {
                    for(int i = from_x + 1 ; i < to_x ; i ++ )
                    {
                        if(board_game[to_y][i] != 0)
                        {
                            return OFF ;
                        }
                    }
                }
            }
        }
        if((abs(from_x - to_x) == abs(from_y - to_y)))
        {
            if(to_x > from_x)
            {
                if(to_y > from_y)
                {
                    for(int i = 1 ; i < abs(from_x - to_x) ; i ++)
                    {
                        if(board_game[from_y + i][from_x + i] != 0)
                        {
                            return OFF ;
                        }
                    }
                }
                else if(to_y < from_y)
                {
                    for(int i = 1 ; i < abs(from_x - to_x) ; i ++)
                    {
                        if(board_game[from_y - i][from_x + i] != 0)
                        {
                            return OFF ;
                        }
                    }
                }
            }
            else if(to_x < from_x)
            {
                if(to_y > from_y)
                {
                    for(int i = 1 ; i < abs(from_x - to_x) ; i ++)
                    {
                        if(board_game[from_y + i][from_x - i] != 0)
                        {
                            return OFF ;
                        }
                    }
                }
                else if(to_y < from_y)
                {
                    for(int i = 1 ; i < abs(from_x - to_x) ; i ++)
                    {
                        if(board_game[from_y - i][from_x - i] != 0)
                        {
                            return OFF ;
                        }
                    }
                }
            }
        }
    }


    if(!checking)
    {
        if((!(strcmp(name , "king"))) && ((to_x - from_x == 2 || to_x - from_x == -2) && first_move && from_y == to_y
                                          && !(check_mate_before_moving)))//castling move
        {
            if(from_y == 0)
            {
                if(to_x - from_x == 2)
                {

                    if(black_pieces[7].first_move && board_game[from_y][from_x + 1] ==0 && !(check_mate_before_moving)
                       && black_pieces[7].present_x != -100)
                    {
                        black_pieces[7].present_x = (to_x -1) *block_size ;
                        board_game[0][5] = 1 ;
                        board_game[0][7] = 0 ;
                        castling = ON ;
                    }
                    else
                    {
                        return OFF ;
                    }
                }
                else if(to_x - from_x == -2)
                {
                    if(black_pieces[0].first_move && board_game[from_y][from_x - 1] ==0 && board_game[from_y][from_x - 3] ==0
                        && !(check_mate_before_moving) && black_pieces[0].present_x != -100)
                    {
                            black_pieces[0].present_x = (from_x - 1) *block_size ;
                            board_game[0][from_x - 1] = 1 ;
                            board_game[0][0] = 0 ;
                            castling = ON ;
                    }
                    else
                    {
                        return OFF ;
                    }
                }
            }
            else if (from_y == 7)
            {
                if(to_x - from_x == 2)
                {
                    if(white_pieces[15].first_move && board_game[from_y][from_x + 1] ==0 && !(check_mate_before_moving)
                       && white_pieces[15].present_x != -100)
                    {
                        white_pieces[15].present_x = (to_x -1) *block_size ;
                        board_game[7][5] = -1 ;
                        board_game[7][7] = 0 ;
                        castling = ON ;
                    }
                    else
                    {
                        return OFF ;
                    }
                }
                else if(to_x - from_x == -2)
                {
                    if(white_pieces[8].first_move && board_game[from_y][from_x - 1] ==0 && board_game[from_y][from_x - 3] ==0
                        && !(check_mate_before_moving) && white_pieces[8].present_x != -100)
                    {
                        white_pieces[8].present_x = (from_x - 1) *block_size ;
                        board_game[7][from_x - 1] = -1 ;
                        board_game[7][0] = 0 ;
                        castling = ON ;
                    }
                    else
                    {
                        return OFF ;
                    }
                }
            }
        }

            if(board_game[to_y][to_x] < 0)
            {
                the_done_array[undo_moves].board_from_x = from_x ;
                the_done_array[undo_moves].board_from_y = from_y ;
                the_done_array[undo_moves].board_to_x = to_x ;
                the_done_array[undo_moves].board_to_y = to_y ;

                the_done_array[undo_moves].board_from_value = board_game[from_y][from_x] ;
                the_done_array[undo_moves].board_to_value = board_game[to_y][to_x] ;

                board_game[to_y][to_x] = board_game[from_y][from_x] ;
                board_game[from_y][from_x] = 0 ;

                for(int i = 0 ; i < pieces/2 ; i ++)
                {
                    if(to_x * block_size == white_pieces[i].present_x && (to_y + 2 )*block_size == white_pieces[i].present_y)
                    {
                        white_pieces[i].present_x = -100 ;
                        white_pieces[i].present_y = -100 ;
                        the_done_array[undo_moves].eaten_piece = i ;

                        if(!strcmp(white_pieces[i].name , "wpawn"))
                        {
                            dead_table_count[0] ++ ;
                            sprintf(dead_table_str , "%d" ,dead_table_count[0] ) ;

                            dead_table_count_surface[0] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                            dead_table_count_texture[0] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[0]) ;

                        }
                        else if(!strcmp(white_pieces[i].name , "knight"))
                        {
                            dead_table_count[1] ++ ;
                            sprintf(dead_table_str , "%d" ,dead_table_count[1] ) ;

                            dead_table_count_surface[1] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                            dead_table_count_texture[1] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[1]) ;

                        }
                        else if(!strcmp(white_pieces[i].name , "bishop"))
                        {
                            dead_table_count[2] ++ ;
                            sprintf(dead_table_str , "%d" ,dead_table_count[2] ) ;

                            dead_table_count_surface[2] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                            dead_table_count_texture[2] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[2]) ;

                        }
                        else if(!strcmp(white_pieces[i].name , "rook"))
                        {
                            dead_table_count[3] ++ ;
                            sprintf(dead_table_str , "%d" ,dead_table_count[3] ) ;

                            dead_table_count_surface[3] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                            dead_table_count_texture[3] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[3]) ;

                        }
                        else if(!strcmp(white_pieces[i].name , "queen"))
                        {
                            dead_table_count[4] ++ ;
                            sprintf(dead_table_str , "%d" ,dead_table_count[4] ) ;

                            dead_table_count_surface[4] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[0]) ;
                            dead_table_count_texture[4] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[4]) ;

                        }
                    }
                }
            }
            else if (board_game[to_y][to_x] > 0)
            {


                the_done_array[undo_moves].board_from_x = from_x ;
                the_done_array[undo_moves].board_from_y = from_y ;
                the_done_array[undo_moves].board_to_x = to_x ;
                the_done_array[undo_moves].board_to_y = to_y ;

                the_done_array[undo_moves].board_from_value = board_game[from_y][from_x] ;
                the_done_array[undo_moves].board_to_value = board_game[to_y][to_x] ;

                board_game[to_y][to_x] = board_game[from_y][from_x] ;
                board_game[from_y][from_x] = 0 ;

                for(int i = 0 ; i < pieces/2 ; i ++)
                {
                    if(to_x * block_size == black_pieces[i].present_x && (to_y + 2 )*block_size == black_pieces[i].present_y)
                    {
                        black_pieces[i].present_x = -100 ;
                        black_pieces[i].present_y = -100 ;
                        the_done_array[undo_moves].eaten_piece = i ;

                        if(!strcmp(black_pieces[i].name , "bpawn"))
                        {
                            dead_table_count[5] ++ ;
                            sprintf(dead_table_str , "%d" ,dead_table_count[5] ) ;

                            dead_table_count_surface[5] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                            dead_table_count_texture[5] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[5]) ;

                        }
                        else if(!strcmp(black_pieces[i].name , "knight"))
                        {
                            dead_table_count[6] ++ ;
                            sprintf(dead_table_str , "%d" ,dead_table_count[6] ) ;

                            dead_table_count_surface[6] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                            dead_table_count_texture[6] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[6]) ;

                        }
                        else if(!strcmp(black_pieces[i].name , "bishop"))
                        {
                            dead_table_count[7] ++ ;
                            sprintf(dead_table_str , "%d" ,dead_table_count[7] ) ;

                            dead_table_count_surface[7] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                            dead_table_count_texture[7] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[7]) ;

                        }
                        else if(!strcmp(black_pieces[i].name , "rook"))
                        {
                            dead_table_count[8] ++ ;
                            sprintf(dead_table_str , "%d" ,dead_table_count[8] ) ;

                            dead_table_count_surface[8] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                            dead_table_count_texture[8] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[8]) ;

                        }
                        else if(!strcmp(black_pieces[i].name , "queen"))
                        {
                            dead_table_count[9] ++ ;
                            sprintf(dead_table_str , "%d" ,dead_table_count[9] ) ;

                            dead_table_count_surface[9] = TTF_RenderText_Solid(font , dead_table_str , game_label_color[1]) ;
                            dead_table_count_texture[9] =SDL_CreateTextureFromSurface(my_render , dead_table_count_surface[9]) ;

                        }
                    }
                }
            }
            else if (board_game[to_y][to_x] == 0)
            {
                the_done_array[undo_moves].board_from_x = from_x ;
                the_done_array[undo_moves].board_from_y = from_y ;
                the_done_array[undo_moves].board_to_x = to_x ;
                the_done_array[undo_moves].board_to_y = to_y ;

                the_done_array[undo_moves].board_from_value = board_game[from_y][from_x] ;
                the_done_array[undo_moves].board_to_value = board_game[to_y][to_x] ;

                int temp = board_game[from_y][from_x] ;
                board_game[from_y][from_x] = board_game[to_y][to_x] ;
                board_game[to_y][to_x] = temp ;

            }
        }
    if(checking&&check_king_move)
    {

            if(board_game[to_y][to_x] < 0)
            {
                board_game[to_y][to_x] = board_game[from_y][from_x] ;
                board_game[from_y][from_x] = 0 ;

            }
            else if (board_game[to_y][to_x] > 0)
            {
                board_game[to_y][to_x] = board_game[from_y][from_x] ;
                board_game[from_y][from_x] = 0 ;

            }
            else if (board_game[to_y][to_x] == 0)
            {
                int temp = board_game[from_y][from_x] ;
                board_game[from_y][from_x] = board_game[to_y][to_x] ;
                board_game[to_y][to_x] = temp ;
            }

    }
    return ON ;
}


int check_mate_func(game_pieces enemy_pieces[] , game_pieces the_king)
{


    for(int i = 0 ; i <16 ; i ++)
    {
        if(enemy_pieces[i].present_x != -100 )
        {
            if(check_validity((enemy_pieces[i].present_x)/block_size ,
                                                        (enemy_pieces[i].present_y)/block_size - 2 ,
                                                        (the_king.present_x)/block_size , (the_king.present_y)/block_size - 2 ,
                                                        enemy_pieces[i].name,
                                                        enemy_pieces[i].first_move) )
                                                        {

                                                            return ON ;
                                                        }
        }
    }

    return OFF ;
}

void return_board(int board_replac[][8])
{
    for(int i = 0 ; i < 8 ; i ++)
    {
        for (int j = 0 ; j < 8 ; j ++)
        {
            board_game[i][j] = board_replac[i][j];
        }
    }
}


//////////////////////////////////////////////////////////////////////////////////////////////////////
//////////////////////////////////////////////promotion setting /////////////////////////////////
void setup_promotion()
{
    for(int i = 2 ; i < promotion_labels ; i ++)
    {
        promotion_label_surfaces[i] = TTF_RenderText_Solid(font , promotion_label_names[i] , promotion_label_color[2]) ;
    }
    promotion_label_surfaces[0] = TTF_RenderText_Solid(font , promotion_label_names[0] , promotion_label_color[0]) ;
    promotion_label_surfaces[1] = TTF_RenderText_Solid(font , promotion_label_names[1] , promotion_label_color[1]) ;

    for(int i = 0 ; i < promotion_labels ; i ++)
    {
        promotion_label_textures[i] = SDL_CreateTextureFromSurface(my_render , promotion_label_surfaces[i]) ;
    }
    for(int i = 2 ; i < 5; i += 2)
    {
        for (int j = 0 ; j < 2 ; j ++)
        {
        promotion_label_struct[i + j].x =650 + j * 2 * 100  ;
        promotion_label_struct[i + j].y = (i - 2) * 120 + 350 ;
        promotion_label_struct[i + j].width =  110;
        promotion_label_struct[i + j].height =  65;
        }
    }

    promotion_label_struct[0].x = window_width - 330 ;
    promotion_label_struct[0].y =  40 ;
    promotion_label_struct[0].width =  300;
    promotion_label_struct[0].height =  100;
    promotion_label_struct[1].x =window_width - 330  ;
    promotion_label_struct[1].y = 40 ;
    promotion_label_struct[1].width =  300;
    promotion_label_struct[1].height =  100;
}

void promotion_input()
{
    SDL_Event event ;
    SDL_PollEvent(&event) ;
    switch(event.type)
    {
        case SDL_QUIT :
             SDL_ShowSimpleMessageBox(SDL_MESSAGEBOX_INFORMATION,
                                                    "Complete your action, please",
                                                    "Please choose your promotion first, Sir",
                                                    NULL);
            break ;

        case SDL_MOUSEBUTTONDOWN :
            for(int i = 2; i < promotion_labels; i ++)
                {
                    if(event.button.x >= promotion_label_struct[i].x - promotion_label_struct[i].width / 4
                       && event.button.x <= promotion_label_struct[i].x - promotion_label_struct[i].width / 4 + 150
                       && event.button.y>=promotion_label_struct[i].y - 150
                       && event.button.y<=promotion_label_struct[i].y )
                       {
                           if(i == 2)
                           {
                               if(white_player)
                               {
                                   if(redo_promotion)
                                   {
                                    pieces_textures[the_done_array[undo_moves].current_piece + 16] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[10]) ;
                                    white_pieces[the_done_array[undo_moves].current_piece].name = "queen" ;
                                   }
                                   else
                                   {
                                    pieces_textures[current_white_piece + 16] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[10]) ;
                                    white_pieces[current_white_piece].name = "queen" ;
                                   }
                               }
                               else
                               {
                                   if(redo_promotion)
                                   {
                                    pieces_textures[the_done_array[undo_moves].current_piece] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[3]) ;
                                    black_pieces[the_done_array[undo_moves].current_piece].name = "queen" ;
                                   }
                                   else
                                   {
                                   pieces_textures[current_black_piece] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[3]) ;
                                   black_pieces[current_black_piece].name = "queen" ;
                                   }
                               }
                           }
                           if(i == 3)
                           {
                               if(white_player)
                               {
                                   if(redo_promotion)
                                   {
                                    pieces_textures[the_done_array[undo_moves].current_piece + 16] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[8]) ;
                                    white_pieces[the_done_array[undo_moves].current_piece].name = "knight" ;
                                   }
                                   else
                                   {
                                    pieces_textures[current_white_piece + 16] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[8]) ;
                                    white_pieces[current_white_piece].name = "knight" ;
                                   }
                               }
                               else
                               {
                                   if(redo_promotion)
                                   {
                                    pieces_textures[the_done_array[undo_moves].current_piece] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[1]) ;
                                    black_pieces[the_done_array[undo_moves].current_piece].name = "knight" ;
                                   }
                                   else
                                   {
                                   pieces_textures[current_black_piece] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[1]) ;
                                   black_pieces[current_black_piece].name = "knight" ;
                                   }
                               }
                           }
                           if(i == 4)
                           {
                               if(white_player)
                               {
                                   if(redo_promotion)
                                   {
                                    pieces_textures[the_done_array[undo_moves].current_piece + 16] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[7]) ;
                                    white_pieces[the_done_array[undo_moves].current_piece].name = "rook" ;
                                   }
                                   else
                                   {
                                    pieces_textures[current_white_piece + 16] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[7]) ;
                                    white_pieces[current_white_piece].name = "rook" ;
                                   }
                               }
                               else
                               {
                                   if(redo_promotion)
                                   {
                                       pieces_textures[the_done_array[undo_moves].current_piece] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[0]) ;
                                        black_pieces[the_done_array[undo_moves].current_piece].name = "rook" ;
                                   }
                                   else
                                   {
                                        pieces_textures[current_black_piece] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[0]) ;
                                        black_pieces[current_black_piece].name = "rook" ;
                                   }
                               }
                           }
                           if(i == 5)
                           {
                               if(white_player)
                               {
                                   if(redo_promotion)
                                   {
                                       pieces_textures[the_done_array[undo_moves].current_piece + 16] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[9]) ;
                                        white_pieces[the_done_array[undo_moves].current_piece].name = "bishop" ;
                                   }
                                   else
                                    {
                                    pieces_textures[current_white_piece + 16] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[9]) ;
                                    white_pieces[current_white_piece].name = "bishop" ;
                                    }
                               }
                               else
                               {
                                   if(redo_promotion)
                                   {
                                       pieces_textures[the_done_array[undo_moves].current_piece] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[2]) ;
                                        black_pieces[the_done_array[undo_moves].current_piece].name = "bishop" ;
                                   }
                                   else
                                   {
                                   pieces_textures[current_black_piece] = SDL_CreateTextureFromSurface(my_render , pieces_surfaces[2]) ;
                                   black_pieces[current_black_piece].name = "bishop" ;
                                   }
                               }
                           }
                           the_done_array[undo_moves].promotion = OFF ;
                       }
                }

            case SDL_MOUSEMOTION :
                for(int i = 0 ; i < 4 ; i ++)
                {
                    if(event.button.x >= promotion_label_struct[i+2].x - promotion_label_struct[i+2].width / 4 &&
                       event.button.x <= promotion_label_struct[i+2].x - promotion_label_struct[i+2].width / 4 + 150
                       && event.button.y >= promotion_label_struct[i+2].y - 130 &&
                       event.button.y <= promotion_label_struct[i+2].y - 150 + 150)
                    {
                        if(!promotion_selected[i])
                        {
                            promotion_selected[i] = 1;

                            cursor2 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_HAND);
                            SDL_SetCursor(cursor2);
                        }

                    }
                    else
                    {
                        if(promotion_selected[i])
                        {
                            promotion_selected[i] = 0;

                            int check_cursor = OFF ;
                            for(int i = 0; i < 4; i ++)
                            {
                                if(promotion_selected[i] == 1)
                                {
                                    check_cursor = ON ;
                                }
                            }
                            if(!check_cursor )
                            {
                                cursor1 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_ARROW);
                                SDL_SetCursor(cursor1);
                            }
                        }
                    }


    }
    }
}

void render_promotion()
{
    SDL_Rect screen ={window_width-375 , 0 , 375 , window_height} ;
    SDL_SetRenderDrawColor(my_render , 0 , 135 , 255 , 255) ;
    SDL_RenderFillRect(my_render, &screen) ;
    SDL_SetRenderDrawColor(my_render , 0 , 0 , 0 , 255) ;
    SDL_RenderDrawRect(my_render, &screen) ;


    SDL_Rect promotion_labels_rects[promotion_labels] ;
    for(int i = 0 ; i < promotion_labels ; i ++)
   {
       promotion_labels_rects[i].x = promotion_label_struct[i].x ;
       promotion_labels_rects[i].y = promotion_label_struct[i].y ;
       promotion_labels_rects[i].w = promotion_label_struct[i].width ;
       promotion_labels_rects[i].h = promotion_label_struct[i].height ;
   }

   SDL_Rect promotion_pieces[4] ;
   for (int i = 0 ; i < 4 ; i ++)
   {
        promotion_pieces[i].x = promotion_label_struct[i+2].x - promotion_label_struct[i+2].width / 4;
        promotion_pieces[i].y = promotion_label_struct[i+2].y - 130 ;
        promotion_pieces[i].w = 150 ;
        promotion_pieces[i].h = 150 ;
   }

   if(white_player)
   {
       SDL_RenderCopy(my_render , promotion_label_textures[0] , NULL , &promotion_labels_rects[0]) ;
       SDL_RenderCopy(my_render , pieces_textures[27] , NULL , &promotion_pieces[0]) ;
       SDL_RenderCopy(my_render , pieces_textures[25] , NULL , &promotion_pieces[1]) ;
       SDL_RenderCopy(my_render , pieces_textures[24] , NULL , &promotion_pieces[2]) ;
       SDL_RenderCopy(my_render , pieces_textures[26] , NULL , &promotion_pieces[3]) ;

   }
   else
   {
       SDL_RenderCopy(my_render , promotion_label_textures[1] , NULL , &promotion_labels_rects[0]) ;
       SDL_RenderCopy(my_render , pieces_textures[3] , NULL , &promotion_pieces[0]) ;
       SDL_RenderCopy(my_render , pieces_textures[6] , NULL , &promotion_pieces[1]) ;
       SDL_RenderCopy(my_render , pieces_textures[7] , NULL , &promotion_pieces[2]) ;
       SDL_RenderCopy(my_render , pieces_textures[5] , NULL , &promotion_pieces[3]) ;
   }
   for(int i = 2 ; i < promotion_labels ; i ++)
   {
       SDL_RenderCopy(my_render , promotion_label_textures[i] , NULL , &promotion_labels_rects[i]) ;
   }
   SDL_RenderPresent(my_render) ;
}

//////////////////////////////////////////////////////////////////////////////////////////////////////

//////////////////////////////////exit menu variables //////////////////////////////////////////////////////
void setup_exit_menu()
{
    for(int i = 0 ; i < exit_labels ; i ++)
    {
        exit_label_surfaces[i] = TTF_RenderText_Solid(font , exit_label_names[i] , exit_label_color[0]) ;
    }

    for(int i = 0 ; i < exit_labels ; i ++)
    {
        exit_label_textures[i] = SDL_CreateTextureFromSurface(my_render , exit_label_surfaces[i]) ;
    }

    for(int i = 1 ; i < exit_labels ; i ++)
    {
        exit_label_struct[i].y = 180 + 100 * i ;
        exit_label_struct[i].height = 100 ;
    }
        exit_label_struct[0].x = 210 ;
        exit_label_struct[0].y = 100  ;
        exit_label_struct[0].width = 550 ;
        exit_label_struct[0].height = 200 ;

        exit_label_struct[1].width = 350 ;
        exit_label_struct[2].width = 300 ;
        exit_label_struct[3].width = 400 ;

        exit_label_struct[1].x = 310 ;
        exit_label_struct[2].x = 340 ;
        exit_label_struct[3].x = 290 ;
}

void input_exit_menu()
{
    SDL_Event event ;
    SDL_PollEvent(&event) ;
    switch(event.type)
    {
        case SDL_MOUSEBUTTONDOWN :
                    if(event.button.x >= exit_label_struct[1].x && event.button.x <= exit_label_struct[1].x + exit_label_struct[1].width
                       && event.button.y >= exit_label_struct[1].y && event.button.y <= exit_label_struct[1].y + exit_label_struct[1].height)
                    {
                        exit_menu_state = OFF ;
                    }
                    if(event.button.x >= exit_label_struct[2].x && event.button.x <= exit_label_struct[2].x + exit_label_struct[2].width
                       && event.button.y >= exit_label_struct[2].y && event.button.y <= exit_label_struct[2].y + exit_label_struct[2].height)
                    {
                        FILE *save_file ;
                        save_file = fopen("Resources/saved_game.bin" , "wb") ;
                        for(int i = 0 ; i < pieces/2 ; i ++)
                        {
                            fwrite(&white_pieces[i] , sizeof(white_pieces[i]) , 1 , save_file) ;
                            fwrite(&black_pieces[i] , sizeof(black_pieces[i]) , 1 , save_file) ;
                        }
                        for(int i = 0 ; i < 8 ; i ++)
                        {
                            for(int j = 0 ; j < 8 ; j ++)
                            {
                                 fwrite(&board_game[i][j] , sizeof(board_game[i][j]) , 1 , save_file) ;
                            }
                        }
                        fwrite(&white_player , sizeof(white_player) , 1 , save_file) ;

                        for(int i = 0 ; i < dead_table ; i ++)
                        {
                            fwrite(&dead_table_count[i] , sizeof(dead_table_count[i]) , 1 , save_file) ;
                        }

                        fclose(save_file) ;
                        exit_menu_state = OFF ;
                        game_states = OFF ;
                        play_again = OFF ;
                        game_over_menu = OFF ;
                    }
                    if(event.button.x >= exit_label_struct[3].x && event.button.x <= exit_label_struct[3].x + exit_label_struct[3].width
                       && event.button.y >= exit_label_struct[3].y && event.button.y <= exit_label_struct[3].y + exit_label_struct[3].height)
                    {
                        FILE *save_file ;
                        save_file = fopen("Resources/saved_game.bin" , "wb") ;
                        fclose(save_file) ;
                        exit_menu_state = OFF ;
                        game_states = OFF ;
                        play_again = OFF ;
                        game_over_menu = OFF ;
                    }


            break ;
        case SDL_MOUSEMOTION :
            for(int i = 1 ; i < exit_labels; i ++)
                {
                    if(event.button.x >= exit_label_struct[i].x && event.button.x <= exit_label_struct[i].x + exit_label_struct[i].width
                       && event.button.y >= exit_label_struct[i].y && event.button.y <= exit_label_struct[i].y + exit_label_struct[i].height)
                    {
                        if(!exit_label_selected[i])
                        {
                            exit_label_selected[i] = 1;
                            SDL_FreeSurface(exit_label_surfaces[i]) ;
                            exit_label_surfaces[i] = TTF_RenderText_Solid(font_2 , exit_label_names[i] , exit_label_color[1]);
                            exit_label_textures[i] = SDL_CreateTextureFromSurface(my_render , exit_label_surfaces[i]) ;

                            cursor2 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_HAND);
                            SDL_SetCursor(cursor2);
                        }

                    }
                    else
                    {
                        if(exit_label_selected[i])
                        {
                            exit_label_selected[i] = 0;
                            SDL_FreeSurface(exit_label_surfaces[i]);
                            exit_label_surfaces[i] = TTF_RenderText_Solid(font , exit_label_names[i] , exit_label_color[0]);
                            exit_label_textures[i] = SDL_CreateTextureFromSurface( my_render , exit_label_surfaces[i]) ;

                            int check_cursor = OFF ;
                            for(int i = 1; i < exit_labels; i ++)
                            {
                                if(exit_label_selected[i] == 1)
                                {
                                    check_cursor = ON ;
                                }
                            }
                            if(!check_cursor )
                            {
                                cursor1 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_ARROW);
                                SDL_SetCursor(cursor1);
                            }
                        }
                    }
                }
                break ;
    }
}

void render_exit_menu()
{
     SDL_Rect screen ={185 , 90 , 600 , 500} ;
     SDL_SetRenderDrawColor(my_render , 255 , 102 , 102 , 255) ;
     SDL_RenderFillRect(my_render, &screen) ;
     SDL_SetRenderDrawColor(my_render , 0 , 0 , 0 , 255) ;
     SDL_RenderDrawRect(my_render, &screen) ;

     SDL_Rect exit_labels_rects[exit_labels] ;
    for(int i = 0 ; i < exit_labels ; i ++)
    {
       exit_labels_rects[i].x = exit_label_struct[i].x ;
       exit_labels_rects[i].y = exit_label_struct[i].y ;
       exit_labels_rects[i].w = exit_label_struct[i].width ;
       exit_labels_rects[i].h = exit_label_struct[i].height ;
    }

    for(int i = 0 ; i < exit_labels ; i ++)
    {
       SDL_RenderCopy(my_render , exit_label_textures[i] , NULL , &exit_labels_rects[i]) ;
    }

    SDL_RenderPresent(my_render) ;
}
//////////////////////////////////////////////////////////////////////////////////////////////////////
//////////////////////////////////game over menu functions //////////////////////////////////////////////////////

void game_over_menu_setup()
{
    for(int i = 0 ; i < game_over_labels-1 ; i ++)
    {
       game_over_surfaces[i] = TTF_RenderText_Solid(font , game_over_menu_labels[i] , game_over_menu_color[i]) ;
    }
    game_over_surfaces[4] = TTF_RenderText_Solid(font , game_over_menu_labels[4] , game_over_menu_color[3]) ;

    for(int i = 0 ; i < game_over_labels ; i ++)
    {
         game_over_textures[i] = SDL_CreateTextureFromSurface(my_render , game_over_surfaces[i]) ;
    }

    for(int i = 0 ; i < game_over_labels - 2 ; i ++)
    {
        game_over_menu_struct[i].x =210 ;
        game_over_menu_struct[i].y = 140 ;
        game_over_menu_struct[i].width = 550 ;
        game_over_menu_struct[i].height = 200 ;
    }
    for(int i = 3 ; i < game_over_labels ; i ++)
    {
        game_over_menu_struct[i].x = 340 ;
        game_over_menu_struct[i].y =40 + 100 * i ;
        game_over_menu_struct[i].width = 300 ;
        game_over_menu_struct[i].height = 100 ;
    }
}

void input_game_over_menu()
{
    SDL_Event event ;
    SDL_PollEvent(&event) ;
    switch(event.type)
    {
        case SDL_MOUSEBUTTONDOWN :
                    if(event.button.x >= game_over_menu_struct[3].x && event.button.x <= game_over_menu_struct[3].x + game_over_menu_struct[3].width
                       && event.button.y >= game_over_menu_struct[3].y && event.button.y <= game_over_menu_struct[3].y + game_over_menu_struct[3].height)
                    {
                        white_player = ON ;
                        game_over_menu = OFF ;
                        play_again = ON ;
                        game_states = ON ;
                        load_game = OFF ;
                        redo_moves = 0 ;
                        undo_moves = 0 ;
                        for(int i = 0 ; i < dead_table ; i ++)
                        {
                            dead_table_count[i] = 0 ;
                        }
                        game_not_over = ON ;
                        not_stalemate = ON ;
                    }
                    if(event.button.x >= game_over_menu_struct[4].x && event.button.x <= game_over_menu_struct[4].x + game_over_menu_struct[4].width
                       && event.button.y >= game_over_menu_struct[4].y && event.button.y <= game_over_menu_struct[4].y + game_over_menu_struct[4].height)
                    {

                        play_again = OFF ;
                        game_over_menu = OFF ;
                    }



            break ;
        case SDL_MOUSEMOTION :
            for(int i = 3 ; i < game_over_labels; i ++)
                {
                    if(event.button.x >= game_over_menu_struct[i].x && event.button.x <= game_over_menu_struct[i].x + game_over_menu_struct[i].width
                       && event.button.y >= game_over_menu_struct[i].y && event.button.y <= game_over_menu_struct[i].y + game_over_menu_struct[i].height)
                    {
                        if(!game_over_label_selected[i])
                        {
                            game_over_label_selected[i] = 1;
                            SDL_FreeSurface(game_over_surfaces[i]) ;
                            game_over_surfaces[i] = TTF_RenderText_Solid(font_2 , game_over_menu_labels[i] , game_over_menu_color[4]);
                            game_over_textures[i] = SDL_CreateTextureFromSurface(my_render , game_over_surfaces[i]) ;

                            cursor2 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_HAND);
                            SDL_SetCursor(cursor2);
                        }

                    }
                    else
                    {
                        if(game_over_label_selected[i])
                        {
                            game_over_label_selected[i] = 0;
                            SDL_FreeSurface(game_over_surfaces[i]);
                            game_over_surfaces[i] = TTF_RenderText_Solid(font , game_over_menu_labels[i] , game_over_menu_color[3]);
                            game_over_textures[i] = SDL_CreateTextureFromSurface( my_render , game_over_surfaces[i]) ;

                            int check_cursor = OFF ;
                            for(int i = 3; i < game_over_labels; i ++)
                            {
                                if(game_over_label_selected[i] == 1)
                                {
                                    check_cursor = ON ;
                                }
                            }
                            if(!check_cursor )
                            {
                                cursor1 = SDL_CreateSystemCursor(SDL_SYSTEM_CURSOR_ARROW);
                                SDL_SetCursor(cursor1);
                            }
                        }
                    }
                }
                break ;
    }
}



void render_game_over_menu()
{
     SDL_Rect screen ={185 , 140 , 600 , 450} ;
     SDL_SetRenderDrawColor(my_render , 255 , 102 , 102 , 255) ;
     SDL_RenderFillRect(my_render, &screen) ;
     SDL_SetRenderDrawColor(my_render , 0 , 0 , 0 , 255) ;
     SDL_RenderDrawRect(my_render, &screen) ;

     SDL_Rect game_over_menu_rects[game_over_labels] ;
    for(int i = 0 ; i < game_over_labels ; i ++)
    {
       game_over_menu_rects[i].x = game_over_menu_struct[i].x ;
       game_over_menu_rects[i].y = game_over_menu_struct[i].y ;
       game_over_menu_rects[i].w = game_over_menu_struct[i].width ;
       game_over_menu_rects[i].h = game_over_menu_struct[i].height ;
    }

    if(!game_not_over)
    {
        if(!white_player)
        {
            SDL_RenderCopy(my_render ,  game_over_textures[0] , NULL , &game_over_menu_rects[0]) ;
        }
        else
        {
            SDL_RenderCopy(my_render ,  game_over_textures[1] , NULL , &game_over_menu_rects[1]) ;
        }
    }
    else
    {
        SDL_RenderCopy(my_render ,  game_over_textures[2] , NULL , &game_over_menu_rects[2]) ;
    }

    for(int i = 3 ; i < game_over_labels ; i ++)
    {
       SDL_RenderCopy(my_render ,  game_over_textures[i] , NULL , &game_over_menu_rects[i]) ;
    }

    SDL_RenderPresent(my_render) ;
}

//////////////////////////////////////////////////////////////////////////////////////////////////////

void destroy_promotion()
{
        for(int i = 0 ; i < promotion_labels ; i ++)
        {
        SDL_FreeSurface(promotion_label_surfaces[i]) ;
        SDL_DestroyTexture(promotion_label_textures[i]) ;
        }
}

void destroy_menu()
{
    for(int i = 0 ; i < menu_labels ; i ++)
        {
        SDL_FreeSurface(menu_label_surfaces[i]) ;
        SDL_DestroyTexture(menu_label_textures[i]) ;
        }
}

void destroy_exit_menu()
{
    for(int i = 0 ; i < exit_labels ; i ++)
        {
        SDL_FreeSurface(exit_label_surfaces[i]) ;
        SDL_DestroyTexture(exit_label_textures[i]) ;
        }
}

void destroy_game_over_menu()
{
    for(int i = 0 ; i < game_over_labels ; i ++)
        {
        SDL_FreeSurface(game_over_surfaces[i]) ;
        SDL_DestroyTexture(game_over_textures[i]) ;
        }
}

void destroy_game()
{
    for(int i = 0 ; i < pieces ; i ++)
    {
        SDL_DestroyTexture(pieces_textures[i]) ;
    }
    for(int i = 0 ; i < 12 ; i ++)
    {
        SDL_FreeSurface(pieces_surfaces[i]) ;
    }
    SDL_FreeSurface(board_surface) ;
    SDL_DestroyTexture(board_texture) ;

    SDL_FreeSurface(dead_table_surface2) ;
    SDL_DestroyTexture(dead_table_texture2) ;

    SDL_FreeSurface(right_move_surface) ;
    SDL_DestroyTexture(right_move_texture) ;

    SDL_FreeSurface(from_tile_surface) ;
    SDL_DestroyTexture(from_tile_texture) ;

    SDL_FreeSurface(to_tile_surface) ;
    SDL_DestroyTexture(to_tile_texture) ;

    SDL_FreeSurface(from_to_table_surface) ;
    SDL_DestroyTexture(from_to_table_texture) ;

    SDL_DestroyTexture(last_piece_moved_texture) ;

    for(int i = 0 ; i < dead_table ; i ++)
    {
        SDL_FreeSurface(dead_table_count_surface[i]) ;
        SDL_DestroyTexture(dead_table_count_texture[i]) ;
    }

    TTF_CloseFont(font) ;
    TTF_CloseFont(font_2) ;
    Mix_FreeMusic(music);
    Mix_CloseAudio() ;
    SDL_FreeCursor(cursor1) ;
    SDL_FreeCursor(cursor2) ;
    TTF_Quit() ;
    IMG_Quit() ;
    SDL_DestroyRenderer(my_render) ;
    SDL_DestroyWindow(window) ;
    SDL_Quit() ;
}
