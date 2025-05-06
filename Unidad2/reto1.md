## Desarrollar un programa en lenguaje C que muestre una animación en una matriz de LED 8x8. La animación debe estar compuesta por una secuencia de imágenes (frames) que se reproducen de forma continua, simulando el efecto de un video.

### **Requisitos**:

1. **Programación en C**: Todo el desarrollo debe realizarse en lenguaje C, haciendo uso de estructuras y buenas prácticas de programación.
2. **Almacenamiento de Datos en Memoria**: Definir y almacenar los cuadros de la animación como arreglos en memoria, utilizando un formato adecuado para la matriz de LED.
3. **Creación de Tablas de Frames**: Implementar una estructura que permita organizar y recorrer los diferentes cuadros de la animación.
4. **Transferencia de Datos a la Matriz**: Enviar los datos de cada cuadro a la matriz LED para ser visualizados de forma secuencial.
5. **Uso de Máquina de Estados**: Es obligatorio el uso de una máquina de estados para controlar la lógica de reproducción de la animación (inicio, reproducción, pausa, final, reinicio, etc.).
6. **Control de Tiempo**: Implementar mecanismos para definir el intervalo de tiempo entre cada cuadro, simulando una tasa de cuadros por segundo (FPS).

### **Codigo**:
Se implementa el siguiente codigo en assembler.
El siguiente codigo se realizó con una logica por lineas, dentro del codigo se definen cuales pines se usaron tanto para columnas como para filas y para este caso mostrar 24 frames en la matriz de leds 8x8.

```
#include "stm32f4xx.h"
#include <stdint.h>

// ====================================================
// CONFIGURACIONES Y DEFINICIONES
// ====================================================

// Definición de puertos para la matriz LED 8x8
#define FILA_PORT    GPIOD
#define COLUMNA_PORT GPIOE

#define FILA_MASK    0xFF  // Pines 0-7 para filas
#define COLUMNA_MASK 0xFF  // Pines 0-7 para columnas

// Animación: definición de cuadros (frames)
// Cada fila se representa en un byte
#define TOTAL_FRAMES 24
const uint8_t frames[TOTAL_FRAMES][8] = {
    {
  0b10000000,
  0b10000000,
  0b11000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000
},{
  0b00000000,
  0b10000000,
  0b10000000,
  0b11000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000
},{
  0b00000000,
  0b00000000,
  0b10000000,
  0b10000000,
  0b11000000,
  0b00000000,
  0b00000000,
  0b00000000
},{
  0b00000000,
  0b00000000,
  0b00000000,
  0b10000000,
  0b10000000,
  0b11000000,
  0b00000000,
  0b00000000
},{
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b10000000,
  0b10000000,
  0b11000000,
  0b00000000
},{
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b10000000,
  0b10000000,
  0b11000000
},{
  0b00110000,
  0b00110000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b10000000,
  0b10000000,
  0b11000000
},{
  0b00000000,
  0b00110000,
  0b00110000,
  0b00000000,
  0b00000000,
  0b10000000,
  0b10000000,
  0b11000000
},{
  0b00000000,
  0b00000000,
  0b00110000,
  0b00110000,
  0b00000000,
  0b10000000,
  0b10000000,
  0b11000000
},{
  0b00000000,
  0b00000000,
  0b00000000,
  0b00110000,
  0b00110000,
  0b10000000,
  0b10000000,
  0b11000000
},{
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00110000,
  0b10110000,
  0b10000000,
  0b11000000
},{
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b10110000,
  0b10110000,
  0b11000000
},{
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b10000000,
  0b10110000,
  0b11110000
},{
  0b00001111,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b10000000,
  0b10110000,
  0b11110000
},{
  0b00000000,
  0b00001111,
  0b00000000,
  0b00000000,
  0b00000000,
  0b10000000,
  0b10110000,
  0b11110000
},{
  0b00000000,
  0b00000000,
  0b00001111,
  0b00000000,
  0b00000000,
  0b10000000,
  0b10110000,
  0b11110000
},{
  0b00000000,
  0b00000000,
  0b00000000,
  0b00001111,
  0b00000000,
  0b10000000,
  0b10110000,
  0b11110000
},{
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00001111,
  0b10000000,
  0b10110000,
  0b11110000
},{
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b10001111,
  0b10110000,
  0b11110000
},{
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b10000000,
  0b10111111,
  0b11110000
},{
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b10000000,
  0b10110000,
  0b11111111
},{
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b10000000,
  0b10110000,
  0b00000000
},{
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b10000000,
  0b10110000
},{
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000,
  0b00000000
}
};

// ====================================================
// MÁQUINA DE ESTADOS PRINCIPAL
// ====================================================
typedef enum {
    ESTADO_INICIAL,
    ESTADO_REPRODUCCION_FILA0,
    ESTADO_REPRODUCCION_FILA1,
    ESTADO_REPRODUCCION_FILA2,
    ESTADO_REPRODUCCION_FILA3,
    ESTADO_REPRODUCCION_FILA4,
    ESTADO_REPRODUCCION_FILA5,
    ESTADO_REPRODUCCION_FILA6,
    ESTADO_REPRODUCCION_FILA7,
    ESTADO_PAUSA
} EstadoAnimacion;

volatile uint32_t tiempo_ms = 0;      // Contador en ms (actualizado por SysTick)
EstadoAnimacion estado = ESTADO_INICIAL;
volatile uint8_t frame_actual = 0;      // Índice del frame actual

// Parámetros de tiempo:
uint32_t tiempo_fila     = 2;    // Tiempo en ms para refrescar cada fila (p.ej. 2 ms por fila)
uint32_t duracion_frame  = 300;  // Tiempo total para mostrar cada frame (en ms)
uint32_t duracion_pausa  = 3000; // Duración de la pausa tras la animación (3 s)

// Variables para controlar los intervalos:
volatile uint32_t ultimo_cambio = 0;      // Tiempo de la última transición de fila
volatile uint32_t tiempo_inicio_frame = 0;  // Tiempo en que se inició la visualización del frame

// ====================================================
// PROTOTIPOS DE FUNCIONES
// ====================================================
void config_gpio(void);
void config_systick(void);
void mostrar_fila(uint8_t fila_idx, const uint8_t frame[8]);

// ====================================================
// FUNCIÓN MAIN
// ====================================================
int main(void) {
    config_gpio();
    config_systick();

    while (1) {
        switch (estado) {
            case ESTADO_INICIAL:
                // Reiniciar la animación y guardar el inicio del frame actual.
                frame_actual = 0;
                tiempo_inicio_frame = tiempo_ms;
                ultimo_cambio = tiempo_ms;
                estado = ESTADO_REPRODUCCION_FILA0;
                break;

            case ESTADO_REPRODUCCION_FILA0:
                if ((tiempo_ms - ultimo_cambio) >= tiempo_fila) {
                    ultimo_cambio = tiempo_ms;
                    mostrar_fila(0, frames[frame_actual]);
                    estado = ESTADO_REPRODUCCION_FILA1;
                }
                break;

            case ESTADO_REPRODUCCION_FILA1:
                if ((tiempo_ms - ultimo_cambio) >= tiempo_fila) {
                    ultimo_cambio = tiempo_ms;
                    mostrar_fila(1, frames[frame_actual]);
                    estado = ESTADO_REPRODUCCION_FILA2;
                }
                break;

            case ESTADO_REPRODUCCION_FILA2:
                if ((tiempo_ms - ultimo_cambio) >= tiempo_fila) {
                    ultimo_cambio = tiempo_ms;
                    mostrar_fila(2, frames[frame_actual]);
                    estado = ESTADO_REPRODUCCION_FILA3;
                }
                break;

            case ESTADO_REPRODUCCION_FILA3:
                if ((tiempo_ms - ultimo_cambio) >= tiempo_fila) {
                    ultimo_cambio = tiempo_ms;
                    mostrar_fila(3, frames[frame_actual]);
                    estado = ESTADO_REPRODUCCION_FILA4;
                }
                break;

            case ESTADO_REPRODUCCION_FILA4:
                if ((tiempo_ms - ultimo_cambio) >= tiempo_fila) {
                    ultimo_cambio = tiempo_ms;
                    mostrar_fila(4, frames[frame_actual]);
                    estado = ESTADO_REPRODUCCION_FILA5;
                }
                break;

            case ESTADO_REPRODUCCION_FILA5:
                if ((tiempo_ms - ultimo_cambio) >= tiempo_fila) {
                    ultimo_cambio = tiempo_ms;
                    mostrar_fila(5, frames[frame_actual]);
                    estado = ESTADO_REPRODUCCION_FILA6;
                }
                break;

            case ESTADO_REPRODUCCION_FILA6:
                if ((tiempo_ms - ultimo_cambio) >= tiempo_fila) {
                    ultimo_cambio = tiempo_ms;
                    mostrar_fila(6, frames[frame_actual]);
                    estado = ESTADO_REPRODUCCION_FILA7;
                }
                break;

            case ESTADO_REPRODUCCION_FILA7:
                if ((tiempo_ms - ultimo_cambio) >= tiempo_fila) {
                    ultimo_cambio = tiempo_ms;
                    mostrar_fila(7, frames[frame_actual]);
                    // Al terminar de refrescar las 8 filas, se ha completado un ciclo de multiplexación.
                    // Se verifica si se ha mostrado el frame el tiempo suficiente.
                    if ((tiempo_ms - tiempo_inicio_frame) >= duracion_frame) {
                        // Si no es el último frame, avanzar al siguiente; si ya es el último, pasar a PAUSA.
                        if (frame_actual < (TOTAL_FRAMES - 1)) {
                            frame_actual++;
                        } else {
                            estado = ESTADO_PAUSA;
                            ultimo_cambio = tiempo_ms;  // Inicia la cuenta para la pausa
                            break;
                        }
                        // Reiniciar el tiempo de visualización del frame y comenzar de nuevo con la fila 0.
                        tiempo_inicio_frame = tiempo_ms;
                    }
                    estado = ESTADO_REPRODUCCION_FILA0;
                }
                break;

            case ESTADO_PAUSA:
                if ((tiempo_ms - ultimo_cambio) >= duracion_pausa) {
                    estado = ESTADO_INICIAL;
                }
                break;

            default:
                estado = ESTADO_INICIAL;
                break;
        }
    }
    
    return 0; // Nunca se alcanza
}

// ====================================================
// IMPLEMENTACIÓN DE LAS FUNCIONES
// ====================================================

// Configuración de puertos GPIO para la matriz LED (Ánodo Común):
// - Filas: inactivas en ALTO y se activan poniéndolas en BAJO.
// - Columnas: inactivas en BAJO y se activan poniéndolas en ALTO.
void config_gpio(void) {
    RCC->AHB1ENR |= RCC_AHB1ENR_GPIODEN | RCC_AHB1ENR_GPIOEEN;

    // Configurar FILAS (GPIOD, pines 0-7) como salida.
    FILA_PORT->MODER &= ~(0xFFFF);
    FILA_PORT->MODER |=  (0x5555);
    FILA_PORT->OTYPER &= ~FILA_MASK;
    FILA_PORT->OSPEEDR |=  0xFFFF;
    // Inicialmente, apagar filas (mantener en ALTO).
    FILA_PORT->ODR |= FILA_MASK;

    // Configurar COLUMNAS (GPIOE, pines 0-7) como salida.
    COLUMNA_PORT->MODER &= ~(0xFFFF);
    COLUMNA_PORT->MODER |=  (0x5555);
    COLUMNA_PORT->OTYPER &= ~COLUMNA_MASK;
    COLUMNA_PORT->OSPEEDR |=  0xFFFF;
    // Inicialmente, apagar columnas (mantener en BAJO).
    COLUMNA_PORT->ODR &= ~COLUMNA_MASK;
}

// Configuración de SysTick para generar interrupciones cada 1 ms.
void config_systick(void) {
    SysTick_Config(SystemCoreClock / 1000);
}

void SysTick_Handler(void) {
    tiempo_ms++;
}

// Función para mostrar una fila del frame actual en la matriz LED (ánodo común).
// - Filas: se activan poniéndolas en BAJO.
// - Columnas: se activan poniéndolas en ALTO.
void mostrar_fila(uint8_t fila_idx, const uint8_t frame[8]) {
    // Apagar todas las filas (poner en ALTO)
    FILA_PORT->ODR |= FILA_MASK;
    // Apagar todas las columnas (poner en BAJO)
    COLUMNA_PORT->ODR &= ~COLUMNA_MASK;

    // Activar la fila deseada (ponerla en BAJO)
    FILA_PORT->ODR &= ~(1 << fila_idx);

    // Configurar las columnas según el frame:
    // Si el bit es 1, activar la columna (ALTO) para encender el LED;
    // en caso contrario, dejar la columna en BAJO.
    for (uint8_t col = 0; col < 8; col++) {
        if ((frame[fila_idx] >> (7 - col)) & 0x01) {
            COLUMNA_PORT->ODR |= (1 << col);
        } else {
            COLUMNA_PORT->ODR &= ~(1 << col);
        }
    }
}
```