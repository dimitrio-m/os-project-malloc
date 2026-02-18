# Proyecto 1: Custom Memory Allocator (ula-malloc)

Bienvenido a tu primer gran reto de Sistemas Operativos. En este proyecto, dejarás de asumir que la memoria es mágica. Construirás desde cero tu propia librería de gestión de memoria dinámica, reemplazando las funciones estándar de C.

## 🎯 Objetivo

El objetivo es implementar una versión funcional y eficiente de las funciones `malloc`, `free`, `calloc` y `realloc`. Aprenderás a interactuar directamente con el sistema operativo para solicitar espacio en el *Heap* y a gestionar esa memoria manualmente usando estructuras de datos.

## 📋 Requisitos Funcionales (API)

Debes implementar las siguientes 4 funciones en el archivo `src/mm_malloc.c`. Las firmas deben ser exactas:

### 1. `void *my_malloc(size_t size)`

* **Qué hace:** Solicita un bloque de memoria de al menos `size` bytes.
* **Comportamiento:**
* Debe buscar primero si hay un bloque libre reutilizable (usando First-Fit o Best-Fit).
* Si no hay espacio, debe solicitar más memoria al sistema operativo usando `sbrk()`.
* Debe devolver un puntero al inicio de los datos (no al inicio de los metadatos).
* Si falla, devuelve `NULL`.



### 2. `void my_free(void *ptr)`

* **Qué hace:** Libera el bloque de memoria apuntado por `ptr`.
* **Comportamiento:**
* Marca el bloque como "libre" en la estructura de metadatos.
* **No** devuelve la memoria al sistema operativo, la guarda para futuros `malloc`.
* Si `ptr` es `NULL`, no hace nada.



### 3. `void *my_calloc(size_t nmemb, size_t size)`

* **Qué hace:** Asigna memoria para un array de `nmemb` elementos de `size` bytes cada uno.
* **Comportamiento:**
* Debe inicializar toda la memoria en ceros.
* Ayuda: Puedes reutilizar tu `my_malloc` y luego usar `memset`.



### 4. `void *my_realloc(void *ptr, size_t size)`

* **Qué hace:** Cambia el tamaño del bloque de memoria apuntado por `ptr` a `size` bytes.
* **Comportamiento:**
* Si el nuevo tamaño es menor, trunca los datos.
* Si es mayor, puede intentar expandir el bloque actual o asignar uno nuevo y copiar los datos (`memcpy`).



---

## 🚫 Reglas Inquebrantables

Violar cualquiera de estas reglas resultará en una nota de **0 puntos**.

1. **PROHIBIDO `stdlib.h`:** No puedes usar `malloc`, `free`, `realloc` o `calloc` originales. La única forma de obtener memoria del sistema es mediante `sbrk`.
2. **Solo C Estándar:** El código debe compilar en Linux (WSL/Ubuntu) usando `gcc`.
3. **No modificar los tests:** El archivo `mm_test.c` no se toca. Tu código debe pasar los tests tal como están.

---

## 🏗️ Guía de Implementación

### La Estructura de Datos (Metadatos)

Para gestionar la memoria, cada bloque que asignes debe tener un encabezado oculto (*header*) antes del puntero que entregas al usuario.

Se recomienda usar una **Lista Enlazada**.

```c
typedef struct block_meta {
    size_t size;             // Tamaño del bloque de datos
    struct block_meta *next; // Puntero al siguiente bloque
    int free;                // 1 si está libre, 0 si está ocupado
    int magic;               // Número mágico para depuración (opcional)
} block_meta;

```

**Visualización de la memoria:**
`[ HEADER (Metadatos) ]` -> `[ DATOS DEL USUARIO (Lo que retornas) ]` -> `[ HEADER SIGUIENTE ]`...

### Estrategia de Asignación

Puedes elegir cualquiera de estos dos algoritmos:

* **First-Fit (Recomendado):** Recorres la lista desde el inicio y usas el primer bloque libre que tenga suficiente tamaño.
* **Best-Fit:** Recorres toda la lista y eliges el bloque libre que tenga el tamaño más ajustado al solicitado (reduce fragmentación, pero es más lento).

---

## 🚀 Compilación y Ejecución

Hemos incluido un `Makefile` para facilitar tu vida.

1. **Limpiar compilaciones anteriores:**
```bash
make clean

```


2. **Compilar el proyecto:**
```bash
make

```


3. **Ejecutar los tests:**
```bash
./test_runner

```



---

## ⚖️ Criterios de Evaluación

La nota final (20%) se distribuye así:

| Criterio | Peso | Descripción |
| --- | --- | --- |
| **Funcionalidad Básica** | 30% | El código compila y `malloc`/`free` funcionan sin errores de segmentación. |
| **Coalescing & Reutilización** | 40% | El sistema reutiliza bloques liberados en lugar de pedir siempre memoria nueva (el heap no crece infinitamente). El sistema fusiona bloques adyacentes libres. Si el heap se fragmenta en bloques pequeños inutilizables, se pierden estos puntos.|
| **Robustez** | 15% | `calloc` y `realloc` funcionan correctamente y manejan casos bordes (punteros NULL, tamaño 0). |
| **Defensa Oral** | 15% | Capacidad para explicar tu aritmética de punteros y decisiones de diseño en vivo. |

---

## 📚 Recursos Recomendados

* **Lectura:** [OSTEP: Free Space Management (Capítulo 17)](https://pages.cs.wisc.edu/~remzi/OSTEP/vm-freespace.pdf) - *La Biblia para este proyecto.*
* **Manual de Linux:** Ejecuta `man sbrk` en tu terminal.
* **Depuración:** Aprende a usar `gdb` o `valgrind` para detectar errores de memoria.

---

**¡Buena suerte, ingenieros!**
