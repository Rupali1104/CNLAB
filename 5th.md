# Leaky Bucket Algorithm for Congestion Control

## Program Code

```c
#include <stdio.h> 
#include <stdlib.h> 
#include <unistd.h> 
#include <time.h>  // Required for time() function 

#define NOF_PACKETS 10 

// Custom rand function to generate a random number between 1 and a
int my_rand(int a) 
{ 
    int rn = (rand() % 10) % a;  // Using standard rand() instead of random() 
    return rn == 0 ? 1 : rn;     // Ensure the random number is not 0 
} 

int main() 
{ 
    int packet_sz[NOF_PACKETS], i, clk, b_size, o_rate, p_sz_rm = 0, p_sz, p_time, op; 

    // Initialize random number generator 
    srand(time(NULL));  // Seed the random number generator 

    // Generate random packet sizes using custom rand function 
    for (i = 0; i < NOF_PACKETS; i++) { 
        packet_sz[i] = my_rand(6) * 10;  // Random size between 10 and 60 
    } 

    // Print packet sizes 
    for (i = 0; i < NOF_PACKETS; ++i) { 
        printf("\npacket[%d]: %d bytes\t", i, packet_sz[i]); 
    } 

    // Ask for the Output rate and Bucket Size 
    printf("\nEnter the Output rate: "); 
    fflush(stdout);  // Ensures the prompt is displayed immediately 
    scanf("%d", &o_rate); 

    printf("Enter the Bucket Size: "); 
    fflush(stdout);  // Ensures the prompt is displayed immediately 
    scanf("%d", &b_size); 

    // Process packets 
    for (i = 0; i < NOF_PACKETS; ++i) { 
        if ((packet_sz[i] + p_sz_rm) > b_size) { 
            if (packet_sz[i] > b_size) { 
                printf("\n\nIncoming packet size (%d bytes) is greater than bucket capacity (%d bytes) - PACKET REJECTED", packet_sz[i], b_size); 
            } else { 
                printf("\n\nBucket capacity exceeded - PACKETS REJECTED!!"); 
            } 
        } else { 
            p_sz_rm += packet_sz[i]; 
            printf("\n\nIncoming Packet size: %d", packet_sz[i]); 
            printf("\nBytes remaining to Transmit: %d", p_sz_rm); 

            // Random transmission time between 10 and 40 units 
            p_time = my_rand(4) * 10;  // Random transmission time between 10 and 40 units 
            printf("\nTime left for transmission: %d unit(s)", p_time); 

            for (clk = 10; clk <= p_time; clk += 10) { 
                sleep(1); 
                if (p_sz_rm) { 
                    if (p_sz_rm < o_rate) { 
                        op = p_sz_rm; 
                        p_sz_rm = 0; 
                    } else { 
                        op = o_rate; 
                        p_sz_rm -= o_rate; 
                    } 

                    printf("\nPacket of size %d transmitted", op); 
                    printf("\n---Bytes Remaining to Transmit: %d", p_sz_rm); 
                } else { 
                    printf("\nTime left for transmission: %d unit(s)", p_time - clk); 
                    printf("\nNo packets transmitted"); 
                } 
            } 
        } 
    } 

    return 0; 
}

// Output
// packet[0]: 10 bytes
// packet[1]: 50 bytes
// packet[2]: 10 bytes
// packet[3]: 20 bytes
// packet[4]: 20 bytes
// packet[5]: 10 bytes
// packet[6]: 30 bytes
// packet[7]: 30 bytes
// packet[8]: 10 bytes
// packet[9]: 20 bytes

// Enter the Output rate: 20
// Enter the Bucket Size: 100

// Incoming Packet size: 10
// Bytes remaining to Transmit: 10
// Time left for transmission: 20 unit(s)
// Packet of size 10 transmitted ---Bytes Remaining to Transmit: 0

// Incoming Packet size: 50
// Bytes remaining to Transmit: 50
// Time left for transmission: 30 unit(s)
// Packet of size 20 transmitted ---Bytes Remaining to Transmit: 30
// Packet of size 20 transmitted ---Bytes Remaining to Transmit: 10
// Packet of size 10 transmitted ---Bytes Remaining to Transmit: 0

// Incoming Packet size: 10
// Bytes remaining to Transmit: 10
// Time left for transmission: 10 unit(s)
// Packet of size 10 transmitted ---Bytes Remaining to Transmit: 0

// Incoming Packet size: 20
// Bytes remaining to Transmit: 20
// Time left for transmission: 20 unit(s)
// Packet of size 20 transmitted ---Bytes Remaining to Transmit: 0

// Incoming Packet size: 10
// Bytes remaining to Transmit: 10
// Time left for transmission: 10 unit(s)
// Packet of size 10 transmitted ---Bytes Remaining to Transmit: 0

// Incoming Packet size: 30
// Bytes remaining to Transmit: 30
// Time left for transmission: 20 unit(s)
// Packet of size 20 transmitted ---Bytes Remaining to Transmit: 10
// Packet of size 10 transmitted ---Bytes Remaining to Transmit: 0

// Incoming Packet size: 30
// Bytes remaining to Transmit: 30
// Time left for transmission: 30 unit(s)
// Packet of size 20 transmitted ---Bytes Remaining to Transmit: 10
// Packet of size 10 transmitted ---Bytes Remaining to Transmit: 0

// Incoming Packet size: 10
// Bytes remaining to Transmit: 10
// Time left for transmission: 10 unit(s)
// Packet of size 10 transmitted ---Bytes Remaining to Transmit: 0

// Incoming Packet size: 20
// Bytes remaining to Transmit: 20
// Time left for transmission: 10 unit(s)
// Packet of size 20 transmitted ---Bytes Remaining to Transmit: 0
