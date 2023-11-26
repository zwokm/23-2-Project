#include <stdio.h>
#include <windows.h>
#include <conio.h>

#define LEFT 75
#define RIGHT 77
#define UP 72
#define DOWN 80
#define ENTER 0xd
#define ESC 0x1b

#define COL				GetStdHandle(STD_OUTPUT_HANDLE)
#define WHITE			SetConsoleTextAttribute(COL, 0x0007);
#define GRAY			SetConsoleTextAttribute(COL, 0x0008);
#define BLUE			SetConsoleTextAttribute(COL, 0x0009);
#define RED				SetConsoleTextAttribute(COL, 0x000c);
#define YELLOW			SetConsoleTextAttribute(COL, 0x000e);

void map_ptf(int k);
void menu();
void move_menu();

struct cursor
{
	int by, by_last;
	int cx, cy;
};
struct game_source
{
	int score, score_ptf;
	int diff, life;
	int begin_menu, menu_change, start_re;
	char diff_ch[5];
};

struct cursor cursor1 = { 17,17,12,18 };
struct game_source game_source1 = { 0,1000,0,5,0,0,0 };

char player[2] = "웃";

int main()
{
	system("title 별 피하기");
	system("mode con: cols=50 lines=27");//지역변수 
}

int map[2][20][12] =
{ {
		1,1,1,1,1,1,1,1,1,1,1,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,1,1,1,1,1,1,1,1,1,1,1

},
{
		1,1,1,1,1,1,1,1,1,1,1,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,2,0,0,0,0,0,0,0,1,
		1,0,2,0,2,0,2,2,2,0,0,1,
		1,0,0,0,0,0,0,0,2,0,0,1,
		1,2,2,2,2,2,2,2,2,0,0,1,
		1,0,0,0,0,0,2,0,0,0,0,1,
		1,0,0,2,0,0,2,2,2,0,0,1,
		1,0,2,0,2,0,0,0,0,0,0,1,
		1,0,0,2,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,0,0,0,0,0,0,0,0,0,0,1,
		1,1,1,1,1,1,1,1,1,1,1,1
} };

void gotoxy(int x, int y)
{
	COORD Cur;
	Cur.X = x;
	Cur.Y = y;
	SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), Cur);
}
void map_ptf(int k)
{
	int i, j;

	for (i = 0; i < 20; i++)
	{
		for (j = 0; j < 12; j++)
		{
			switch (map[k][i][j])
			{
			case 0:
				printf("  "); break;
			case 1:
				printf("ㅁ"); break;

			}
		}
		putchar('\n');
	}
}

void move_menu()
{
	int kb1 = getch();
	switch (kb1)
	{
	case DOWN:
		if (cursor1.by + 3 <= 23)
		{
			gotoxy(16, cursor1.by);
			printf("  ");
			gotoxy(32, cursor1.by);
			printf("  ");
			cursor1.by += 3;
			gotoxy(16, cursor1.by);
			printf("▷");
			gotoxy(32, cursor1.by);
			printf("◁");
		}
		break;
	case UP:
		if (cursor1.by - 3 >= 17)
		{
			gotoxy(16, cursor1.by);
			printf("  ");
			gotoxy(32, cursor1.by);
			printf("  ");
			cursor1.by -= 3;
			gotoxy(16, cursor1.by);
			printf("▷");
			gotoxy(32, cursor1.by);
			printf("◁");
		}
		break;
	case ENTER:
		if (game_source1.menu_change == 0 && cursor1.by == 17)
			game_source1.begin_menu = 1;
		else if (game_source1.menu_change == 0 && cursor1.by == 20)
		{
			//system("cls");
			game_source1.menu_change = 1;
		}
		else if (game_source1.menu_change == 0 && cursor1.by == 23)
			exit(0);

		break;
	}
}

void menu()
{
	YELLOW printf("\n\n★  ★  ★  ★  ★  ★  ★  ★  ★  ★  ★  ★  ★  ★  ★\n");
	YELLOW printf("★  ★  ★  ★  ★  ★ 별 피하기 ★  ★  ★  ★  ★  ★  \n");
	YELLOW printf("★  ★  ★  ★  ★  ★  ★  ★  ★  ★  ★  ★  ★  ★  ★\n");

	getch();
}