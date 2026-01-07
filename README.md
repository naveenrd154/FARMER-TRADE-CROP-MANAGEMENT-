# FARMER-TRADE-CROP-MANAGEMENT-
Created a C-based application to help farmers manage crop details, pricing, and trade records efficiently. Implemented modules for crop data entry, buyer–seller transactions, and profit tracking using file handling and structures. Designed a user-friendly, menu-driven interface to simplify agricultural trade management and data storage.

## Features
- Crop data management
- Buyer–seller transaction handling
- Profit calculation and tracking
- Menu-driven user interface

## Technologies Used
- C Programming
- File Handling
- Structures

## What I Learned
- Modular programming in C
- File I/O operations
- Designing menu-driven applications
- Debugging logical and data consistency issues

## Why this project matters to me
This project strengthened my fundamentals in C programming and helped me understand how software can solve real-world agricultural problems.

## PROGRAM
    #include <stdio.h>
    #include <stdlib.h>
    #include <string.h>
    
    #define MAX 100
    #define MAX_USERS 100
    
    /* ================= Structures ================= */
    
    struct Crop {
        char name[50];
        int qty;
        float price;
    };
    
    struct User {
        char username[50];
        char password[50];
    };
    
    struct Transaction {
        char cropName[50];
        int qtyBought;
        float totalCost;
        struct Transaction *next;
    };
    
    /* ================= Global Variables ================= */
    
    struct Crop crops[MAX];
    int cropCount = 0;
    
    float farmerRevenue = 0.0;
    
    struct Transaction *transactionHead = NULL;
    
    struct User farmers[MAX_USERS];
    int farmerCount = 0;
    
    struct User buyers[MAX_USERS];
    int buyerCount = 0;
    
    /* File paths */
    const char *cropFile = "crops.txt";
    const char *revenueFile = "revenue.txt";
    const char *transactionFile = "transactions.txt";
    const char *farmerFile = "farmers.txt";
    const char *buyerFile = "buyers.txt";
    
    /* ================= Function Prototypes ================= */
    
    void loadData();
    void saveData();
    
    void loadTransactions();
    void saveTransactions();
    
    void loadUsers();
    void saveUsers();
    void createDefaultUsers();
    
    int farmerLogin();
    int buyerLogin();
    
    void farmerMenu();
    void buyerMenu();
    
    void addCrop();
    void viewCrops();
    void checkRevenue();
    
    void buyCrop();
    void addTransaction(char *name, int qty, float cost);
    void viewTransactions();
    
    /* ================= Main ================= */
    
    int main() {
        int choice;
    
        loadData();
    
        while (1) {
            printf("\n=== Farmer Trade & Crop Management ===\n");
            printf("1. Farmer Login\n");
            printf("2. Buyer Login\n");
            printf("3. Exit\n");
            printf("Enter choice: ");
            scanf("%d", &choice);
    
            switch (choice) {
                case 1:
                    if (farmerLogin())
                        farmerMenu();
                    else
                        printf("Invalid Farmer Credentials!\n");
                    break;
    
                case 2:
                    if (buyerLogin())
                        buyerMenu();
                    else
                        printf("Invalid Buyer Credentials!\n");
                    break;
    
                case 3:
                    saveData();
                    printf("Exiting... Data Saved!\n");
                    exit(0);
    
                default:
                    printf("Invalid choice!\n");
            }
        }
    
        return 0;
    }
    
    /* ================= File Handling ================= */
    
    void loadData() {
        FILE *f = fopen(cropFile, "r");
        if (f) {
            while (fscanf(f, "%s %d %f",
                          crops[cropCount].name,
                          &crops[cropCount].qty,
                          &crops[cropCount].price) == 3) {
                cropCount++;
            }
            fclose(f);
        }
    
        FILE *r = fopen(revenueFile, "r");
        if (r) {
            fscanf(r, "%f", &farmerRevenue);
            fclose(r);
        }
    
        loadTransactions();
        loadUsers();
    }
    
    void saveData() {
        FILE *f = fopen(cropFile, "w");
        for (int i = 0; i < cropCount; i++) {
            fprintf(f, "%s %d %.2f\n",
                    crops[i].name,
                    crops[i].qty,
                    crops[i].price);
        }
        fclose(f);
    
        FILE *r = fopen(revenueFile, "w");
        fprintf(r, "%.2f", farmerRevenue);
        fclose(r);
    
        saveTransactions();
        saveUsers();
    }
    
    void loadTransactions() {
        FILE *t = fopen(transactionFile, "r");
        if (!t) return;
    
        char name[50];
        int qty;
        float cost;
    
        while (fscanf(t, "%s %d %f", name, &qty, &cost) == 3) {
            addTransaction(name, qty, cost);
        }
        fclose(t);
    }
    
    void saveTransactions() {
        FILE *t = fopen(transactionFile, "w");
        struct Transaction *temp = transactionHead;
    
        while (temp) {
            fprintf(t, "%s %d %.2f\n",
                    temp->cropName,
                    temp->qtyBought,
                    temp->totalCost);
            temp = temp->next;
        }
        fclose(t);
    }
    
    /* ================= User Handling ================= */
    
    void createDefaultUsers() {
        FILE *f = fopen(farmerFile, "r");
        if (!f) {
            f = fopen(farmerFile, "w");
            fprintf(f, "farmer1 pass123\nfarmer2 farmerpass\n");
            fclose(f);
        } else {
            fclose(f);
        }
    
        FILE *b = fopen(buyerFile, "r");
        if (!b) {
            b = fopen(buyerFile, "w");
            fprintf(b, "buyer1 pass123\nbuyer2 buyerpass\n");
            fclose(b);
        } else {
            fclose(b);
        }
    }
    
    void loadUsers() {
        createDefaultUsers();
    
        FILE *f = fopen(farmerFile, "r");
        if (f) {
            while (fscanf(f, "%s %s",
                          farmers[farmerCount].username,
                          farmers[farmerCount].password) == 2) {
                farmerCount++;
            }
            fclose(f);
        }
    
        FILE *b = fopen(buyerFile, "r");
        if (b) {
            while (fscanf(b, "%s %s",
                          buyers[buyerCount].username,
                          buyers[buyerCount].password) == 2) {
                buyerCount++;
            }
            fclose(b);
        }
    }
    
    void saveUsers() {
        FILE *f = fopen(farmerFile, "w");
        for (int i = 0; i < farmerCount; i++) {
            fprintf(f, "%s %s\n",
                    farmers[i].username,
                    farmers[i].password);
        }
        fclose(f);
    
        FILE *b = fopen(buyerFile, "w");
        for (int i = 0; i < buyerCount; i++) {
            fprintf(b, "%s %s\n",
                    buyers[i].username,
                    buyers[i].password);
        }
        fclose(b);
    }
    
    /* ================= Login ================= */
    
    int farmerLogin() {
        char u[50], p[50];
        printf("Enter Farmer Username: ");
        scanf("%s", u);
        printf("Enter Password: ");
        scanf("%s", p);
    
        for (int i = 0; i < farmerCount; i++) {
            if (strcmp(u, farmers[i].username) == 0 &&
                strcmp(p, farmers[i].password) == 0) {
                return 1;
            }
        }
        return 0;
    }
    
    int buyerLogin() {
        char u[50], p[50];
        printf("Enter Buyer Username: ");
        scanf("%s", u);
        printf("Enter Password: ");
        scanf("%s", p);
    
        for (int i = 0; i < buyerCount; i++) {
            if (strcmp(u, buyers[i].username) == 0 &&
                strcmp(p, buyers[i].password) == 0) {
                return 1;
            }
        }
        return 0;
    }
    
    /* ================= Farmer Menu ================= */
    
    void farmerMenu() {
        int choice;
        while (1) {
            printf("\n--- Farmer Menu ---\n");
            printf("1. Add Crop\n");
            printf("2. View Crops\n");
            printf("3. Check Revenue\n");
            printf("4. Back\n");
            printf("Enter choice: ");
            scanf("%d", &choice);
    
            switch (choice) {
                case 1: addCrop(); break;
                case 2: viewCrops(); break;
                case 3: checkRevenue(); break;
                case 4: return;
                default: printf("Invalid choice!\n");
            }
        }
    }
    
    void addCrop() {
        if (cropCount >= MAX) {
            printf("Crop limit reached!\n");
            return;
        }
    
        printf("Enter Crop Name: ");
        scanf("%s", crops[cropCount].name);
        printf("Enter Quantity: ");
        scanf("%d", &crops[cropCount].qty);
        printf("Enter Price: ");
        scanf("%f", &crops[cropCount].price);
    
        cropCount++;
        printf("Crop added successfully!\n");
    }
    
    void viewCrops() {
        if (cropCount == 0) {
            printf("No crops available!\n");
            return;
        }
    
        printf("\nAvailable Crops:\n");
        for (int i = 0; i < cropCount; i++) {
            printf("%d. %s | Qty: %d | Price: ₹%.2f\n",
                   i + 1, crops[i].name, crops[i].qty, crops[i].price);
        }
    }
    
    void checkRevenue() {
        printf("Total Revenue: ₹%.2f\n", farmerRevenue);
    }
    
    /* ================= Buyer Menu ================= */
    
    void buyerMenu() {
        int choice;
        while (1) {
            printf("\n--- Buyer Menu ---\n");
            printf("1. View Crops\n");
            printf("2. Buy Crop\n");
            printf("3. View Transactions\n");
            printf("4. Back\n");
            printf("Enter choice: ");
            scanf("%d", &choice);
    
            switch (choice) {
                case 1: viewCrops(); break;
                case 2: buyCrop(); break;
                case 3: viewTransactions(); break;
                case 4: return;
                default: printf("Invalid choice!\n");
            }
        }
    }
    
    void buyCrop() {
        char name[50];
        int qty;
    
        viewCrops();
        printf("Enter Crop Name: ");
        scanf("%s", name);
        printf("Enter Quantity: ");
        scanf("%d", &qty);
    
        for (int i = 0; i < cropCount; i++) {
            if (strcmp(name, crops[i].name) == 0) {
                if (qty > crops[i].qty) {
                    printf("Not enough stock!\n");
                    return;
                }
    
                float cost = qty * crops[i].price;
                crops[i].qty -= qty;
                farmerRevenue += cost;
                addTransaction(name, qty, cost);
    
                printf("Purchase successful! Cost: ₹%.2f\n", cost);
                return;
            }
        }
    
        printf("Crop not found!\n");
    }
    
    /* ================= Transactions ================= */
    
    void addTransaction(char *name, int qty, float cost) {
        struct Transaction *newNode =
            (struct Transaction *)malloc(sizeof(struct Transaction));
    
        strcpy(newNode->cropName, name);
        newNode->qtyBought = qty;
        newNode->totalCost = cost;
        newNode->next = transactionHead;
        transactionHead = newNode;
    }
    
    void viewTransactions() {
        struct Transaction *temp = transactionHead;
    
        if (!temp) {
            printf("No transactions available!\n");
            return;
        }
    
        printf("\n--- Transaction History ---\n");
        while (temp) {
            printf("Bought: %s | Qty: %d | Cost: ₹%.2f\n",
                   temp->cropName,
                   temp->qtyBought,
                   temp->totalCost);
            temp = temp->next;
        }
    }

