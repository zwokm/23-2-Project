# 23-2-Project
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

char player[4] = "��";

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
	system("title �� ���ϱ�");
	system("mode con: cols=50 lines=27");//�������� 
	int kb, kb1, n[20], ran[20], swit[19];
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
				WHITE printf(" �����ϱ�");
			}
			else
			{
				RED printf(" �ٽ��ϱ�");
			}

			gotoxy(20, 20);
			WHITE printf("  ������");
			WHITE
				gotoxy(16, cursor1.by);
			printf("��");
			gotoxy(32, cursor1.by);
			printf("��");

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

		map_ptf(0);//����� 

		gotoxy(cursor1.cx, cursor1.cy);
		BLUE printf("%s", player);

		/*���� ����ȭ�� ������ �����*/
		gotoxy(28, 6);
		GRAY printf("���� : ");
		gotoxy(28, 8);
		GRAY printf("��� : ");
		gotoxy(28, 9);
		GRAY printf("�¸� ���� :");
		gotoxy(40, 9);
		printf("%d��", game_source1.score_ptf);
		gotoxy(28, 11);
		GRAY printf("����Ű");
		gotoxy(28, 13);
		GRAY printf("��  ��");

		for (j = 0; j < 20; j++)/*�浹 Ȯ�� -> ����,���ھ� ����*/
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

		gotoxy(35, 6); /*����,���� ������ �ٲ�� �κ���*/
		RED printf("%-4d", game_source1.score);
		gotoxy(35, 8);
		RED printf("%d", game_source1.life);
		gotoxy(48, 26);


		if (game_source1.score > game_source1.score_ptf)//�¸� 
			break;
		else if (game_source1.life == 0)//�й�
			break;

		if (game_source1.score > game_source1.score_ptf)//�¸�
		{
			gotoxy(0, 0);
			map_ptf(1);
			gotoxy(5, 3);
			RED printf("ESC - ���ư���");
			do
			{
				kb1 = getch();
			} while (kb1 != ESC);
			system("cls");
			game_source1.start_re = 1;
		}
		else if (game_source1.life == 0 && game_source1.score < game_source1.score_ptf)//�й�
		{
			gotoxy(5, 10);
			RED printf("  ���� ����");
			gotoxy(5, 11);
			printf("ESC - ���ư���");
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
				printf("��"); break;

			}
		}
		putchar('\n');
	}
}

void fall_star(int* n, int* ran)
{

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
			printf("��");
			gotoxy(32, cursor1.by);
			printf("��");
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
			printf("��");
			gotoxy(32, cursor1.by);
			printf("��");
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
	YELLOW printf("\n\n��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��\n");
	YELLOW printf("��  ��  ��  ��  ��  �� �� ���ϱ� ��  ��  ��  ��  ��  ��  \n");
	YELLOW printf("��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��  ��\n");

	getch();
}