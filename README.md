## EX. NO:2 IMPLEMENTATION OF PLAYFAIR CIPHER

 

## AIM:
 

 

To write a C program to implement the Playfair Substitution technique.

## DESCRIPTION:

The Playfair cipher starts with creating a key table. The key table is a 5×5 grid of letters that will act as the key for encrypting your plaintext. Each of the 25 letters must be unique and one letter of the alphabet is omitted from the table (as there are 25 spots and 26 letters in the alphabet).

To encrypt a message, one would break the message into digrams (groups of 2 letters) such that, for example, "HelloWorld" becomes "HE LL OW OR LD", and map them out on the key table. The two letters of the diagram are considered as the opposite corners of a rectangle in the key table. Note the relative position of the corners of this rectangle. Then apply the following 4 rules, in order, to each pair of letters in the plaintext:
1.	If both letters are the same (or only one letter is left), add an "X" after the first letter
2.	If the letters appear on the same row of your table, replace them with the letters to their immediate right respectively
3.	If the letters appear on the same column of your table, replace them with the letters immediately below respectively
4.	If the letters are not on the same row or column, replace them with the letters on the same row respectively but at the other pair of corners of the rectangle defined by the original pair.
## EXAMPLE:
![image](https://github.com/Hemamanigandan/EX-NO-2-/assets/149653568/e6858d4f-b122-42ba-acdb-db18ec2e9675)

 

## ALGORITHM:

STEP-1: Read the plain text from the user.
STEP-2: Read the keyword from the user.
STEP-3: Arrange the keyword without duplicates in a 5*5 matrix in the row order and fill the remaining cells with missed out letters in alphabetical order. Note that ‘i’ and ‘j’ takes the same cell.
STEP-4: Group the plain text in pairs and match the corresponding corner letters by forming a rectangular grid.
STEP-5: Display the obtained cipher text.




Program:
```
#include <stdio.h>
#include <string.h>
#include <ctype.h>

#define SIZE 100

void toLowerCase(char str[]) {
    for (int i = 0; str[i]; i++) {
        str[i] = tolower(str[i]);
    }
}

void removeSpaces(char str[]) {
    int j = 0;
    for (int i = 0; str[i]; i++) {
        if (str[i] != ' ')
            str[j++] = str[i];
    }
    str[j] = '\0';
}

void generateKeyTable(char key[], char keyTable[5][5]) {
    int used[26] = {0};

    // Treat J as I
    used['j' - 'a'] = 1;

    int row = 0, col = 0;

    for (int i = 0; key[i]; i++) {
        char ch = key[i];

        if (ch == 'j')
            ch = 'i';

        if (ch >= 'a' && ch <= 'z' && !used[ch - 'a']) {
            keyTable[row][col] = ch;
            used[ch - 'a'] = 1;

            col++;
            if (col == 5) {
                row++;
                col = 0;
            }
        }
    }

    for (char ch = 'a'; ch <= 'z'; ch++) {
        if (!used[ch - 'a']) {
            keyTable[row][col] = ch;

            col++;
            if (col == 5) {
                row++;
                col = 0;
            }
        }
    }
}

void printKeyTable(char keyTable[5][5]) {
    printf("\nKey Matrix:\n");
    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++) {
            printf("%c ", keyTable[i][j]);
        }
        printf("\n");
    }
}

void search(char keyTable[5][5], char a, char b, int pos[]) {
    if (a == 'j')
        a = 'i';
    if (b == 'j')
        b = 'i';

    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++) {
            if (keyTable[i][j] == a) {
                pos[0] = i;
                pos[1] = j;
            }
            if (keyTable[i][j] == b) {
                pos[2] = i;
                pos[3] = j;
            }
        }
    }
}

void prepareText(char plain[]) {
    char temp[SIZE];
    int i = 0, j = 0;

    while (plain[i]) {
        temp[j++] = plain[i];

        if (plain[i + 1]) {
            if (plain[i] == plain[i + 1]) {
                temp[j++] = 'x';
                i++;
            } else {
                i++;
            }
        } else {
            i++;
        }
    }

    temp[j] = '\0';

    if (j % 2 != 0) {
        temp[j++] = 'z';
        temp[j] = '\0';
    }

    strcpy(plain, temp);
}

void encrypt(char plain[], char keyTable[5][5]) {
    int pos[4];

    for (int i = 0; plain[i]; i += 2) {
        search(keyTable, plain[i], plain[i + 1], pos);

        // Same row
        if (pos[0] == pos[2]) {
            plain[i] = keyTable[pos[0]][(pos[1] + 1) % 5];
            plain[i + 1] = keyTable[pos[2]][(pos[3] + 1) % 5];
        }

        // Same column
        else if (pos[1] == pos[3]) {
            plain[i] = keyTable[(pos[0] + 1) % 5][pos[1]];
            plain[i + 1] = keyTable[(pos[2] + 1) % 5][pos[3]];
        }

        // Rectangle rule
        else {
            char c1 = keyTable[pos[0]][pos[3]];
            char c2 = keyTable[pos[2]][pos[1]];

            plain[i] = c1;
            plain[i + 1] = c2;
        }
    }
}

int main() {
    char key[SIZE];
    char plain[SIZE];
    char keyTable[5][5];

    printf("Enter Key: ");
    fgets(key, SIZE, stdin);
    key[strcspn(key, "\n")] = '\0';

    printf("Enter Plaintext: ");
    fgets(plain, SIZE, stdin);
    plain[strcspn(plain, "\n")] = '\0';

    toLowerCase(key);
    removeSpaces(key);

    toLowerCase(plain);
    removeSpaces(plain);

    prepareText(plain);

    generateKeyTable(key, keyTable);

    printf("\nKey       : %s\n", key);
    printf("Plaintext : %s\n", plain);

    printKeyTable(keyTable);

    encrypt(plain, keyTable);

    printf("\nCiphertext: %s\n", plain);

    return 0;
}
```




Output:
<img width="1608" height="711" alt="image" src="https://github.com/user-attachments/assets/f4e5301c-8108-496e-acb6-6e4a68839b44" />
