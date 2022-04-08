#include <stdio.h>
#include <stdlib.h>

typedef struct _node {
    int element;
    char grounName;
    struct _node * prevElement;
    struct _node * prevGroup;
    struct _node * nextElement;
    struct _node * nextGroup;
} Node;

typedef Node Groups;
typedef Node Elements;

void initShare(Elements eHeader[], Elements eTrailer[], Groups gHeader[], Groups gTrailer[], int NE, int NG) {
    int i = 0;
    for (i = 0; i < NE; i++) {
        gHeader[i].nextElement = &gTrailer[i];
        gTrailer[i].prevElement = &gHeader[i];
        gHeader[i].prevElement = NULL;
        gTrailer[i].nextElement = NULL;
    }
    for (i = 0; i < NG; i++) {
        eHeader[i].nextGroup = &eTrailer[i];
        eTrailer[i].prevGroup = &eHeader[i];
        eHeader[i].prevGroup = NULL;
        eTrailer[i].nextGroup = NULL;
    }
}

void traverseShareElements(Groups g[], char groupName) {
    int index = groupName - 'A';
    Groups * cur = g[index].nextElement;
    if (cur->nextElement == NULL) {
        printf("0\n");
        return;
    }
    while (cur->nextElement != NULL) {
        printf("%d ", cur->element);
        cur = cur->nextElement;
    }
    printf("\n");
}

void traverseShareGroups(Elements e[], int element) {
    Elements * cur = e[element - 1].nextGroup;
    if (cur->nextGroup == NULL) {
        printf("0\n");
        return;
    }
    while (cur->nextGroup != NULL) {
        printf("%c ", cur->grounName);
        cur = cur->nextGroup;
    }
    printf("\n");
}

void addShare(Elements e[], Groups g[], int element, char groupName) {
    Node * new = (Node*)malloc(sizeof(Node));
    new->element = element;
    new->grounName = groupName;
    if (new == NULL)
        return;
    
    int index = groupName - 'A';
    Groups * Gcur = &g[index];
    while (Gcur->nextElement != NULL)
        Gcur = Gcur->nextElement;
    
    Elements * Ecur = &e[element - 1];
    while (Ecur->nextGroup != NULL)
        Ecur = Ecur->nextGroup;
    
    new->prevElement = Gcur->prevElement;
    new->nextElement = Gcur;
    Gcur->prevElement->nextElement = new;
    Gcur->prevElement = new;
    
    new->prevGroup = Ecur->prevGroup;
    new->nextGroup = Ecur;
    Ecur->prevGroup->nextGroup = new;
    Ecur->prevGroup = new;
    
    printf("OK\n");
}

void removeShare(Elements e[], Groups g[], int element, char groupName) {
    int index = groupName - 'A';
    Node * delNode = g[index].nextElement;
    Groups * Gcur = g[index].nextElement;
    while (Gcur->nextElement != NULL) {
        if (Gcur->element == element)
            break;
        Gcur = Gcur->nextElement;
    }
    
    Elements * Ecur = e[element - 1].nextGroup;
    while (Ecur->nextGroup != NULL) {
        if (Ecur->grounName == groupName)
            break;
        Ecur = Ecur->nextGroup;
    }
    
    delNode = Gcur;
    Gcur->prevElement->nextElement = Gcur->nextElement;
    Gcur->nextElement->prevElement = Gcur->prevElement;
    Ecur->prevGroup->nextGroup = Ecur->nextGroup;
    Ecur->nextGroup->prevGroup = Ecur->prevGroup;
    free(delNode);
    printf("OK\n");
}

int main() {
    Groups gHeader[5] = { 0 }, gTrailer[5] = { 0 };
    Elements eHeader[4] = { 0 }, eTrailer[4] = { 0 };
    int element;
    int stop = 0;
    char groupName;
    char cmd;
    
    initShare(eHeader, eTrailer, gHeader, gTrailer, 5, 4);
    
    while (!stop) {
        scanf("%c", &cmd);
        getchar();
        switch (cmd) {
            case 'a':
                scanf("%d %c", &element, &groupName);
                getchar();
                addShare(eHeader, gHeader, element, groupName);
                break;
            case 'r':
                scanf("%d %c", &element, &groupName);
                getchar();
                removeShare(eHeader, gHeader, element, groupName);
                break;
            case 'e':
                scanf("%c", &groupName);
                getchar();
                traverseShareElements(gHeader, groupName);
                break;
            case 'g':
                scanf("%d", &element);
                getchar();
                traverseShareGroups(eHeader, element);
                break;
            case 'q':
                stop = 1;
                break;
        }
    }
    
    return 0;
}
