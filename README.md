#include <avr/io.h>
#include <util/delay.h>

#define F_CPU 16000000UL // Frecuencia del microcontrolador (16MHz)
#define DEBOUNCE_DELAY 50 // Retardo para el antirrebote en milisegundos

volatile uint8_t contador = 0; // Variable para almacenar el valor del contador

void incrementarContador() {
    if (contador < 15) {
        contador++;
    }
}

void decrementarContador() {
    if (contador > 0) {
        contador--;
    }
}

// Función para implementar antirrebotes
int botonPresionado(volatile uint8_t *PIN, uint8_t bit) {
    if (!(*PIN & (1 << bit))) {
        _delay_ms(DEBOUNCE_DELAY);
        if (!(*PIN & (1 << bit))) {
            return 1;
        }
    }
    return 0;
}

int main(void) {
    // Configuración de pines para los botones y el contador
    DDRD &= ~((1 << PD2) | (1 << PD3)); // PD2 y PD3 como entradas (botones)
    DDRB |= 0b00001111; // PB0-PB3 como salidas (contador de 4 bits)

    // Bucle principal
    while (1) {
        // Incrementar contador
        if (botonPresionado(&PIND, PD2)) {
            incrementarContador();
        }

        // Decrementar contador
        if (botonPresionado(&PIND, PD3)) {
            decrementarContador();
        }

        // Actualizar la salida del contador en los pines PB0-PB3
        PORTB = (PORTB & 0xF0) | (contador & 0x0F);
    }

    return 0;
}
