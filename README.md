# assemblylanguage
Repositorio a modo 'apuntes' o 'guia' de a poco para aprender a programar en assembly.

Antes de empezar: 

    - Utilizo DOSBox 0.74-3 (aplicacion nativa en Linux Mint) para crear un entorno donde un programa DOS vive en una computadora de la epoca. ¿Qué era DOS? Significa 'Disk Operating System'. Uno de los mas conocidos fue MS-DOS, desarrollado por Microsoft.

    Era un sistema operativo de la epoca de las primeras PCs, muy anterior a Windows moderno.

    En el entorno que uso, nuestro ensamblador es MASM (Microsoft Macro Assembler).

    Pensalo asi: Tu código
                MESSAGE.ASM
                    │
                    │ MASM
                    ▼
                MESSAGE.OBJ
                    │
                    │ LINK
                    ▼
                MESSAGE.EXE
                    │
                    │ DOSBox
                    ▼
                Programa ejecutándose

    - Cada cosa tiene una función:

    MASM → toma tu código Assembly (.ASM) y lo convierte en código objeto (.OBJ).
    LINK → toma el .OBJ y construye el ejecutable (.EXE).
    DOSBox → crea el entorno DOS necesario para ejecutar ese .EXE.
    8086 → es la arquitectura/procesador que estás programando.

    Por ejemplo, cuando escribís:

        mov ax, @data
        mov ds, ax

        mov ah, 09h
        mov dx, offset str
        int 21h

    MASM entiende esas instrucciones y directivas, y las transforma en código máquina que el 8086 puede ejecutar.

    Una distinción que te va a servir muchísimo:

    Assembly es el lenguaje; MASM es el ensamblador que traduce ese lenguaje.

    Y existen otros ensambladores, por ejemplo NASM, TASM y FASM. No necesariamente aceptan exactamente la misma sintaxis.

    - Intel 8086 → el CPU físico original, lanzado en 1978.
    - x86 → la familia de arquitecturas que evolucionó a partir del 8086.
    - 8086 Assembly → lenguaje ensamblador que utiliza las instrucciones y registros de esa arquitectura de 16 bits.

    Por ejemplo, estos registros:

        AX
        BX
        CX
        DX
        SI
        DI
        SP
        BP
    
    son registros característicos del 8086.

    Y estas instrucciones:

        MOV
        ADD
        SUB
        CMP
        JMP
        PUSH
        POP

    son parte del conjunto de instrucciones x86 que comenzó con el 8086.

    - Y acá aparece algo interesante

    El 8086 original era de 16 bits, pero la arquitectura fue evolucionando:

     8086 (16 bits)
        ↓
     80286
        ↓
     80386 (32 bits)
        ↓
     x86 / IA-32
        ↓
     x86-64 (64 bits)

    Por eso el procesador que yo tengo, un Ryzen 7 7800X3D actual no es un 8086, pero sí implementa la familia de instrucciones x86-64, que mantiene una enorme cantidad de compatibilidad hacia atrás con x86. 

    Así que estás empezando a estudiar algo que todavía está en la base de los procesadores modernos de AMD e Intel. La diferencia es que tu 8086 es muchísimo más simple y tiene registros de 16 bits, lo cual lo hace bastante bueno para aprender cómo funciona realmente un CPU.


### Como hacer nuestro primer 'Hello World' en un programa en ensamblador.

    .model small
    .stack 100h
    .data
            str db "Hello Assembly","$"
    .code
            mov ax,@data
            mov ds,ax

            mov ah, 09h
            mov dx, offset str
            int 21h

            mov ah,04ch
            int 21h
    end

    Existen cuatro secciones en los programas ensambladores:

    1. Seccion uno: Direccion de memoria / Memory direction
                    o
                    Modelo de variables de memoria / Memory variables models
                
                    Existen cinco modelos: El modelo especifica la cantidad total
                    de memoria que los programas necesitan. El programa necesita datos y codigo para ejecutarse, y ambos se almacenan en la memoria RAM. Asi que:

                    small, significa:
                        codigo: en un segmento
                        datos: en un segmento

                    ambos iguales...

                    el tamaño medium, significa:
                        codigo: en mas de un segmento
                        datos: en un segmento

                    - Un segmento, significa que el codigo es <= 64bits

                    el tamaño compact, significa:
                        codigo: en un segmento
                        datos: en mas de un segmento

                    Tambien existen los tamaños large, que significa codigo y datos en mas de un segmento.

                    Y el tamaño huge, que significa, toda la memoria disponible.

                    - Podemos seleccionar cualquier palabra clave segun nuestra situacion, nuestro problema y nuestro programa. Pero vamos a usar 'small' para la asignacion de memoria.

    2. La seccion dos de nuestro programa basico es: stack 100h, vamos a asignar memoria en el area de RAM para operaciones de pila. Podemos reservar .stack 100h donde H significa valores hexadecimales. Podemos seleciconar binario con B, decimal con D, hexadecimal con H y octal con O. Usaremos la forma hexadecimal.

    3. La seccion tres, es la seccion de datos: es donde podemos declarar nuestras variables.

        - Variables:

            Sintaxis: nombre de la variable / tipo de variable / valor inicial
            Por ejemplo:
                str db "Good morning", "$"
            el nombre es str, el tipo de la variable es db, y su valor es la cadena utilizada. El "$" es un terminador de la cadena, que le indica a DOS donde dejar de leer bytes de memoria.

            Reglas de declaracion para las variables:

            - El nombre de la variable debe tener solo ocho caracteres. Como maximo.
            - Se usan letras y digitos, pero no se permiten espacios. Por ejemplo: "a b" no esta permitido.
            - Solo se permite el guion bajo, no simbolos especiales en una variable. Podemos usar guion bajo pero no signos como @ o el $

            Esas son las reglas de nomenclatura.
        
        - Tipos de variables: 

            - DB (definir byte / Define Byte): reserva 1 byte = 8 bits de memoria. Se utiliza, entre otras cosas, para almacenar caracteres y cadenas de texto.
            Con signo: -128 a +127.
            Sin signo: 0 a 255.
            Ejemplo:
            letra DB 'A'

            - DW (definir palabra / Define Word): reserva 2 bytes = 16 bits de memoria.
            Con signo: -32.768 a +32.767.
            Sin signo: 0 a 65.535.
            Ejemplo:
            numero DW 1000

            - DD (definir palabra doble / Define Double Word): reserva 4 bytes = 32 bits de memoria.
            Con signo: -2³¹ a 2³¹ - 1, es decir, -2.147.483.648 a +2.147.483.647.
            Sin signo: 0 a 2³² - 1, es decir, 0 a 4.294.967.295.
            Ejemplo:
            numero DD 100000

            Para hacerlo un poco mas claro, hagamos esto:
            Tenemos 8 bits en memoria:
                00000000 los cuales pueden ser ceros y unos, no?

                Cuando reservamos 1 byte = 8 bits.
                Lo que definimos es, que va a haber 2 elevado a la 8 de combinaciones disponibles de ceros y unos, para representar un numero. Es decir, 2**8 = 256 combinaciones.

                La pregunta es: ¿qué números queremos representar con esas 256 posibilidades?

                2⁸ = 256 valores

                0 → 255
                ↑    ↑
                1º   256º valor

                Es por eso, que en numeros sin signo, si reservamos 8 bits, se puede almacenar ahi desde 0 valor, a 255 valor.

