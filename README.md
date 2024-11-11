#include <reg51.h>

void delay(void);
void uart_init(void);
void uart_transmit(char c);
void uart_print(char *str);

void main(void) {
    uart_init(); // Initialize UART
    
    while (1) { 
        P1 = 0xFF; // Turn ON all LEDs connected to Port1
        uart_print("LEDs ON\n"); // Print status
        delay();   // Wait for some time
        
        P1 = 0x00; // Turn OFF all LEDs connected to Port1
        uart_print("LEDs OFF\n"); // Print status
        delay();   // Wait for some time
    }
}

void delay(void) {
    unsigned int i, j;
    for (i = 0; i < 300; i++)  // Outer loop
        for (j = 0; j < 300; j++);  // Inner loop
}

void uart_init(void) {
    TMOD = 0x20;  // Timer 1 in mode 2
    TH1 = 0xFD;   // Load value for 9600 baud rate
    SCON = 0x50;  // 8-bit data, 1 stop bit, receiver enabled
    TR1 = 1;      // Start timer 1
}

void uart_transmit(char c) {
    SBUF = c;          // Load the data to be transmitted
    while (TI == 0);   // Wait until transmission is complete
    TI = 0;            // Clear the transmit interrupt flag
}

void uart_print(char *str) {
    while (*str) {
        uart_transmit(*str++);
    }
}
