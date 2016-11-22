#include <mygba.h>
#include "modules.h"
#include "gfx/ball.pal.c"
#include "gfx/ball.raw.c"
#include "gfx/block.pal.c"
#include "gfx/block.raw.c"

#include "gfx/yuna.pal.c"
#include "gfx/yuna.raw.c"
#include "gfx/yuna.map.c"
#include "gfx/eva.pal.c"
#include "gfx/eva.raw.c"
#include "gfx/eva.map.c"
#include "gfx/ae86.pal.c"
#include "gfx/ae86.raw.c"
#include "gfx/ae86.map.c"
#include "gfx/gameover.pal.c"
#include "gfx/gameover.raw.c"
#include "gfx/gameover.map.c"
#include "gfx/youwin.pal.c"
#include "gfx/youwin.raw.c"
#include "gfx/youwin.map.c"
#include "gfx/staff.pal.c"
#include "gfx/staff.raw.c"
#include "gfx/staff.map.c"
#include "gfx/opening.pal.c"
#include "gfx/opening.raw.c"
#include "gfx/opening.map.c"
#include "gfx/option.pal.c"
#include "gfx/option.raw.c"
#include "gfx/option.map.c"

u8 i;
u8 vbl_count=0;
u8 spd=1;           //Must be 1,2,4,8,16 !!!!,control the speed,in move_ball
u8 spd2=1;          //the no of vbl_count, also control the speed,total speed=spd/spd2
u8 breakflag;     //when 0,break loop

u8 bgno=1;
u8 spdno=1;
u8 musicno=1;

u8 optionno=1;      // can be used in opening and option


u8 frames=0;        //when reach 16, read iniput,must set 0 when start
u8 length=2;        // the length of the snake

u8 ball[64];
u8 x[64];          //Position,[0]is a random ball
u8 y[64];
u8 direct[64];     //direct[0] is special,the direct of current input
u8 randomx;
u8 randomy;

u16 score=0;
u16 hiscore=2000;
u16 maxscore=2000;

u8 missionno=0;
u8 block[32];
u8 blockx[32];
u8 blocky[32];
u8 blocklength=0;

void vblFunc(void);
void vblFunc_opening(void);
void vblFunc_option(void);
void vblFunc_pause(void);

void setspd(void);
void setbg(void);
void setmusic(void);

void newball(void);     //generate a new ball ball[0]
void move_ball(void);
void update_ball(void);
void buttons(void);
void chdirect(void);        //change direct every frame,frame=spd2
void judge(void);
void lastball(void);   //save the last ball in ball [63],in order to put the new ball when eat the random ball
void drawscore(void);

void pause(void);
void gameover(void);
void youwin(void);
void newgame(void);
void opening(void);
void option(void);
void staff(void);

void mission1(void);
void mission2(void);
void mission3(void);
void mission4(void);
void mission5(void);
void mission(void);
void chmission(void);
void youwin_mission(void);
void vblFunc_mission(void);
void judge_mission(void);

int main()
{
    ham_Init();
    while(1)
    {
        opening();
    }
    return 0;
}
void newgame(void)
{
    ham_ResetAll();
    u8 i;
    x[1]=16; x[2]=0;
    y[1]=0;  y[2]=0;
    direct[0]=3;
    direct[1]=3;
    direct[2]=3;
    length=2;
    frames=0;
    vbl_count=0;
    breakflag=1;
    
    // set the speed
    setspd();
    // set the bg
    setbg();
    // set the music
    setmusic();

    // Display the object
    ham_LoadObjPal((void*)ball_Palette,256);

    //initialize the snake
    ball[0] = ham_CreateObj((void*)ball_Bitmap,
        0,1,OBJ_MODE_NORMAL,1,0,0,0,0,0,0,
        x[0],y[0]);
    for(i=1;i<=63;i++) ham_CloneObj(0,0,0);
    for(i=0;i<=63;i++) ham_SetObjVisible(i,0);
    ham_SetObjVisible(1,1);
    ham_SetObjVisible(2,1);

    //initialize the first random ball
    srand(0x87654321);
    randomx=rand()%9;
    randomy=rand()%9;
    x[0]=randomx*16;
    y[0]=randomy*16;
    ham_SetObjX(0,x[0]);
    ham_SetObjY(0,y[0]);
    ham_SetObjVisible(0,1);
    
    //initialize the score screen]
    ham_DrawText(21,3,"SCORE:");
    ham_DrawText(21,6,"HISCORE:");
    ham_DrawText(21,9,"MAXSCORE:");
    ham_DrawText(21,15,"MISSION:");
    ham_DrawText(21,17,"SPEED:");

    //start the int
    ham_StartIntHandler(INT_TYPE_VBL,&vblFunc);

    while(1){}

}

void setspd(void)
{
    if(spdno==1)  {spd=2;spd2=3;}
    if(spdno==2)  {spd=1;spd2=1;}
    if(spdno==3)  {spd=4;spd2=3;}
    if(spdno==4)  {spd=2;spd2=1;}
    if(spdno==5)  {spd=8;spd2=3;}
}
void setmusic(void)
{
    kragInit(KRAG_INIT_STEREO);
    ham_StartIntHandler(INT_TYPE_TIM1,&kradInterrupt);
    if(musicno==1)  krapPlay(&mod_song1,KRAP_MODE_LOOP,1);
    if(musicno==2)  krapPlay(&mod_song2,KRAP_MODE_LOOP,1);
    if(musicno==3)  krapPlay(&mod_song3,KRAP_MODE_LOOP,1);
    if(musicno==4)  krapPlay(&mod_song4,KRAP_MODE_LOOP,1);
    if(musicno==5)  krapPlay(&mod_song5,KRAP_MODE_LOOP,1);
    if(musicno==6)  krapPlay(&mod_song6,KRAP_MODE_LOOP,1);
}
void setbg(void)
{
    switch(bgno)
    {
        case 1:
        {
            ham_ResetAll();
            map_fragment_info_ptr bg_yuna;

            // Initialize the text display system
            ham_InitText(2);

            // Setup the background mode
            ham_SetBgMode(1);

            // Initialize the palettes
            ham_LoadBGPal((void*)yuna_Palette,256);
	
            // Setup the tileset for our image
            ham_bg[0].ti = ham_InitTileSet(
                    (void*)yuna_Tiles,
                    SIZEOF_16BIT(yuna_Tiles),1,1);
            // Setup the map for our image
            ham_bg[0].mi = ham_InitMapEmptySet(3,0);
            bg_yuna = ham_InitMapFragment(
                (void*)yuna_Map,30,20,0,0,30,20,0);
            ham_InsertMapFragment(bg_yuna,0,0,0);
	
            // Display the background
            ham_InitBg(0,1,1,0);

            break;
        }
        case 2:
        {
            ham_ResetAll();
            map_fragment_info_ptr bg_eva;

            // Initialize the text display system
            ham_InitText(2);

            // Setup the background mode
            ham_SetBgMode(1);

            // Initialize the palettes
            ham_LoadBGPal((void*)eva_Palette,256);
	
            // Setup the tileset for our image
            ham_bg[0].ti = ham_InitTileSet(
                    (void*)eva_Tiles,
                    SIZEOF_16BIT(eva_Tiles),1,1);
            // Setup the map for our image
            ham_bg[0].mi = ham_InitMapEmptySet(3,0);
            bg_eva = ham_InitMapFragment(
                (void*)eva_Map,30,20,0,0,30,20,0);
            ham_InsertMapFragment(bg_eva,0,0,0);
	
            // Display the background
            ham_InitBg(0,1,1,0);
            break;
        }
        case 3:
        {
            ham_ResetAll();
            map_fragment_info_ptr bg_ae86;

            // Initialize the text display system
            ham_InitText(2);

            // Setup the background mode
            ham_SetBgMode(1);

            // Initialize the palettes
            ham_LoadBGPal((void*)ae86_Palette,256);
	
            // Setup the tileset for our image
            ham_bg[0].ti = ham_InitTileSet(
                    (void*)ae86_Tiles,
                    SIZEOF_16BIT(ae86_Tiles),1,1);
            // Setup the map for our image
            ham_bg[0].mi = ham_InitMapEmptySet(3,0);
            bg_ae86 = ham_InitMapFragment(
                (void*)ae86_Map,30,20,0,0,30,20,0);
            ham_InsertMapFragment(bg_ae86,0,0,0);
	
            // Display the background
            ham_InitBg(0,1,1,0);

            break;
        }
    }
}

void vblFunc(void)
{
    kramWorker();
    ++vbl_count;
    if(vbl_count==spd2)
    {
        ham_CopyObjToOAM();
        move_ball();
        update_ball();
        buttons();
        vbl_count=0;
    }
    if(frames==16)
    {
        drawscore();
        chdirect();
        judge();
        lastball();
        frames=0;
    }
}
void vblFunc_mission(void)
{
    kramWorker();
    ++vbl_count;
    if(vbl_count==spd2)
    {
        ham_CopyObjToOAM();
        move_ball();
        update_ball();
        buttons();
        vbl_count=0;
    }
    if(frames==16)
    {
        drawscore();
        chdirect();
        judge_mission();
        lastball();
        frames=0;
    }
}

void move_ball()
{
    u8 i;
    for(i=1;i<=length;i++)
    {
        if(direct[i]==0&&y[i]>=1)  y[i]-=spd;
        if(direct[i]==1&&y[i]<=143)  y[i]+=spd;
        if(direct[i]==2&&x[i]>=1)  x[i]-=spd;
        if(direct[i]==3&&x[i]<=143) x[i]+=spd;
    }
    frames+=spd;
    return;
}

void update_ball()
{
    u8 i;
    for(i=1;i<=length;i++)
    {
        ham_SetObjX(i,x[i]);
        ham_SetObjY(i,y[i]);
    }
    return;
}
void buttons(void)
{
    if(F_CTRLINPUT_UP_PRESSED)  direct[0]=0;
    if(F_CTRLINPUT_DOWN_PRESSED)    direct[0]=1;
    if(F_CTRLINPUT_LEFT_PRESSED)    direct[0]=2;
    if(F_CTRLINPUT_RIGHT_PRESSED)   direct[0]=3;
    if(F_CTRLINPUT_START_PRESSED)   pause();
    return;
}
void chdirect(void)
{
    u8 i;
    for(i=length;i>=1;i--)
    {
        direct[i]=direct[i-1];
    }
}
void judge(void)
{
    u8 i;
    // See if knuck the random ball
    if((x[1]==x[0])&&(y[1]==y[0]))
    {
        length++;
        x[length]=x[63];
        y[length]=y[63];
        direct[length]=direct[63];
        
        ham_SetObjVisible(length,1);
        newball();
    }
    // See if knuck itself
    for(i=2;i<=length;i++)
    {
        if((x[i]==x[1])&&(y[i]==y[1]))  {ham_StopIntHandler(INT_TYPE_VBL); ham_StopIntHandler(INT_TYPE_TIM1);gameover();}
    }
    // See if knuck the wall
    if(y[1]==0&&direct[1]==0) {ham_StopIntHandler(INT_TYPE_VBL); ham_StopIntHandler(INT_TYPE_TIM1);gameover();}
    if(y[1]==144&&direct[1]==1) {ham_StopIntHandler(INT_TYPE_VBL); ham_StopIntHandler(INT_TYPE_TIM1);gameover();}
    if(x[1]==0&&direct[1]==2) {ham_StopIntHandler(INT_TYPE_VBL); ham_StopIntHandler(INT_TYPE_TIM1);gameover();}
    if(x[1]==144&&direct[1]==3) {ham_StopIntHandler(INT_TYPE_VBL); ham_StopIntHandler(INT_TYPE_TIM1);gameover();}

    //see if complete
    if(score>=maxscore) {ham_StopIntHandler(INT_TYPE_VBL); ham_StopIntHandler(INT_TYPE_TIM1);youwin();}
}

void judge_mission(void)
{
    u8 i;
    // See if knuck the random ball
    if((x[1]==x[0])&&(y[1]==y[0]))
    {
        length++;
        x[length]=x[63];
        y[length]=y[63];
        direct[length]=direct[63];

        ham_SetObjVisible(length,1);
        newball();
    }
    // See if knuck itself
    for(i=2;i<=length;i++)
    {
        if((x[i]==x[1])&&(y[i]==y[1]))  {ham_StopIntHandler(INT_TYPE_VBL); ham_StopIntHandler(INT_TYPE_TIM1);gameover();}
    }
    // See if knuck the wall
    if(y[1]==0&&direct[1]==0) {ham_StopIntHandler(INT_TYPE_VBL); ham_StopIntHandler(INT_TYPE_TIM1);gameover();}
    if(y[1]==144&&direct[1]==1) {ham_StopIntHandler(INT_TYPE_VBL); ham_StopIntHandler(INT_TYPE_TIM1);gameover();}
    if(x[1]==0&&direct[1]==2) {ham_StopIntHandler(INT_TYPE_VBL); ham_StopIntHandler(INT_TYPE_TIM1);gameover();}
    if(x[1]==144&&direct[1]==3) {ham_StopIntHandler(INT_TYPE_VBL); ham_StopIntHandler(INT_TYPE_TIM1);gameover();}

    // See if knuck the blocks
    if(blocklength>0)
    {
        for(i=0;i<=blocklength-1;i++)
        {
            if((x[1]==blockx[i])&&(y[1]==blocky[i])) {ham_StopIntHandler(INT_TYPE_VBL); ham_StopIntHandler(INT_TYPE_TIM1);gameover();}
        }
    }
    if(score==maxscore) {ham_StopIntHandler(INT_TYPE_VBL); ham_StopIntHandler(INT_TYPE_TIM1);youwin_mission();}
}

void lastball()
{
    x[63]=x[length];
    y[63]=y[length];
    direct[63]=direct[length];
}


void newball(void)
{
    u8 i;
    randomx=rand()%9;
    randomy=rand()%9;
    x[0]=randomx*16;
    y[0]=randomy*16;
    for(i=1;i<=length;i++)
    {
        if(x[i]==x[0]&&y[i]==y[0])
        {
            randomx=rand()%9;
            randomy=rand()%9;
            x[0]=randomx*16;
            y[0]=randomy*16;
            i=1;
        }
    }
    for(i=0;i<=blocklength;i++)
    {
        if(blockx[i]==x[0]&&blocky[i]==y[0])
        {
            randomx=rand()%9;
            randomy=rand()%9;
            x[0]=randomx*16;
            y[0]=randomy*16;
            i=1;
        }
    }
    ham_SetObjXY(0,x[0],y[0]);
    ham_SetObjVisible(0,1);
}

void gameover(void)
{
    ham_ResetAll();
    map_fragment_info_ptr bg_gameover;

    // Initialize the text display system
    ham_InitText(2);

    // Setup the background mode
    ham_SetBgMode(1);

    // Initialize the palettes
    ham_LoadBGPal((void*)gameover_Palette,256);
	
    // Setup the tileset for our image
    ham_bg[0].ti = ham_InitTileSet(
            (void*)gameover_Tiles,
            SIZEOF_16BIT(gameover_Tiles),1,1);
    // Setup the map for our image
    ham_bg[0].mi = ham_InitMapEmptySet(3,0);
    bg_gameover = ham_InitMapFragment(
        (void*)gameover_Map,30,20,0,0,30,20,0);
    ham_InsertMapFragment(bg_gameover,0,0,0);
	
    // Display the background
    ham_InitBg(0,1,1,0);

    while(1)
    {
        if(F_CTRLINPUT_A_PRESSED|| F_CTRLINPUT_B_PRESSED|| F_CTRLINPUT_START_PRESSED)
        {
            spdno=1;bgno=1;musicno=1;
            score=0;hiscore=2000;maxscore=2000;
            length=2;blocklength=0;
            missionno=0;
            opening();
        }
    }
    
}


void youwin(void)
{
    ham_ResetAll();
    map_fragment_info_ptr bg_youwin;

    // Initialize the text display system
    ham_InitText(2);

    // Setup the background mode
    ham_SetBgMode(1);

    // Initialize the palettes
    ham_LoadBGPal((void*)youwin_Palette,256);
	
    // Setup the tileset for our image
    ham_bg[0].ti = ham_InitTileSet(
            (void*)youwin_Tiles,
            SIZEOF_16BIT(youwin_Tiles),1,1);
    // Setup the map for our image
    ham_bg[0].mi = ham_InitMapEmptySet(3,0);
    bg_youwin = ham_InitMapFragment(
        (void*)youwin_Map,30,20,0,0,30,20,0);
    ham_InsertMapFragment(bg_youwin,0,0,0);
	
    // Display the background
    ham_InitBg(0,1,1,0);

    while(1)
    {
        if(F_CTRLINPUT_A_PRESSED|| F_CTRLINPUT_B_PRESSED|| F_CTRLINPUT_START_PRESSED)
        {
            spdno=1;bgno=1;musicno=1;
            score=0;hiscore=2000;maxscore=2000;
            length=2;blocklength=0;
            missionno=0;
            opening();
        }
    }
}
void youwin_mission(void)
{
    ham_ResetAll();
    map_fragment_info_ptr bg_youwin;

    // Initialize the text display system
    ham_InitText(2);

    // Setup the background mode
    ham_SetBgMode(1);

    // Initialize the palettes
    ham_LoadBGPal((void*)youwin_Palette,256);
	
    // Setup the tileset for our image
    ham_bg[0].ti = ham_InitTileSet(
            (void*)youwin_Tiles,
            SIZEOF_16BIT(youwin_Tiles),1,1);
    // Setup the map for our image
    ham_bg[0].mi = ham_InitMapEmptySet(3,0);
    bg_youwin = ham_InitMapFragment(
        (void*)youwin_Map,30,20,0,0,30,20,0);
    ham_InsertMapFragment(bg_youwin,0,0,0);
	
    // Display the background
    ham_InitBg(0,1,1,0);

    while(1)
    {
        if(F_CTRLINPUT_A_PRESSED|| F_CTRLINPUT_B_PRESSED|| F_CTRLINPUT_START_PRESSED)
        {
            spdno=1;bgno=1;musicno=1;
            score=0;hiscore=2000;maxscore=2000;
            length=2;blocklength=0;
            chmission();
        }
    }
}

void opening(void)
{
    ham_ResetAll();
    map_fragment_info_ptr bg_opening;
    vbl_count=2;                //this is different ,it decrease ,when reach 1 ,judge
    optionno=1;

    // Initialize the text display system
    ham_InitText(2);

    // Setup the background mode
    ham_SetBgMode(1);

    // Initialize the palettes
    ham_LoadBGPal((void*)opening_Palette,256);
	
    // Setup the tileset for our image
    ham_bg[0].ti = ham_InitTileSet(
            (void*)opening_Tiles,
            SIZEOF_16BIT(opening_Tiles),1,1);
    // Setup the map for our image
    ham_bg[0].mi = ham_InitMapEmptySet(3,0);
    bg_opening = ham_InitMapFragment(
        (void*)opening_Map,30,20,0,0,30,20,0);
    ham_InsertMapFragment(bg_opening,0,0,0);
	
    // Display the background
    ham_InitBg(0,1,1,0);

    // Draw some text

    ham_DrawText(8,10,"NEW GAME");
    ham_DrawText(8,12,"MISSION");
    ham_DrawText(8,14,"OPTION");
    ham_DrawText(8,16,"STAFF");

    ham_LoadObjPal((void*)ball_Palette,256);
    ball[0] = ham_CreateObj((void*)ball_Bitmap,
                            0,1,OBJ_MODE_NORMAL,1,0,0,0,0,0,0,
                            x[0],y[0]);
    ham_SetObjXY(0,40,80);
    ham_StartIntHandler(INT_TYPE_VBL,(void*)&vblFunc_opening);

    while(1){}

}
void vblFunc_opening()
{
    --vbl_count;
    if(vbl_count==1)
    {
        vbl_count=2;
        ham_CopyObjToOAM();
        if((F_CTRLINPUT_UP_PRESSED)&&optionno>1)
        {
            optionno--;
            if(optionno==1) {x[0]=40;y[0]=80; }
            if(optionno==2) {x[0]=40;y[0]=96;}
            if(optionno==3) {x[0]=40;y[0]=112;}
            if(optionno==4) {x[0]=40;y[0]=128;}
            ham_SetObjXY(0,x[0],y[0]);
            ham_CopyObjToOAM();
            vbl_count=10;
        }
        if((F_CTRLINPUT_DOWN_PRESSED)&&optionno<4)
        {
            optionno++;
            if(optionno==1) {x[0]=40;y[0]=80; }
            if(optionno==2) {x[0]=40;y[0]=96;}
            if(optionno==3) {x[0]=40;y[0]=112;}
            if(optionno==4) {x[0]=40;y[0]=128;}
            ham_SetObjXY(0,x[0],y[0]);
            ham_CopyObjToOAM();
            vbl_count=10;
        }

        if(F_CTRLINPUT_A_PRESSED|| F_CTRLINPUT_B_PRESSED|| F_CTRLINPUT_START_PRESSED)
        {
            if(optionno==1) { ham_StopIntHandler(INT_TYPE_VBL);newgame();}
            if(optionno==2) { ham_StopIntHandler(INT_TYPE_VBL);chmission();}
            if(optionno==3) { ham_StopIntHandler(INT_TYPE_VBL);option();}
            if(optionno==4) { ham_StopIntHandler(INT_TYPE_VBL);staff();}
        }
    }
}
void option(void)
{
    ham_ResetAll();
    map_fragment_info_ptr bg_option;
    vbl_count=2;
    optionno=1;
    spdno=1;
    bgno=1;
    musicno=1;

    // Initialize the text display system
    ham_InitText(2);

    // Setup the background mode
    ham_SetBgMode(1);

    // Initialize the palettes
    ham_LoadBGPal((void*)option_Palette,256);
	
    // Setup the tileset for our image
    ham_bg[0].ti = ham_InitTileSet(
            (void*)option_Tiles,
            SIZEOF_16BIT(option_Tiles),1,1);
    // Setup the map for our image
    ham_bg[0].mi = ham_InitMapEmptySet(3,0);
    bg_option = ham_InitMapFragment(
        (void*)option_Map,30,20,0,0,30,20,0);
    ham_InsertMapFragment(bg_option,0,0,0);
	
    // Display the background
    ham_InitBg(0,1,1,0);
    
    // Draw option text
    ham_DrawText(8,10,"SPEED");
    ham_DrawText(16,10,"%d",spdno);
    ham_DrawText(8,13,"SCREEN");
    ham_DrawText(16,13,"%d",bgno);
    ham_DrawText(8,16,"MUSIC");
    ham_DrawText(16,16,"%d",musicno);
    ham_DrawText(0,18,"PRESS LEFT OR RIGHT TO CHANGE");
    ham_DrawText(0,19,"THE VALUE,AND A OR B TO EXIT");

    // Display the ball
    ham_LoadObjPal((void*)ball_Palette,256);
    ball[0] = ham_CreateObj((void*)ball_Bitmap,
                            0,1,OBJ_MODE_NORMAL,1,0,0,0,0,0,0,
                            x[0],y[0]);
    ham_SetObjXY(0,40,80);
    ham_StartIntHandler(INT_TYPE_VBL,(void*)&vblFunc_option);

    while(1)
    {
    }
}
void vblFunc_option(void)
{
    ham_CopyObjToOAM();
    --vbl_count;
    ham_DrawText(16,10,"%d",spdno);
    ham_DrawText(16,13,"%d",bgno);
    ham_DrawText(16,16,"%d",musicno);
    
    if(vbl_count==1)
    {
        vbl_count=2;
        if((F_CTRLINPUT_UP_PRESSED)&&optionno>1)
        {
            optionno--;
            if(optionno==1) {x[0]=40;y[0]=80; }
            if(optionno==2) {x[0]=40;y[0]=104;}
            if(optionno==3) {x[0]=40;y[0]=128;}
            ham_SetObjXY(0,x[0],y[0]);
            vbl_count=10;
        }
        if((F_CTRLINPUT_DOWN_PRESSED)&&optionno<3)
        {
            optionno++;
            if(optionno==1) {x[0]=40;y[0]=80; }
            if(optionno==2) {x[0]=40;y[0]=104;}
            if(optionno==3) {x[0]=40;y[0]=128;}
            ham_SetObjXY(0,x[0],y[0]);
            vbl_count=10;
        }
        if(F_CTRLINPUT_LEFT_PRESSED)
        {
            if(optionno==1&&spdno>1)    spdno--;
            if(optionno==2&&bgno>1)     bgno--;
            if(optionno==3&&musicno>1)  musicno--;
            vbl_count=10;
        }
        if(F_CTRLINPUT_RIGHT_PRESSED)
        {
            if(optionno==1&&spdno<5)    spdno++;
            if(optionno==2&&bgno<3)     bgno++;
            if(optionno==3&&musicno<7)  musicno++;
            vbl_count=10;
        }


        if(F_CTRLINPUT_A_PRESSED|| F_CTRLINPUT_B_PRESSED|| F_CTRLINPUT_START_PRESSED)
        {
            ham_StopIntHandler(INT_TYPE_VBL);
            ham_StopIntHandler(INT_TYPE_TIM1);
            opening();
        }
    }
}
void staff()
{
    ham_ResetAll();
    map_fragment_info_ptr bg_staff;
    vbl_count=2;                //this is different ,it decrease ,when reach 1 ,judge
    optionno=1;

    // Initialize the text display system
    ham_InitText(2);

    // Setup the background mode
    ham_SetBgMode(1);

    // Initialize the palettes
    ham_LoadBGPal((void*)staff_Palette,256);
	
    // Setup the tileset for our image
    ham_bg[0].ti = ham_InitTileSet(
            (void*)staff_Tiles,
            SIZEOF_16BIT(staff_Tiles),1,1);
    // Setup the map for our image
    ham_bg[0].mi = ham_InitMapEmptySet(3,0);
    bg_staff = ham_InitMapFragment(
        (void*)staff_Map,30,20,0,0,30,20,0);
    ham_InsertMapFragment(bg_staff,0,0,0);
	
    // Display the background
    ham_InitBg(0,1,1,0);
    while(1)
    {
        if(F_CTRLINPUT_A_PRESSED|| F_CTRLINPUT_B_PRESSED|| F_CTRLINPUT_START_PRESSED)
        {
            ham_StopIntHandler(INT_TYPE_VBL);
            ham_StopIntHandler(INT_TYPE_TIM1);
            opening();
        }
    }
    
}
void pause(void)
{
    krapPause(1);
    ham_StopIntHandler(INT_TYPE_VBL);
    ham_DrawText(22,17,"PAUSE");

    ham_StartIntHandler(INT_TYPE_VBL,(void*)&vblFunc_pause);
    vbl_count=0;

    while(1)
    {
        if(vbl_count>=10)
        {
            if(F_CTRLINPUT_START_PRESSED)
            {
                ham_DrawText(22,17,"     ");
                ham_StopIntHandler(INT_TYPE_VBL);
                ham_StartIntHandler(INT_TYPE_VBL,&vblFunc);
                krapUnpause();
                break;
            }
            vbl_count=0;
        }
    }

}
void vblFunc_pause(void)
{
    ++vbl_count;

}
void drawscore(void)
{
    score=(length-2)*100;
    if(score>=hiscore) hiscore=score;
    ham_DrawText(21,4,"   ");
    ham_DrawText(21,4,"%d",score);
    ham_DrawText(21,7,"   ");
    ham_DrawText(21,7,"%d",hiscore);
    ham_DrawText(21,10,"   ");
    ham_DrawText(21,10,"%d",maxscore);
    ham_DrawText(21,16,"   ");
    ham_DrawText(21,16,"%d",missionno);
    ham_DrawText(21,18,"   ");
    ham_DrawText(21,18,"%d",spdno);
    
}
void mission1(void)
{
    u8 i;
    spdno=2;bgno=1;musicno=1;maxscore=2000;
    for(i=0;i<=63;i++){blockx[i]=0;blocky[i]=0;}

    blocklength=0;
}
void mission2(void)
{
    spdno=2;bgno=2;musicno=2;maxscore=2000;
    for(i=0;i<=63;i++){blockx[i]=0;blocky[i]=0;}
    blockx[0]=80;blocky[0]=48;
    blockx[1]=80;blocky[1]=64;
    blockx[2]=80;blocky[2]=80;
    blockx[3]=80;blocky[3]=96;
    blocklength=4;
}
void mission3(void)
{
    spdno=3;bgno=3;musicno=3;maxscore=1800;
    for(i=0;i<=63;i++){blockx[i]=0;blocky[i]=0;}
    blockx[0]=16;blocky[0]=48;
    blockx[1]=16;blocky[1]=64;
    blockx[2]=16;blocky[2]=80;
    blockx[3]=16;blocky[3]=96;
    blockx[4]=128;blocky[4]=48;
    blockx[5]=128;blocky[5]=64;
    blockx[6]=128;blocky[6]=80;
    blockx[7]=128;blocky[7]=96;
    blocklength=8;
}
void mission4(void)
{
    spdno=3;bgno=1;musicno=4;maxscore=2500;
    for(i=0;i<=63;i++){blockx[i]=0;blocky[i]=0;}
    blockx[0]=48;blocky[0]=48;
    blockx[1]=96;blocky[1]=48;
    blockx[2]=48;blocky[2]=96;
    blockx[3]=96;blocky[3]=96;
    blocklength=4;
}
void mission5(void)
{
    spdno=4;bgno=2;musicno=5;maxscore=2500;
    for(i=0;i<=63;i++){blockx[i]=0;blocky[i]=0;}
    blockx[0]=16;blocky[0]=48;
    blockx[1]=16;blocky[1]=64;
    blockx[2]=16;blocky[2]=80;
    blockx[3]=16;blocky[3]=96;
    blockx[4]=128;blocky[4]=48;
    blockx[5]=128;blocky[5]=64;
    blockx[6]=128;blocky[6]=80;
    blockx[7]=128;blocky[7]=96;
    blocklength=8;
}
void mission(void)
{
    ham_ResetBg();ham_ResetObj();
    u8 i;
    x[1]=16; x[2]=0;
    y[1]=0;  y[2]=0;
    direct[0]=3;
    direct[1]=3;
    direct[2]=3;
    length=2;
    frames=0;
    vbl_count=0;
    breakflag=1;

    // set the speed
    setspd();
    // set the bg
    setbg();
    // set the music
    setmusic();

    // Display the object
    ham_LoadObjPal((void*)ball_Palette,256);

    //initialize the snake
    ball[0] = ham_CreateObj((void*)ball_Bitmap,
        0,1,OBJ_MODE_NORMAL,1,0,0,0,0,0,0,
        x[0],y[0]);
    for(i=1;i<=63;i++) ham_CloneObj(0,0,0);
    for(i=0;i<=63;i++) ham_SetObjVisible(i,0);
    ham_SetObjVisible(1,1);
    ham_SetObjVisible(2,1);

    //initialize the first random ball
    srand(0x87654321);
    randomx=rand()%9;
    randomy=rand()%9;
    x[0]=randomx*16;
    y[0]=randomy*16;
    ham_SetObjX(0,x[0]);
    ham_SetObjY(0,y[0]);
    ham_SetObjVisible(0,1);

    // initialize the blocks
    if(blocklength>0)
    {
        block[0] = ham_CreateObj((void*)block_Bitmap,
                            0,1,OBJ_MODE_NORMAL,1,0,0,0,0,0,0,
                            blockx[0],blocky[0]);
        for(i=1;i<=blocklength-1;i++) ham_CloneObj(64,0,0);
        for(i=0;i<=blocklength-1;i++)
        {
            ham_SetObjX(64+i,blockx[i]);
            ham_SetObjY(64+i,blocky[i]);
        }
    }

    //initialize the score screen]
    ham_DrawText(21,3,"SCORE:");
    ham_DrawText(21,6,"HISCORE:");
    ham_DrawText(21,9,"MAXSCORE:");
    ham_DrawText(21,15,"MISSION:");
    ham_DrawText(21,17,"SPEED:");

    //start the int
    ham_StartIntHandler(INT_TYPE_VBL,&vblFunc_mission);

    while(1){}

}
void chmission(void)
{
    ham_ResetAll();
    
    map_fragment_info_ptr bg_opening;
    vbl_count=0;

    // Initialize the text display system
    ham_InitText(2);

    // Setup the background mode
    ham_SetBgMode(1);

    // Initialize the palettes
    ham_LoadBGPal((void*)opening_Palette,256);
	
    // Setup the tileset for our image
    ham_bg[0].ti = ham_InitTileSet(
            (void*)opening_Tiles,
            SIZEOF_16BIT(opening_Tiles),1,1);
    // Setup the map for our image
    ham_bg[0].mi = ham_InitMapEmptySet(3,0);
    bg_opening = ham_InitMapFragment(
        (void*)opening_Map,30,20,0,0,30,20,0);
    ham_InsertMapFragment(bg_opening,0,0,0);
	
    // Display the background
    ham_InitBg(0,1,1,0);

    // Draw some text

    missionno++;

    ham_DrawText(10,10,"MISSION %d",missionno);
    while(1)
    {
        if(F_CTRLINPUT_A_PRESSED|| F_CTRLINPUT_B_PRESSED|| F_CTRLINPUT_START_PRESSED)
        {
            if(missionno==1) mission1();
            if(missionno==2) mission2();
            if(missionno==3) mission3();
            if(missionno==4) mission4();
            if(missionno==5) mission5();
            if(missionno==6) {missionno=0;opening();}
            mission();
        }
    }
}
