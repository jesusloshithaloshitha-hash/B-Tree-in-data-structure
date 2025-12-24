#include <stdio.h>
#include <stdlib.h>
#define MAX 4   
struct BPlusTree {
    int keys[MAX];
    struct BPlusTree *child[MAX + 1];
    int isLeaf;
    int count;
    struct BPlusTree *next;
};
struct BPlusTree *root = NULL;
struct BPlusTree* createNode(int isLeaf) {
    struct BPlusTree *newNode =
        (struct BPlusTree*)malloc(sizeof(struct BPlusTree));
    newNode->isLeaf = isLeaf;
    newNode->count = 0;
    newNode->next = NULL;

    for (int i = 0; i < MAX + 1; i++)
        newNode->child[i] = NULL;
    return newNode;
}
void insertLeaf(struct BPlusTree *leaf, int key) {
    int i;
    for (i = leaf->count - 1; i >= 0 && leaf->keys[i] > key; i--)
        leaf->keys[i + 1] = leaf->keys[i];
    leaf->keys[i + 1] = key;
    leaf->count++;
}
void insert(int key) {
    if (root == NULL) {
        root = createNode(1);
        root->keys[0] = key;
        root->count = 1;
        return;
    }
    struct BPlusTree *cursor = root;
    struct BPlusTree *parent = NULL;
    while (!cursor->isLeaf) {
        parent = cursor;
        int i;
        for (i = 0; i < cursor->count; i++) {
            if (key < cursor->keys[i])
                break;
        }
        cursor = cursor->child[i];
    }
    insertLeaf(cursor, key);
    if (cursor->count < MAX)
        return;
    struct BPlusTree *newLeaf = createNode(1);
    int split = MAX / 2;
    cursor->count = split;
    newLeaf->count = MAX - split;
    for (int i = 0; i < newLeaf->count; i++)
        newLeaf->keys[i] = cursor->keys[i + split];
    newLeaf->next = cursor->next;
    cursor->next = newLeaf;
    if (cursor == root) {
        struct BPlusTree *newRoot = createNode(0);
        newRoot->keys[0] = newLeaf->keys[0];
        newRoot->child[0] = cursor;
        newRoot->child[1] = newLeaf;
        newRoot->count = 1;
        root = newRoot;
    }
}
void display() {
    if (root == NULL) {
        printf("Tree is empty\n");
        return;
    }
    struct BPlusTree *cursor = root;
    while (!cursor->isLeaf)
        cursor = cursor->child[0];
    printf("B+ Tree Elements:\n");
    while (cursor != NULL) {
        for (int i = 0; i < cursor->count; i++)
            printf("%d ", cursor->keys[i]);
        cursor = cursor->next;
    }
    printf("\n");
}
void search(int key) {
    struct BPlusTree *cursor = root;

    while (cursor != NULL && !cursor->isLeaf) {
        int i;
        for (i = 0; i < cursor->count; i++) {
            if (key < cursor->keys[i])
                break;
        }
        cursor = cursor->child[i];
    }
    if (cursor != NULL) {
        for (int i = 0; i < cursor->count; i++) {
            if (cursor->keys[i] == key) {
                printf("Key %d Found\n", key);
                return;
            }
        }
    }
    printf("Key %d Not Found\n", key);
}
int main() {
    int choice, key;
    do {
        printf("\n1.Insert\n2.Search\n3.Display\n4.Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);
        switch (choice) {
        case 1:
            printf("Enter key: ");
            scanf("%d", &key);
            insert(key);
            break;

        case 2:
            printf("Enter key to search: ");
            scanf("%d", &key);
            search(key);
            break;
        case 3:
            display();
            break;
        }
    } while (choice != 4);
    return 0;
}
