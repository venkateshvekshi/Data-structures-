#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_CUSTOMERS 100

typedef struct Transaction {
    float amount;
    char type[10]; // "deposit" or "withdraw"
    struct Transaction *next;
} Transaction;

typedef struct {
    int accountNumber;
    char name[100];
    float balance;
    Transaction *transactions;
} Customer;

Customer customers[MAX_CUSTOMERS];
int customerCount = 0;

// Function prototypes
void createAccount();
void deposit();
void withdraw();
void viewAccount();
void addTransaction(Customer *cust, float amount, const char *type);
void showTransactions(Transaction *t);

int main() {
    int choice;
    while (1) {
        printf("\n--- Bank Account Management ---\n");
        printf("1. Create Account\n2. Deposit\n3. Withdraw\n4. View Account\n5. Exit\nEnter choice: ");
        scanf("%d", &choice);
        switch (choice) {
            case 1: createAccount(); break;
            case 2: deposit(); break;
            case 3: withdraw(); break;
            case 4: viewAccount(); break;
            case 5: exit(0);
            default: printf("Invalid choice.\n");
        }
    }
    return 0;
}

void createAccount() {
    if (customerCount >= MAX_CUSTOMERS) {
        printf("Maximum number of customers reached.\n");
        return;
    }
    Customer *cust = &customers[customerCount];
    printf("Enter account number: ");
    scanf("%d", &cust->accountNumber);
    printf("Enter name: ");
    scanf(" %[^\n]", cust->name);
    cust->balance = 0.0;
    cust->transactions = NULL;
    customerCount++;
    printf("Account created successfully.\n");
}

Customer* findCustomer(int accNum) {
    for (int i = 0; i < customerCount; i++) {
        if (customers[i].accountNumber == accNum)
            return &customers[i];
    }
    return NULL;
}

void deposit() {
    int acc;
    float amount;
    printf("Enter account number: ");
    scanf("%d", &acc);
    Customer *cust = findCustomer(acc);
    if (cust == NULL) {
        printf("Account not found.\n");
        return;
    }
    printf("Enter amount to deposit: ");
    scanf("%f", &amount);
    cust->balance += amount;
    addTransaction(cust, amount, "deposit");
    printf("Deposit successful. New balance: %.2f\n", cust->balance);
}

void withdraw() {
    int acc;
    float amount;
    printf("Enter account number: ");
    scanf("%d", &acc);
    Customer *cust = findCustomer(acc);
    if (cust == NULL) {
        printf("Account not found.\n");
        return;
    }
    printf("Enter amount to withdraw: ");
    scanf("%f", &amount);
    if (amount > cust->balance) {
        printf("Insufficient balance.\n");
        return;
    }
    cust->balance -= amount;
    addTransaction(cust, amount, "withdraw");
    printf("Withdrawal successful. New balance: %.2f\n", cust->balance);
}

void addTransaction(Customer *cust, float amount, const char *type) {
    Transaction *t = (Transaction *)malloc(sizeof(Transaction));
    t->amount = amount;
    strcpy(t->type, type);
    t->next = cust->transactions;
    cust->transactions = t;
}

void showTransactions(Transaction *t) {
    printf("Transactions:\n");
    while (t != NULL) {
        printf(" - %s: %.2f\n", t->type, t->amount);
        t = t->next;
    }
}

void viewAccount() {
    int acc;
    printf("Enter account number: ");
    scanf("%d", &acc);
    Customer *cust = findCustomer(acc);
    if (cust == NULL) {
        printf("Account not found.\n");
        return;
    }
    printf("Account Number: %d\n", cust->accountNumber);
    printf("Name: %s\n", cust->name);
    printf("Balance: %.2f\n", cust->balance);
    showTransactions(cust->transactions);
}
