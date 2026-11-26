---
layout: default
title: Programación
parent: Trabajos
nav_order: 1
permalink: /trabajos/programacion/
---

# Programación

## Sistema de inventario y ventas (Tiendita)
- **Lenguaje:** C
- **Descripción:** Programa de consola para administrar el inventario y las ventas del día de una tienda. Utiliza estructuras (`struct Producto`), memoria dinámica (`realloc` y `free`), aritmética de apuntadores y validación de entradas para agregar productos, consultar existencias y registrar ventas.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct {
    char nombre[40];
    float precio;
    int cantidad;
} Producto;

void limpiar_buffer(void) {
    int c;
    while ((c = getchar()) != '\n' && c != EOF) { }
}

void leer_nombre(const char *mensaje, char *destino, size_t tam) {
    do {
        printf("%s", mensaje);
        if (fgets(destino, (int)tam, stdin) != NULL) {
            destino[strcspn(destino, "\n")] = '\0';
        } else {
            destino[0] = '\0';
        }
        if (strlen(destino) == 0) {
            printf("Nombre invalido. Intenta de nuevo.\n");
        }
    } while (strlen(destino) == 0);
}

float leer_precio_valido(const char *mensaje) {
    float precio;
    while (1) {
        printf("%s", mensaje);
        if (scanf("%f", &precio) == 1) {
            limpiar_buffer();
            if (precio > 0.0f) {
                return precio;
            }
            printf("Precio invalido. Intenta de nuevo.\n");
        } else {
            printf("Precio invalido. Intenta de nuevo.\n");
            limpiar_buffer();
        }
    }
}

int leer_cantidad_valida(const char *mensaje, int max_permitido) {
    int cant;
    while (1) {
        printf("%s", mensaje);
        if (scanf("%d", &cant) == 1) {
            limpiar_buffer();
            if (cant <= 0) {
                printf("Cantidad invalida. Intenta de nuevo.\n");
            } else if (max_permitido >= 0 && cant > max_permitido) {
                printf("Cantidad invalida (solo hay %d disponibles). Intenta de nuevo.\n", max_permitido);
            } else {
                return cant;
            }
        } else {
            printf("Cantidad invalida. Intenta de nuevo.\n");
            limpiar_buffer();
        }
    }
}

void agregar(Producto **lista, int *total, int *cap) {
    if (*total >= *cap) {
        int nueva_cap = (*cap == 0) ? 4 : (*cap) * 2;
        Producto *temp = realloc(*lista, (size_t)nueva_cap * sizeof(Producto));
        if (temp == NULL) {
            printf("Error de memoria.\n");
            return;
        }
        *lista = temp;
        *cap = nueva_cap;
    }

    Producto *p = *lista + *total;
    leer_nombre("\nNombre: ", p->nombre, sizeof(p->nombre));
    p->precio = leer_precio_valido("Precio: ");
    p->cantidad = leer_cantidad_valida("Cantidad: ", -1);

    (*total)++;
    printf("Producto \"%s\" agregado.\n", p->nombre);
}

void ver_inventario(const Producto *lista, int total) {
    printf("\n--- INVENTARIO ---\n");
    if (total == 0) {
        printf("Vacio.\n");
        return;
    }

    const Producto *p = lista;
    const Producto *fin = lista + total;
    while (p < fin) {
        printf("%-22s | $%.2f | Cant: %d\n",
               p->nombre, p->precio, p->cantidad);
        p++;
    }
}

Producto *buscar_nombre(Producto *lista, int total, const char *nombre) {
    Producto *p = lista;
    Producto *fin = lista + total;
    while (p < fin) {
        if (strcmp(p->nombre, nombre) == 0) {
            return p;
        }
        p++;
    }
    return NULL;
}

void vender(Producto *lista, int total, float *ventas) {
    char nombre[40];
    Producto *p = NULL;

    while (1) {
        leer_nombre("\nNombre del producto a vender: ", nombre, sizeof(nombre));
        p = buscar_nombre(lista, total, nombre);
        if (p == NULL) {
            printf("Producto invalido (no existe). Intenta de nuevo.\n");
            continue;
        }
        if (p->cantidad <= 0) {
            printf("Producto sin existencias. Intenta con otro.\n");
            continue;
        }
        break;
    }

    printf("Disponible de \"%s\": %d\n", p->nombre, p->cantidad);
    int cant = leer_cantidad_valida("Cantidad a vender: ", p->cantidad);

    float cobrar = p->precio * (float)cant;
    p->cantidad -= cant;
    *ventas += cobrar;

    printf("Venta realizada. Total: $%.2f\n", cobrar);
}

int main(void) {
    Producto *inventario = NULL;
    int total = 0;
    int capacidad = 0;
    float ventas_dia = 0.0f;
    int op;

    do {
        printf("\n=== MENU TIENDITA ===\n");
        printf("1. Agregar producto\n");
        printf("2. Ver inventario\n");
        printf("3. Vender\n");
        printf("4. Salir\n");
        printf("Elige una opcion: ");

        if (scanf("%d", &op) != 1) {
            limpiar_buffer();
            op = -1;
        } else {
            limpiar_buffer();
        }

        switch (op) {
            case 1:
                agregar(&inventario, &total, &capacidad);
                break;
            case 2:
                ver_inventario(inventario, total);
                break;
            case 3:
                vender(inventario, total, &ventas_dia);
                break;
            case 4:
                printf("Total vendido hoy: $%.2f\n", ventas_dia);
                break;
            default:
                printf("Opcion no valida.\n");
                break;
        }
    } while (op != 4);

    free(inventario);
    return 0;
}
```

## Práctica 2: Análisis de calificaciones e histograma
- **Lenguaje:** C
- **Descripción:** Programa con menú interactivo para capturar hasta 50 calificaciones, calcular la nota máxima y mínima (con conteo de alumnos), obtener el promedio y generar un histograma vertical por rangos en consola.

```c
#include <stdio.h>

// Funcion para limpiar la "basura" en caso de no introducir lo indicado
void limpiar_bufer(void) {
    while (getchar() != '\n' && getchar() != EOF);
}

// Funcion para llenar el arreglo "calificaciones"
int llenar_Calificaciones(double calificaciones[]) {
    int limite;
    do {
        printf("Ingresa cuantas calificaciones vas a ingresar (Maximo 50): \n");

        if (scanf("%d", &limite) != 1) {
            printf("Entrada invalida. Ingresa un numero entero.\n");
            limpiar_bufer();
            limite = 0;
            continue;
        }
        limpiar_bufer();

        if (limite <= 0 || limite > 50) {
            printf("Numero no valido. Intenta de nuevo (1 a 50).\n");
        }
    } while (limite <= 0 || limite > 50);

    for (int i = 0; i < limite; i++) {
        printf("Ingresa tu calificacion %d: ", i + 1);
        if (scanf("%lf", &calificaciones[i]) != 1) {
            printf("Entrada invalida.\n");
            limpiar_bufer();
            i--;
            continue;
        }
        limpiar_bufer();
    }

    return limite;
}

// Funcion para calcular la calificacion mas alta
void Nota_Maxima(double calificaciones[], int limite, int *contador, double *max) {
    *max = calificaciones[0];
    *contador = 1;

    for (int i = 1; i < limite; i++) {
        if (calificaciones[i] > *max) {
            *max = calificaciones[i];
            *contador = 1;
        } else if (calificaciones[i] == *max) {
            (*contador)++;
        }
    }
}

// Funcion para calcular la calificacion minima
void Nota_Minima(double calificaciones[], int limite, int *contadox, double *min) {
    *min = calificaciones[0];
    *contadox = 1;

    for (int i = 1; i < limite; i++) {
        if (calificaciones[i] < *min) {
            *min = calificaciones[i];
            *contadox = 1;
        } else if (calificaciones[i] == *min) {
            (*contadox)++;
        }
    }
}

// Funcion para calcular promedio
double Promedio(double calificaciones[], int limite) {
    double suma = 0.0;
    for (int i = 0; i < limite; i++) {
        suma += calificaciones[i];
    }
    return suma / limite;
}

// Histograma por rangos de 10 (0-9, 10-19, ..., 60+)
void Histograma(double calificaciones[], int limite) {
    int notas[7] = {0};
    int maxn = 0;

    for (int i = 0; i < limite; i++) {
        int entero = (int)calificaciones[i];
        int indice = entero / 10;

        if (indice < 0) indice = 0;
        if (indice > 6) indice = 6;
        notas[indice]++;
    }

    for (int i = 0; i < 7; i++) {
        if (notas[i] > maxn) {
            maxn = notas[i];
        }
    }

    if (maxn == 0 || limite <= 0) {
        printf("No hay calificaciones validas para mostrar.\n");
        return;
    }

    printf("\nHISTOGRAMA DE CALIFICACIONES\n");

    for (int h = maxn; h >= 1; h--) {
        printf("%2d > ", h);
        for (int i = 0; i < 7; i++) {
            if (notas[i] >= h) {
                printf(" ##### ");
            } else {
                printf("       ");
            }
        }
        printf("\n");
    }

    printf("   +");
    for (int i = 0; i < 7; i++) {
        printf("-------");
    }
    printf("\n");

    printf("    ");
    for (int i = 0; i < 7; i++) {
        if (i < 6) {
            printf(" %02d-%02d ", i * 10, i * 10 + 9);
        } else {
            printf("  60+  ");
        }
    }
    printf("\n\n");
}

int main(void) {
    int opcion, contador, contadox;
    int limite = 0;
    int llenado = 0; // 0 = no se han llenado calificaciones, 1 = ya estan llenas
    double calificaciones[50], max, min, promedio;

    printf("Practica 2\n");
    printf("Christian Santiago Munoz Olivera\n");
    printf("Presiona la tecla Enter para continuar...\n");
    limpiar_bufer();

    do {
        printf("\nMenu\n");
        printf("1. Llenar calificaciones\n");
        printf("2. Nota maxima\n");
        printf("3. Nota minima\n");
        printf("4. Promedio\n");
        printf("5. Histograma\n");
        printf("6. Salir\n");
        printf("Elige una opcion: ");

        if (scanf("%d", &opcion) != 1) {
            printf("Entrada invalida.\n");
            limpiar_bufer();
            opcion = 0;
            continue;
        }
        limpiar_bufer();

        switch (opcion) {
            case 1:
                limite = llenar_Calificaciones(calificaciones);
                llenado = 1;
                printf("Calificaciones guardadas");
                break;

            case 2:
                if (llenado == 0) {
                    printf("Primero llena las calificaciones (Opcion 1)");
                    break;
                }
                Nota_Maxima(calificaciones, limite, &contador, &max);
                printf("La calificacion mayor es: %.2f\n", max);
                printf("La cantidad de alumnos con la nota mayor es: %d\n", contador);
                break;

            case 3:
                if (llenado == 0) {
                    printf("Primero llena las calificaciones (Opcion 1)");
                    break;
                }
                Nota_Minima(calificaciones, limite, &contadox, &min);
                printf("La calificacion menor es: %.2f\n", min);
                printf("La cantidad de alumnos con la nota menor es: %d\n", contadox);
                break;

            case 4:
                if (llenado == 0) {
                    printf("Primero llena las calificaciones (Opcion 1)");
                    break;
                }
                Promedio(calificaciones, limite);
                printf("El promedio de las calificaciones es: %.2f\n", promedio);
                break;

            case 5:
                if (llenado == 0) {
                    printf("Primero llena las calificaciones (Opcion 1)");
                    break;
                }
                Histograma(calificaciones, limite);
                break;

            case 6:
                printf("Saliendo....\n");
                break;

            default:
                printf("Opcion no valida\n");
                break;
        }
    } while (opcion != 6);

    return 0;
}
```

## Práctica 1: Fundamentos de programación (Fechas, primos y figuras)
- **Lenguaje:** C
- **Descripción:** Programa modular con menú principal y submenú que permite verificar años bisiestos, calcular la diferencia de días entre dos fechas válidas, determinar si un número es primo y dibujar figuras geométricas en consola (rectángulo, rombo, triángulo isósceles y triángulo rectángulo).

```c
#include <stdio.h>

int viciesto(int ano) {
    return (ano % 4 == 0 && ano % 100 != 0) || ano % 400 == 0;
}

int dias_desde_inicio(int dia, int mes, int ano) {
    int dias_por_mes[] = {31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
    int dias = (ano - 1) * 365 + (ano - 1) / 4 - (ano - 1) / 100 + (ano - 1) / 400;

    if (viciesto(ano)) {
        dias_por_mes[1] = 29;
    }
    for (int i = 0; i < mes - 1; i++) {
        dias += dias_por_mes[i];
    }
    return dias + dia;
}

int fecha_valida(int dia, int mes, int ano) {
    int dias_por_mes[] = {31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};

    if (ano < 1 || ano > 9999 || mes < 1 || mes > 12) {
        return 0;
    }
    if (viciesto(ano)) {
        dias_por_mes[1] = 29;
    }
    return dia >= 1 && dia <= dias_por_mes[mes - 1];
}

int primo_o_no(int numero) {
    if (numero < 2) {
        return 0;
    }
    for (int i = 2; i < numero; i++) {
        if (numero % i == 0) {
            return 0;
        }
    }
    return 1;
}

void rectangulo(int baser, int altura) {
    for (int i = 0; i < baser; i++) {
        for (int j = 0; j < altura; j++) {
            printf("*");
        }
        printf("\n");
    }
}

void rombo(int tamano) {
    for (int i = 0; i < tamano; i++) {
        for (int j = 0; j < tamano - i - 1; j++) {
            printf(" ");
        }
        for (int j = 0; j < 2 * i + 1; j++) {
            printf("*");
        }
        printf("\n");
    }
    for (int i = tamano - 2; i >= 0; i--) {
        for (int j = 0; j < tamano - i - 1; j++) {
            printf(" ");
        }
        for (int j = 0; j < 2 * i + 1; j++) {
            printf("*");
        }
        printf("\n");
    }
}

void triangulo_isoseles(int diagonal) {
    for (int i = 0; i < diagonal; i++) {
        for (int j = 0; j < diagonal - i - 1; j++) {
            printf(" ");
        }
        if (i == 0) {
            printf("*");
        } else if (i == diagonal - 1) {
            for (int j = 0; j < 2 * i + 1; j++) {
                printf("*");
            }
        } else {
            printf("*");
            for (int j = 0; j < 2 * i - 1; j++) {
                printf(" ");
            }
            printf("*");
        }
        printf("\n");
    }
}

void triangulo_rectangulo(int alturax) {
    for (int i = 0; i < alturax; i++) {
        for (int j = 0; j <= i; j++) {
            if (j == 0 || j == i || i == alturax - 1) {
                printf("*");
            } else {
                printf(" ");
            }
        }
        printf("\n");
    }
}

int main(void) {
    int opcion, option, baser, altura, tamano, diagonal, ano, alturax;

    printf("Practica 1, Fundamentos de programacion\n");
    printf("Christian Santiago Munoz Olivera\n");
    printf("Presiona la tecla Enter para comenzar\n");
    while (getchar() != '\n');

    do {
        printf("\nMenu principal\n");
        printf("1. Verificar si es un ano bisiesto\n");
        printf("2. Ver diferencia de dias\n");
        printf("3. Determinar si es primo o no\n");
        printf("4. Dibujar figura\n");
        printf("5. Salir\n");

        if (scanf("%d", &opcion) != 1) {
            printf("Entrada invalida. Por favor ingresa un numero.\n");
            while (getchar() != '\n' && getchar() != EOF);
            continue;
        }
        while (getchar() != '\n' && getchar() != EOF);

        switch (opcion) {
            case 1:
                printf("Ingresa el ano a saber si es bisiesto en formato AAAA\n");
                if (scanf("%d", &ano) != 1) {
                    printf("Entrada invalida.\n");
                    while (getchar() != '\n' && getchar() != EOF);
                    break;
                }
                while (getchar() != '\n' && getchar() != EOF);

                if (viciesto(ano)) {
                    printf("El ano es bisiesto\n");
                } else {
                    printf("El ano no es bisiesto\n");
                }
                break;

            case 2: {
                int dia1, mes1, ano1, dia2, mes2, ano2;
                printf("Ingresa la primera fecha (DD MM AAAA): ");
                if (scanf("%d %d %d", &dia1, &mes1, &ano1) != 3) {
                    printf("Formato de fecha invalido.\n");
                    while (getchar() != '\n' && getchar() != EOF);
                    break;
                }
                while (getchar() != '\n' && getchar() != EOF);

                printf("Ingresa la segunda fecha (DD MM AAAA): ");
                if (scanf("%d %d %d", &dia2, &mes2, &ano2) != 3) {
                    printf("Formato de fecha invalido.\n");
                    while (getchar() != '\n' && getchar() != EOF);
                    break;
                }
                while (getchar() != '\n' && getchar() != EOF);

                if (!fecha_valida(dia1, mes1, ano1) || !fecha_valida(dia2, mes2, ano2)) {
                    printf("Una o ambas fechas no son validas.\n");
                } else {
                    int diferencia = dias_desde_inicio(dia1, mes1, ano1) - dias_desde_inicio(dia2, mes2, ano2);
                    if (diferencia < 0) {
                        diferencia = -diferencia;
                    }
                    printf("La diferencia es de %d dias\n", diferencia);
                }
                break;
            }

            case 3:
                printf("Ingresa tu numero: ");
                if (scanf("%d", &numero) != 1) {
                    printf("Entrada invalida.\n");
                    while (getchar() != '\n' && getchar() != EOF);
                    break;
                }
                while (getchar() != '\n' && getchar() != EOF);

                if (primo_o_no(numero)) {
                    printf("El %d es primo\n", numero);
                } else {
                    printf("El %d no es primo\n", numero);
                }
                break;

            case 4:
                do {
                    printf("Sub-Menu\n");
                    printf("1. Rectangulo\n");
                    printf("2. Rombo\n");
                    printf("3. Contorno de triangulo isoseles\n");
                    printf("4. Contorno de triangulo rectangulo\n");
                    printf("5. Salir\n");

                    if (scanf("%d", &option) != 1) {
                        printf("Entrada invalida.\n");
                        while (getchar() != '\n' && getchar() != EOF);
                        continue;
                    }
                    while (getchar() != '\n' && getchar() != EOF);

                    switch (option) {
                        case 1:
                            printf("Ingresa el valor de la base:\n");
                            if (scanf("%d", &baser) != 1) {
                                printf("Entrada invalida.\n");
                                while (getchar() != '\n' && getchar() != EOF);
                                break;
                            }
                            while (getchar() != '\n' && getchar() != EOF);

                            printf("Ingresa el valor de la altura:\n");
                            if (scanf("%d", &altura) != 1) {
                                printf("Entrada invalida.\n");
                                while (getchar() != '\n' && getchar() != EOF);
                                break;
                            }
                            while (getchar() != '\n' && getchar() != EOF);
                            rectangulo(baser, altura);
                            break;

                        case 2:
                            printf("Ingresa el valor de la diagonal:\n");
                            if (scanf("%d", &tamano) != 1) {
                                printf("Entrada invalida.\n");
                                while (getchar() != '\n' && getchar() != EOF);
                                break;
                            }
                            while (getchar() != '\n' && getchar() != EOF);
                            rombo(tamano);
                            break;

                        case 3:
                            printf("Ingresa el valor de la diagonal:\n");
                            if (scanf("%d", &diagonal) != 1) {
                                printf("Entrada invalida.\n");
                                while (getchar() != '\n' && getchar() != EOF);
                                break;
                            }
                            while (getchar() != '\n' && getchar() != EOF);
                            triangulo_isoseles(diagonal);
                            break;

                        case 4:
                            printf("Ingresa la altura del triangulo:\n");
                            if (scanf("%d", &alturax) != 1) {
                                printf("Entrada invalida.\n");
                                while (getchar() != '\n' && getchar() != EOF);
                                break;
                            }
                            while (getchar() != '\n' && getchar() != EOF);
                            triangulo_rectangulo(alturax);
                            break;

                        case 5:
                            printf("Saliendo al menu principal\n");
                            break;
                    }
                } while (option != 5);
                break;

            case 5:
                printf("Saliendo......\n");
                break;

            default:
                printf("Opcion no valida\n");
                break;
        }
    } while (opcion != 5);

    return 0;
}
