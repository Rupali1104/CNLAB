# Program for Error Detecting Code using CRC-CCITT (16-bits)

```c
#include <stdio.h> 
#include <string.h> 

#define MAX_LENGTH 28 
#define MAX_GEN_POLY 10 

char data[MAX_LENGTH];       // Data to be transmitted 
char gen_poly[MAX_GEN_POLY]; // Generating polynomial 
char crc_remainder[MAX_GEN_POLY]; // Remainder (CRC value) 
int data_length, gen_poly_length; 

void XOR() { 
    for (int j = 0; j < gen_poly_length; j++) { 
        crc_remainder[j] = (crc_remainder[j] == gen_poly[j]) ? '0' : '1'; 
    } 
} 

void crc() { 
    // Initialize the remainder with the first part of the data 
    for (int i = 0; i < gen_poly_length; i++) { 
        crc_remainder[i] = data[i]; 
    } 

    // Perform the division process 
    for (int i = 0; i < data_length; i++) { 
        // Perform XOR if the first bit is 1 
        if (crc_remainder[0] == '1') { 
            XOR(); 
        } 
        // Shift left: drop the first bit and add the next bit of data 
        for (int j = 0; j < gen_poly_length - 1; j++) { 
            crc_remainder[j] = crc_remainder[j + 1]; 
        } 
        // Add the next bit from the data (if available) 
        crc_remainder[gen_poly_length - 1] = (i + gen_poly_length < data_length + gen_poly_length - 1) ? data[i + gen_poly_length] : '0'; 
    } 
    
    // Final XOR check 
    if (crc_remainder[0] == '1') { 
        XOR(); 
    } 
} 

void check_received_data() { 
    char received_data[MAX_LENGTH]; 
    char check_value[MAX_GEN_POLY]; 

    printf("Enter the received data: "); 
    scanf("%s", received_data); 

    // Initialize check_value to the received data 
    for (int i = 0; i < gen_poly_length; i++) { 
        check_value[i] = received_data[i]; 
    } 

    // Perform CRC on received data 
    for (int i = 0; i < data_length; i++) { 
        if (check_value[0] == '1') { 
            XOR(); 
        } 
        for (int j = 0; j < gen_poly_length - 1; j++) { 
            check_value[j] = check_value[j + 1]; 
        } 
        check_value[gen_poly_length - 1] = (i + gen_poly_length < data_length + gen_poly_length - 1) ? received_data[i + gen_poly_length] : '0'; 
    } 

    // Check if there's an error 
    int error_detected = 0; 
    for (int i = 0; i < gen_poly_length - 1; i++) { 
        if (check_value[i] == '1') { 
            error_detected = 1; 
            break; 
        } 
    } 

    if (error_detected) { 
        printf("Error detected in the received data.\n"); 
    } else { 
        printf("No error detected in the received data.\n"); 
    } 
} 

int main() { 
    printf("Enter the data to be transmitted: "); 
    scanf("%s", data); 
    printf("Enter the generating polynomial: "); 
    scanf("%s", gen_poly); 

    data_length = strlen(data); 
    gen_poly_length = strlen(gen_poly); 

    // Padding data with n-1 zeros 
    for (int i = data_length; i < data_length + gen_poly_length - 1; i++) { 
        data[i] = '0'; 
    } 
    data[data_length + gen_poly_length - 1] = '\0'; // Null-terminate the string 

    printf("\n--------------------"); 
    printf("\nData padded with n-1 zeros: %s", data); 
    printf("\n--------------------"); 

    // Calculate CRC 
    crc(); 

    // Output the remainder (CRC value) 
    printf("\nCRC or check value is: "); 
    for (int i = 1; i < gen_poly_length; i++) { 
        printf("%c", crc_remainder[i]); // Remainder starts from index 1 
    } 
    printf("\n"); 

    // Append the CRC to the original data for transmission 
    for (int i = 0; i < gen_poly_length - 1; i++) { 
        data[data_length + i] = crc_remainder[i + 1]; // Skip the first bit of the remainder 
    } 
    data[data_length + gen_poly_length - 2] = '\0'; // Null-terminate the string 

    printf("\n--------------------"); 
    printf("\nFinal data to be sent: %s", data); 
    printf("\n--------------------\n"); 

    // Check the received data for errors 
    check_received_data(); 

    return 0; 
} 

// # Output 
// Enter the data to be transmitted: 1001101101
// Enter the generating polynomial: 1011
// --------------------
// Data padded with n-1 zeros: 1001101101000
// --------------------
// CRC or check value is: 000
// --------------------
// Final data to be sent: 100110110100
// --------------------
// Enter the received data: 100110110100
// No error detected in the received data.
