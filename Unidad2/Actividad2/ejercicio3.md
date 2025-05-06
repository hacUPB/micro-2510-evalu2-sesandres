## Debes crear tres macros en C enfocadas en el trabajo con sistemas embebidos. Las macros se utilizarán para manipular registros del microcontrolador u otras operaciones similares. 

### 1. Aplicar una máscara para escribir en un registro del microcontrolador. Descripción: En sistemas embebidos, es común escribir en registros específicos de hardware utilizando máscaras para modificar solo los bits deseados, sin afectar el resto del registro. Crea una macro que aplique una máscara a un registro. La macro debe permitirte especificar el registro y la máscara, y establecer los bits correspondientes en ese registro. Elige un registro del microcontrolador, selecciona la máscara que se debería aplicar para realizar una tarea particular. Elige un nombre adecuado para esta macro. 

```
#define SET_REGISTER_BITS(reg, mask, value) \
    do { \
        reg = (reg & ~(mask)) | ((value) & (mask)); \
    } while(0)
```
### 2. Determinar si un periférico está presente en el microprocesador. Descripción: Los microcontroladores a menudo tienen registros de control de periféricos (como el registro PCC) que indican si ciertos periféricos, como los puertos, están habilitados. En esta tarea, deberás crear una macro para verificar si un periférico (ej. puerto E) está presente usando el registro de control. Crea una macro que verifique si el bit correspondiente a un periférico (ej. puerto E) está activado en el registro PCC.

```
#define IS_PERIPHERAL_ENABLED(pcc_reg, peripheral_bit) \
    ((pcc_reg & (1 << (peripheral_bit))) != 0)
```

### 3. Alternar un bit de un registro. Descripción: En algunos casos, es necesario alternar (cambiar) un bit específico de un registro, por ejemplo, para cambiar el estado de un LED o para activar/desactivar un periférico. Crea una macro que alterne el estado de un bit específico en un registro.

```
#define TOGGLE_BIT(reg, bit) \
    do { \
        reg ^= (1 << (bit)); \
    } while(0)
```

### 4. Piensa en otra macro que pudiera ser útil y que puedas usar en tus proyectos e impleméntala. 

Delay basado en ciclos de CPU
```
#define DELAY_CYCLES(cycles) \
    do { \
        for (volatile uint32_t i = 0; i < (cycles); i++) { \
            __asm__ volatile ("nop"); \
        } \
    } while(0)
```