---
layout: default
title: Matemáticas Discretas
parent: Trabajos
nav_order: 2
permalink: /trabajos/matematicas-discretas/
---

# Matemáticas Discretas

## 1. Grafo del Metro CDMX
- **Lenguaje:** Python (`networkx`, `matplotlib`)
- **Descripción:** Modelado y visualización de la red del Metro de la Ciudad de México como un grafo no dirigido, conectando estaciones por línea y dibujando el mapa completo con `kamada_kawai_layout`.
- **Repositorio original:** [Grafo Metro CDMX](https://www.google.com/search?q=https%3A%2F%2Fgithub.com%2FChristianMuOl20%2FLogica%2Fblob%2Fmain%2FGrafo%2520Metro%2520CDMX)

```python
import networkx as nx
import matplotlib.pyplot as plt

metro_cdmx = nx.Graph()

# Definir función para agregar líneas
def agregar_linea(grafo, lista_estaciones, nombre_linea):
    for i in range(len(lista_estaciones) - 1):
        grafo.add_edge(lista_estaciones[i], lista_estaciones[i + 1], linea=nombre_linea)

# Definición de todas las líneas
linea1_rosa = ["Observatorio", "Tacubaya", "Balderas", "Pino Suarez", "Candelaria", "San Lazaro", "Pantitlan"]
linea2_azul = ["Cuatro Caminos", "Tacubaya", "Hidalgo", "Bellas Artes", "Pino Suarez", "Chabacano", "Tasqueña"]
linea3_verde = ["Indios Verdes", "La Raza", "Hidalgo", "Balderas", "Centro Médico", "Zapata", "Universidad"]

linea4_amarilla = [
    "Martín Carrera", "Talismán", "Consulado", "Canal del Norte",
    "Morelos", "Candelaria", "Fray Servando", "Jamaica", "Santa Anita"
]

linea5_amarilla_claro = [
    "Politécnico", "Instituto del Petróleo", "Autobuses del Norte",
    "La Raza", "Misterios", "Valle Gómez", "Consulado", "Terminal Aérea",
    "Hangares", "Pantitlán"
]

linea6_roja = [
    "El Rosario", "Tezozómoc", "UAM-Azcapotzalco", "Ferrería/Arena Ciudad de México",
    "Norte 45", "Vallejo", "Instituto del Petróleo", "Lindavista",
    "Deportivo 18 de Marzo", "Martín Carrera"
]

linea7_naranja = [
    "El Rosario", "Aquiles Serdán", "Camarones", "Tacuba", "San Joaquín",
    "Polanco", "Auditorio", "Constituyentes", "Tacubaya", "San Pedro de los Pinos",
    "San Antonio", "Mixcoac", "Barranca del Muerto"
]

linea8_verde_claro = [
    "Garibaldi/Lagunilla", "Bellas Artes", "San Juan de Letrán", "Salto del Agua",
    "Doctores", "Obrera", "Chabacano", "La Viga", "Santa Anita",
    "Coyuya", "Iztacalco", "Apatlaco", "Aculco", "Escuadrón 201",
    "Atlalilco", "Iztapalapa", "Cerro de la Estrella", "UAM-I", "Constitución de 1917"
]

linea9_cafe = [
    "Tacubaya", "Patriotismo", "Chilpancingo", "Centro Médico",
    "Lázaro Cárdenas", "Chabacano", "Jamaica", "Mixiuhca",
    "Velódromo", "Ciudad Deportiva", "Puebla", "Pantitlán"
]

lineaA_morada = [
    "Pantitlán", "Agrícola Oriental", "Canal de San Juan", "Tepalcates",
    "Guelatao", "Peñón Viejo", "Acatitla", "Santa Marta", "Los Reyes", "La Paz"
]

lineaB_gris = [
    "Buenavista", "Guerrero", "Garibaldi/Lagunilla", "Lagunilla",
    "Tepito", "Morelos", "San Lázaro", "Ricardo Flores Magón",
    "Romero Rubio", "Oceanía", "Deportivo Oceanía", "Bosque de Aragón",
    "Villa de Aragón", "Nezahualcóyotl", "Impulsora", "Río de los Remedios",
    "Múzquiz", "Ecatepec", "Olímpica", "Plaza Aragón", "Ciudad Azteca"
]

linea12_dorada = [
    "Mixcoac", "Insurgentes Sur", "Hospital 20 de Noviembre", "Zapata",
    "Parque de los Venados", "Eje Central", "Ermita", "Mexicaltzingo",
    "Atlalilco", "Culhuacán", "San Andrés Tomatlán", "Lomas de Estrella",
    "Calle 11", "Periférico Oriente", "Tezonco", "Olivos",
    "Nopalera", "Zapotitlán", "Tlaltenco", "Tláhuac"
]

# Agregar líneas al grafo
agregar_linea(metro_cdmx, linea1_rosa, "Rosa")
agregar_linea(metro_cdmx, linea2_azul, "Azul")
agregar_linea(metro_cdmx, linea3_verde, "Verde")
agregar_linea(metro_cdmx, linea4_amarilla, "Amarilla")
agregar_linea(metro_cdmx, linea5_amarilla_claro, "Amarillo Claro")
agregar_linea(metro_cdmx, linea6_roja, "Roja")
agregar_linea(metro_cdmx, linea7_naranja, "Naranja")
agregar_linea(metro_cdmx, linea8_verde_claro, "Verde Claro")
agregar_linea(metro_cdmx, linea9_cafe, "Café")
agregar_linea(metro_cdmx, lineaA_morada, "Morada")
agregar_linea(metro_cdmx, lineaB_gris, "Gris-Verde")
agregar_linea(metro_cdmx, linea12_dorada, "Dorada")

# Configurar figura y visualización
plt.figure(figsize=(22, 22))
pos_completo = nx.kamada_kawai_layout(metro_cdmx)

nx.draw_networkx_nodes(metro_cdmx, pos_completo, node_color="skyblue", node_size=250, alpha=0.9)
nx.draw_networkx_edges(metro_cdmx, pos_completo, alpha=0.4, edge_color="gray", width=1.5)
nx.draw_networkx_labels(metro_cdmx, pos_completo, font_size=7, font_family="sans-serif", verticalalignment="bottom")

plt.title("Mapa de Red del Metro de la Ciudad de México (Grafo Completo)", fontsize=18, fontweight='bold')
plt.axis('off')
plt.show()
```

## 2. Búsqueda de caminos en el Metro CDMX (Dijkstra)
- **Lenguaje:** Python (`numpy`, `networkx`, `matplotlib`)
- **Descripción:** Script complementario que asigna coordenadas aproximadas a las estaciones y utiliza el algoritmo de Dijkstra para calcular y resaltar la ruta más corta entre dos estaciones del Metro CDMX.
- **Repositorio original:** [Busqueda de caminos Metro CDMX](https://www.google.com/search?q=https%3A%2F%2Fgithub.com%2FChristianMuOl20%2FLogica%2Fblob%2Fmain%2FBusqueda%2520de%2520caminos%2520Metro%2520CDMX)

```python
import numpy as np

# Diccionario de coordenadas aproximadas (X, Y) para dar una distribución realista al mapa del Metro CDMX
# Basado en la geografía relativa de las terminales y transbordos clave
coordenadas_metro = {
    # Línea 1
    "Observatorio": (-4, -1),
    "Tacubaya": (-3, -1),
    "Balderas": (-1, 0),
    "Pino Suarez": (1, 0),
    "Candelaria": (2, 1),
    "San Lazaro": (3, 2),
    "Pantitlan": (6, 2),
    # Línea 2 adicionales
    "Cuatro Caminos": (-4, 3),
    "Hidalgo": (-1, 2),
    "Bellas Artes": (0, 2),
    "Chabacano": (1, -2),
    "Tasbaqueña": (1, -6),
    # Línea 3 adicionales
    "Indios Verdes": (-1, 6),
    "La Raza": (-1, 4),
    "Centro Médico": (-1, -2),
    "Zapata": (-1, -4),
    "Universidad": (-1, -7),
    # Línea 4 adicionales
    "Martín Carrera": (2, 5),
    "Talismán": (2, 4),
    "Consulado": (2, 3),
    "Canal del Norte": (2, 2),
    "Morelos": (2, 1.5),
    "Fray Servando": (2, 0),
    "Jamaica": (2, -2),
    "Santa Anita": (2, -3),
    # Línea 5 adicionales
    "Politécnico": (-2, 5),
    "Instituto del Petróleo": (-2, 4),
    "Autobuses del Norte": (-1.5, 4),
    "Misterios": (0, 3.5),
    "Valle Gómez": (1, 3),
    "Terminal Aérea": (4, 3),
    "Hangares": (5, 2.5),
    # Línea 6 adicionales
    "El Rosario": (-4, 5),
    "Tezozómoc": (-3.5, 5),
    "UAM-Azcapotzalco": (-3, 5),
    "Ferrería/Arena Ciudad de México": (-2.5, 5),
    "Norte 45": (-2.2, 5),
    "Vallejo": (-2.1, 5),
    "Lindavista": (-1.5, 5),
    "Deportivo 18 de Marzo": (0.5, 5),
    # Línea 7 adicionales
    "Aquiles Serdán": (-4, 4),
    "Camarones": (-4, 3),
    "Tacuba": (-4, 2),
    "San Joaquín": (-4, 1),
    "Polanco": (-4, 0),
    "Auditorio": (-4, -0.5),
    "Constituyentes": (-3.5, -0.5),
    "San Pedro de los Pinos": (-3, -2),
    "San Antonio": (-3, -3),
    "Mixcoac": (-3, -4),
    "Barranca del Muerto": (-3, -5),
    # Línea 8 adicionales
    "Garibaldi/Lagunilla": (0, 3),
    "San Juan de Letrán": (0, 1),
    "Salto del Agua": (0, 0),
    "Doctores": (0, -1),
    "Obrera": (0, -1.5),
    "La Viga": (1.5, -2.5),
    "Coyuya": (3, -3),
    "Iztacalco": (3.5, -3.5),
    "Apatlaco": (4, -4),
    "Aculco": (4.5, -4.5),
    "Escuadrón 201": (5, -5),
    "Atlalilco": (4, -6),
    "Iztapalapa": (5, -6),
    "Cerro de la Estrella": (5.5, -6),
    "UAM-I": (6, -6),
    "Constitución de 1917": (7, -6),
    # Línea 9 adicionales
    "Patriotismo": (-2.5, -1.5),
    "Chilpancingo": (-2, -1.8),
    "Lázaro Cárdenas": (0.5, -2),
    "Mixiuhca": (3, -2),
    "Velódromo": (4, -1),
    "Ciudad Deportiva": (4.5, -0.5),
    "Puebla": (5, 1),
    # Línea A adicionales
    "Pantitlán": (6, 2),
    "Agrícola Oriental": (6.5, 1.5),
    "Canal de San Juan": (7, 1),
    "Tepalcates": (7.5, 0.5),
    "Guelatao": (8, 0),
    "Peñón Viejo": (8.5, -0.5),
    "Acatitla": (9, -1),
    "Santa Marta": (9.5, -1.5),
    "Los Reyes": (10, -2),
    "La Paz": (10.5, -2.5),
    # Línea B adicionales
    "Buenavista": (-2, 2.5),
    "Guerrero": (-1.5, 2.5),
    "Lagunilla": (0.5, 2.8),
    "Tepito": (1, 2.5),
    "San Lázaro": (3, 2),
    "Ricardo Flores Magón": (3.5, 3.5),
    "Romero Rubio": (4, 4),
    "Oceanía": (4.5, 4.5),
    "Deportivo Oceanía": (5, 5),
    "Bosque de Aragón": (5.5, 5.5),
    "Villa de Aragón": (6, 6),
    "Nezahualcóyotl": (6.5, 6.5),
    "Impulsora": (7, 7),
    "Río de los Remedios": (7.5, 7.5),
    "Múzquiz": (8, 8),
    "Ecatepec": (8.5, 8.5),
    "Olímpica": (9, 9),
    "Plaza Aragón": (9.5, 9.5),
    "Ciudad Azteca": (10, 10),
    # Línea 12 adicionales
    "Insurgentes Sur": (-2.5, -4),
    "Hospital 20 de Noviembre": (-2, -4),
    "Parque de los Venados": (-0.5, -4),
    "Eje Central": (0, -4),
    "Ermita": (1, -4),
    "Mexicaltzingo": (2.5, -4),
    "Culhuacán": (3, -5),
    "San Andrés Tomatlán": (3.5, -5.5),
    "Lomas de Estrella": (4, -6.5),
    "Calle 11": (4.5, -7),
    "Periférico Oriente": (5, -7.5),
    "Tezonco": (5.5, -8),
    "Olivos": (6, -8.5),
    "Nopalera": (6.5, -9),
    "Zapotitlán": (7, -9.5),
    "Tlaltenco": (7.5, -10),
    "Tláhuac": (8, -10.5)
}

# Para cualquier estación que no hayamos mapeado explícitamente, le asignamos una posición aleatoria controlada
pos_final = {}
for nodo in metro_cdmx.nodes():
    if nodo in coordenadas_metro:
        pos_final[nodo] = np.array(coordenadas_metro[nodo])
    else:
        pos_final[nodo] = np.random.rand(2) * 10 - 5
def encontrar_y_graficar_ruta(origen, destino):
    if origen not in metro_cdmx or destino not in metro_cdmx:
        print("Una o ambas estaciones no existen en el mapa.")
        return

    # Calcular el camino más corto usando el algoritmo de Dijkstra (peso por defecto = 1 en cada arista)
    camino = nx.dijkstra_path(metro_cdmx, source=origen, target=destino)
    print(f"Ruta más corta desde {origen} a {destino}:")
    print(" -> ".join(camino))

    # Crear lista de aristas que componen el camino
    aristas_camino = list(zip(camino[:-1], camino[1:]))

    # Graficar
    plt.figure(figsize=(18, 18))

    # Dibujar todos los nodos y aristas del fondo en colores tenues
    nx.draw_networkx_nodes(metro_cdmx, pos_final, node_color="lightgray", node_size=150, alpha=0.6)
    nx.draw_networkx_edges(metro_cdmx, pos_final, edge_color="lightgray", alpha=0.5, width=1.0)

    # Resaltar las estaciones del camino
    nx.draw_networkx_nodes(metro_cdmx, pos_final, nodelist=camino, node_color="red", node_size=250, alpha=0.9)
    # Resaltar las conexiones del camino
    nx.draw_networkx_edges(metro_cdmx, pos_final, edgelist=aristas_camino, edge_color="darkred", width=3.5)

    # Mostrar etiquetas solo de las estaciones de la ruta para no saturar
    etiquetas_filtradas = {nodo: nodo for nodo in camino}
    nx.draw_networkx_labels(metro_cdmx, pos_final, labels=etiquetas_filtradas, font_size=9, font_weight="bold", verticalalignment="bottom")

    plt.title(f"Dijkstra: Ruta de {origen} a {destino}", fontsize=16, fontweight='bold')
    plt.axis('off')
    plt.show()

# Ejemplo de uso:
encontrar_y_graficar_ruta("La Paz", "Universidad")
```

## 3. Recursión y Combinatoria
- **Lenguaje:** Python
- **Descripción:** Implementación recursiva mediante búsqueda en profundidad (DFS) para generar permutaciones, combinaciones de tamaño `r` y cálculo de factorial.
- **Repositorio original:** [Recursión y Combinatoria](https://www.google.com/search?q=https%3A%2F%2Fgithub.com%2FChristianMuOl20%2FLogica%2Fblob%2Fmain%2FRecursi%25C3%25B3n%2520y%2520Combinatoria)

```python
def dfs_permutaciones(lista):
    resultado = []
    dfs([], lista, resultado)
    return resultado


def dfs(actual, restantes, resultado):
    if len(restantes) == 0:
        resultado.append(actual[:])
        return

    for i in range(len(restantes)):
        siguiente = restantes[i]
        nuevo_resto = restantes[:i] + restantes[i + 1:]

        dfs(actual + [siguiente], nuevo_resto, resultado)

def dfs_combinaciones(lista, r):
    resultado=[]
    dfs([], 0, lista, r, resultado)
    return resultado

def dfs(actual, inicio, lista, r, resultado):
    if len(actual) == r:
        resultado.append(actual[:])
        return

    for i in range(inicio, len(lista)):
        dfs(actual + [lista[i]], i + 1, lista, r, resultado)

#Unicamente calcula los numeros enteros positivos
def factorial(n):
    if n <= 1:
        return 1
    return n * factorial(n - 1)

a=[1,2,3]
dfs_permutaciones(a)
dfs_combinaciones(a,2)
factorial(5)
```

