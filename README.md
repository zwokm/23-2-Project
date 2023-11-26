#include <stdio.h>
#include <stdlib.h>
#include <string.h>
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

void gotoxy(int x, int y);
void map_ptf(int k);
void move();
void fall_star(int* n, int* ran);
void menu();
void move_menu();
int player_dead(int* ran, int* n);

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

char player[4] = "웃";

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

int main()
{
	system("title 별 피하기");
	system("mode con: cols=50 lines=27");//지역변수 
	int kb, kb1, n[20], ran[20], swit[19], sleep_num;
	register int i, j;

	srand(time(NULL));

	while (1)
	{
		do
		{
			menu();
			gotoxy(20, 17);
			if (game_source1.start_re == 0)
			{
				WHITE printf(" 시작하기");
			}
			else
			{
				RED printf(" 다시하기");
			}

			gotoxy(20, 20);
			WHITE printf("  끝내기");
			WHITE
				gotoxy(16, cursor1.by);
			printf("▷");
			gotoxy(32, cursor1.by);
			printf("◁");

			while (1)
			{
				move_menu();
				if (game_source1.begin_menu == 1)
					break;
			}

		} while (game_source1.begin_menu == 0);

		cursor1.by = 17, cursor1.cx = 12, cursor1.cy = 18, game_source1.score = 0, game_source1.life = 5, game_source1.start_re = 0, game_source1.begin_menu = 0;
		for (i = 0; i < 20; i++)
			n[i] = 1;

		gotoxy(0, 0);
		system("cls");

		map_ptf(0);//맵출력 

		gotoxy(cursor1.cx, cursor1.cy);
		BLUE printf("%s", player);

		/*최초 랜덤  초기화*/
		for (i = 0; i < 20; i++)
		{
			do
			{
				ran[i] = rand() % 19 + 2;
			} while ((ran[i] % 2 == 0 ? 1 : 0) == 0);
		}

		/*게임 실행화면 오른쪽 내용들*/
		gotoxy(28, 6);
		GRAY printf("점수 : ");
		gotoxy(28, 8);
		GRAY printf("목숨 : ");
		gotoxy(28, 9);
		GRAY printf("승리 조건 :");
		gotoxy(40, 9);
		printf("%d점", game_source1.score_ptf);
		gotoxy(28, 11);
		GRAY printf("조작키");
		gotoxy(28, 13);
		GRAY printf("←  →");

		if (game_source1.diff == 0)
			sleep_num = 22;

		int j_count = 0;

		/*게임 실행중*//*중요부분*/
		for (i = 1; ; i++) //무한루프
		{
			Sleep(sleep_num);

			if (kbhit())
				move();

			if (i % 100 == 0)
			{
				j_count++;
			}

			if (kbhit())
				move();

			for (j = 0; j <= j_count; j++)
			{
				fall_star(&n[j], &ran[j]);
			}

			if (kbhit())
				move();

			Sleep(sleep_num);

			if (kbhit())
				move();

			for (j = 0; j < 20; j++)/*충돌 확인 -> 생명,스코어 감소*/
			{
				int a;
				a = player_dead(&ran[j], &n[j]);
				if (a == 1)
				{
					if (game_source1.life == 0)
					{
						break;
					}
					else
					{
						game_source1.life--;
						game_source1.score -= 5;
					}
				}
			}

			if (kbhit())
				move();

			Sleep(sleep_num);

			if (kbhit())
				move();

			gotoxy(35, 6); /*점수,생명 실행중 바뀌는 부분임*/
			RED printf("%-4d", game_source1.score);
			gotoxy(35, 8);
			RED printf("%d", game_source1.life);
			gotoxy(48, 26);

			if (kbhit())
				move();

			Sleep(sleep_num);

			if (kbhit())
				move();

			if (game_source1.score > game_source1.score_ptf)//승리 
				break;
			else if (game_source1.life == 0)//패배
				break;
		}
		if (game_source1.score > game_source1.score_ptf)//승리
		{
			gotoxy(0, 0);
			map_ptf(1);
			gotoxy(5, 3);
			RED printf("ESC - 돌아가기");
			do
			{
				kb1 = getch();
			} while (kb1 != ESC);
			system("cls");
			game_source1.start_re = 1;
		}
		else if (game_source1.life == 0 && game_source1.score < game_source1.score_ptf)//패배
		{
			gotoxy(5, 10);
			RED printf("  게임 오버");
			gotoxy(5, 11);
			printf("ESC - 돌아가기");
			do
			{
				kb1 = getch();
			} while (kb1 != ESC);
			system("cls");
			game_source1.start_re = 1;
		}

	}
	printf("\n\n");

}

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

void fall_star(int* n, int* ran)
{
	if (*n < 19)
	{
		if (*n != 1)
		{
			gotoxy(*ran, *n - 1);
			printf("  ");
		}

		gotoxy(*ran, *n);
		YELLOW printf("★");

		(*n)++;
	}
	else
	{
		gotoxy(*ran, *n - 1);
		printf("  ");
		gotoxy(cursor1.cx, cursor1.cy);
		BLUE printf("%s", player);
		*n = 1;
		game_source1.score += 5;
		do
		{
			*ran = rand() % 19 + 2;
		} while (((*ran) % 2 == 0 ? 1 : 0) == 0);
	}
	gotoxy(48, 26);

}
void move()
{
	register int kb;

	kb = getch();

	switch (kb)
	{
	case LEFT:
		if (map[0][cursor1.cy][cursor1.cx / 2 - 1] == 0)
		{
			gotoxy(cursor1.cx, cursor1.cy);
			BLUE printf("  ");
			cursor1.cx -= 2;
			gotoxy(cursor1.cx, cursor1.cy);
			BLUE printf("%s", player);
			gotoxy(48, 26);
		}
		break;

	case RIGHT:
		if (map[0][cursor1.cy][cursor1.cx / 2 + 1] == 0)
		{
			gotoxy(cursor1.cx, cursor1.cy);
			BLUE printf("  ");
			cursor1.cx += 2;
			gotoxy(cursor1.cx, cursor1.cy);
			BLUE printf("%s", player);
			gotoxy(48, 26);
		}
		break;
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
int player_dead(int* ran, int* n)
{
	if (*ran == cursor1.cx && *n == 19)
	{
		gotoxy(cursor1.cx, cursor1.cy);
		RED printf("\a※");
		Sleep(300);
		return 1;
	}
	else
		return 0;
}