# Simple RSA Algorithm Program

## Program Code

```c
#include <stdio.h> 
#include <stdlib.h> 
#include <math.h> 
#include <string.h> 

long int p, q, n, t, flag, e[100], d[100], temp[100], j, m[100], en[100], i; 
char msg[100]; 

int prime(long int); 
void ce(); 
long int modInverse(long int, long int); 
void encrypt(); 
void decrypt(); 

int main() 
{ 
    printf("Enter the First prime number: "); 
    scanf("%ld", &p); 
    flag = prime(p); 
    if (flag == 0 || p == 1) { 
        printf("Invalid Input\n"); 
        exit(1); 
    } 
     
    printf("Enter another prime number: "); 
    scanf("%ld", &q); 
    flag = prime(q); 
    if (flag == 0 || q == 1 || p == q) { 
        printf("Invalid Input\n"); 
        exit(1); 
    } 
     
    printf("ENTER MESSAGE: "); 
    scanf("%s", msg); 
     
    for (i = 0; i < strlen(msg); i++) 
        m[i] = msg[i]; 
     
    n = p * q; 
    t = (p - 1) * (q - 1); 

    ce(); 
    printf("\nPOSSIBLE VALUES OF e AND d ARE:\n"); 
    for (i = 0; i < j; i++) 
        printf("%ld\t%ld\n", e[i], d[i]); 

    encrypt(); 
    decrypt(); 

    return 0; 
} 

int prime(long int pr) // Checks if entered number is prime or not 
{ 
    int i; 
    if (pr == 1) 
        return 0; 
    for (i = 2; i <= sqrt(pr); i++) { 
        if (pr % i == 0) 
            return 0; 
    } 
    return 1; 
} 

void ce() 
{ 
    int k; 
    k = 0; 
    for (i = 2; i < t; i++) { 
        if (t % i == 0) // If i is a factor of t, skip 
            continue; 
        flag = prime(i); 
        if (flag == 1 && i != p && i != q) { 
            e[k] = i; 
            d[k] = modInverse(e[k], t); // Use modInverse to calculate d 
            if (d[k] > 0) { 
                k++; 
            } 
            if (k == 99) 
                break; 
        } 
    } 
    j = k; 
} 

long int modInverse(long int a, long int m) 
{ 
    long int m0 = m, t, q; 
    long int x0 = 0, x1 = 1; 
     
    if (m == 1) return 0; 
     
    while (a > 1) { 
        q = a / m; 
        t = m; 
         
        m = a % m; 
        a = t; 
         
        t = x0; 
        x0 = x1 - q * x0; 
        x1 = t; 
    } 
    if (x1 < 0) 
        x1 += m0; 
     
    return x1; 
} 

void encrypt() 
{ 
    long int pt, ct, key = e[0], k, len; 
    i = 0; 
    len = strlen(msg); 
    while (i < len) { 
        pt = m[i] - 96;  // Convert to 1-26 scale 
        k = 1; 
        for (j = 0; j < key; j++) { 
            k = (k * pt) % n; 
        } 
        temp[i] = k; 
        ct = k + 96;  // Convert back to char 
        en[i] = ct; 
        i++; 
    } 
    en[i] = -1; 
    printf("\nTHE ENCRYPTED MESSAGE IS:\n"); 
    for (i = 0; en[i] != -1; i++) 
        printf("%c", (char) en[i]); 
} 

void decrypt() 
{ 
    long int pt, ct, key = d[0], k; 
    i = 0; 
    while (en[i] != -1) { 
        ct = temp[i]; 
        k = 1; 
        for (j = 0; j < key; j++) { 
            k = (k * ct) % n; 
        } 
        pt = k + 96;  // Convert back to original character 
        m[i] = pt; 
        i++; 
    } 
    m[i] = -1; 
    printf("\nTHE DECRYPTED MESSAGE IS:\n"); 
    for (i = 0; m[i] != -1; i++) 
        printf("%c", (char) m[i]); 
} 

// Output:
// Enter the First prime number: 2 
// Enter another prime number: 19 
// ENTER MESSAGE: jitendra 

// POSSIBLE VALUES OF e AND d ARE: 
// 5   11 
// 7   13 
// 11  5 
// 13  7 
// 17  17 

// THE ENCRYPTED MESSAGE IS: 
// v�tij�ra 
// THE DECRYPTED MESSAGE IS: 
// jitendra

 
 
 
 
 
 
 
 
 
