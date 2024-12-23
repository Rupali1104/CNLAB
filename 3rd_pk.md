# Program for Error Detecting Code using CRC-CCITT (16-bits)

```c

#include <stdio.h>
#include <string.h>

// Function to perform XOR operation
void XOR(char *dividend, char *divisor, int len) {
    for (int i = 0; i < len; i++) {
        dividend[i] = (dividend[i] == divisor[i]) ? '0' : '1';
    }
}

// Function to compute CRC
void calculateCRC(char data[], char generator[], char crc[]) {
    int data_len = strlen(data);
    int generator_len = strlen(generator);

    // Create a temporary dividend by appending (generator length - 1) zeros to the data
    char dividend[data_len + generator_len];
    strcpy(dividend, data);
    for (int i = data_len; i < data_len + generator_len - 1; i++) {
        dividend[i] = '0';
    }
    dividend[data_len + generator_len - 1] = '\0';

    // Temporary remainder
    char remainder[generator_len];
    strncpy(remainder, dividend, generator_len);

    // Perform the division
    for (int i = 0; i < data_len; i++) {
        if (remainder[0] == '1') {
            XOR(remainder, generator, generator_len);
        }
        // Shift the remainder left and append the next bit from dividend
        memmove(remainder, remainder + 1, generator_len - 1);
        remainder[generator_len - 1] = dividend[generator_len + i];
    }

    // Copy the remainder (CRC) into the crc array
    strncpy(crc, remainder, generator_len - 1);
    crc[generator_len - 1] = '\0';
}

// Function to check for errors at the receiver side
int checkForErrors(char received_data[], char generator[]) {
    int len = strlen(generator);
    char remainder[len];
    strncpy(remainder, received_data, len);

    for (int i = 0; i < strlen(received_data) - len + 1; i++) {
        if (remainder[0] == '1') {
            XOR(remainder, generator, len);
        }
        memmove(remainder, remainder + 1, len - 1);
        remainder[len - 1] = received_data[len + i];
    }

    // Check if the remainder is zero
    for (int i = 0; i < len - 1; i++) {
        if (remainder[i] != '0') {
            return 1; // Error detected
        }
    }
    return 0; // No error
}

int main() {
    char data[128], generator[128], crc[128], transmitted_data[256];

    // Input the data and generator polynomial
    printf("Enter the data: ");
    scanf("%s", data);
    printf("Enter the generator polynomial: ");
    scanf("%s", generator);

    // Calculate CRC
    calculateCRC(data, generator, crc);

    // Append the CRC to the data to create transmitted data
    strcpy(transmitted_data, data);
    strcat(transmitted_data, crc);

    printf("CRC: %s\n", crc);
    printf("Transmitted Data: %s\n", transmitted_data);

    // // Receiver side check
    printf("Enter the received data: ");
    scanf("%s", transmitted_data);

    if (checkForErrors(transmitted_data, generator)) {
        printf("Error detected in the received data.\n");
    } else {
        printf("No error detected in the received data.\n");
    }

    return 0;
}
