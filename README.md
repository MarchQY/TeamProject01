# TeamProject01  1.0
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_STR 50
#define DATA_FILE "books.dat"

typedef struct Book {
    char id[20];        // 编号
    char name[MAX_STR]; // 书名
    char location[MAX_STR]; // 位置
    struct Book* next;  // 链表指针
} Book;


Book* head = NULL; // 全局头指针

//  文件读取 (初始化)
void loadBooks() {
    FILE* fp = fopen(DATA_FILE, "rb");
    if (!fp) return; // 文件不存在则新建

    Book* temp;
    while (1) {
        temp = (Book*)malloc(sizeof(Book));
        if (fread(temp, sizeof(Book), 1, fp) != 1) 
		{
            free(temp);
            break;
        }					// 尝试读取一个结构体大小
        temp->next = NULL; // 修复指针（文件中存的指针是无效的，需重建链表）
        
        if (head == NULL) 
		{
            head = temp;
        } 
		else 
		{
            Book* p = head;
            while (p->next != NULL) p = p->next;
            p->next = temp;
        }			 	// 尾插法重建链表
    }
    fclose(fp);
    printf("加载了书籍数据。\n");
}

// 文件保存
void saveBooks() {
    FILE* fp = fopen(DATA_FILE, "wb");
    if (!fp) { printf("保存失败！\n"); return; }

    Book* p = head;
    while (p != NULL) {
        fwrite(p, sizeof(Book), 1, fp);
        p = p->next;
    }
    fclose(fp);
    printf("数据已保存。\n");
}

// 增加书籍
void addBook() {
    Book* newBook = (Book*)malloc(sizeof(Book));
    printf("输入编号: "); scanf("%s", newBook->id);
    printf("输入书名: "); scanf("%s", newBook->name);
    printf("输入位置: "); scanf("%s", newBook->location);
    
    newBook->next = head; // 头插法
    head = newBook;
}

// 删除书籍
void deleteBook() 
{
    char id[20];
    printf("输入要删除的编号: "); scanf("%s", id);
    
    Book *p = head, *prev = NULL;
    while (p != NULL) 
	{
        if (strcmp(p->id, id) == 0) 
		{
            if (prev == NULL) head = p->next; // 删除头节点
            else prev->next = p->next;
            free(p);
            printf("删除成功。\n");
            return;
        }
        prev = p;
        p = p->next;
    }
    printf("未找到该书籍。\n");
}

// 简单的菜单
void menu_v1() {
    int choice;
    while(1) {
        printf("\n--- V1.0 简易存储 ---\n1.添加 2.删除 3.保存 4.退出\n");
        scanf("%d", &choice);
        if(choice == 1) addBook();
        else if(choice == 2) deleteBook();
        else if(choice == 3) saveBooks();
        else if(choice == 4) { saveBooks(); exit(0); }
    }
}

int main() 
{
    loadBooks();
    menu_v1();
    return 0;
}
