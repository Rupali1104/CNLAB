# Leaky Bucket Algorithm for Congestion Control

## Program Code

```c


#include <stdio.h>
#include <stdlib.h>
#include <unistd.h> // For sleep function

#define BUCKET_SIZE 10
#define OUT_RATE 3

int main(){
    // we will take number of time units first as input
    // then we will take number of packets arriving at each time unit

    int time_units;
    printf("Enter the number of time units: ");
    scanf("%d", &time_units);
    int packets[time_units];

    printf("Enter the number of packets arriving at each time unit: ");
    for(int i = 0; i < time_units; i++){
        scanf("%d", &packets[i]);
    }

    // initialize the bucket with 0 packets
    int bucket = 0;

    // now the logic
    // first what happens is that the packets arrive at the bucket
    // then the packets are sent out at the rate of OUT_RATE packets per time unit

    for(int i=0; i<time_units; i++){
        // it will keep sending 3 packets (if there are 3 packets) until the bucket is empty

        printf("\nAt time unit %d: \n", i+1);
        printf("Packets arrived: %d\n", packets[i]);
        printf("Packets in the bucket: %d\n", bucket);

        // if the packets are more than the bucket size, then the bucket will overflow
        if(packets[i] + bucket <= BUCKET_SIZE){
            bucket += packets[i];
            printf("Packets added to the bucket: %d\n", packets[i]);
        } else {
            printf("Bucket overflow, Packet Dropped!!\n");
        }

        int sent = 0;

        if(bucket >= OUT_RATE){
            sent = OUT_RATE;
            bucket -= OUT_RATE;
        } else {
            sent = bucket;
            bucket = 0;
        }

        printf("Packets sent: %d\n", sent);

        sleep(1);
    }

    printf("All Packets Sent, Bucket Empty\n");

}