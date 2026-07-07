# IA
Es la ciencia e ingenio de hacer máquinas inteligentes, especialmente programas de cómputo inteligentes.

Búsqueda del estado requerido en el conjunto de los estados producidos por las acciones posibles.

# Problema del campesino, el coyote, el pollo y el maíz

## Arbol de movimientos
```mermaid
flowchart TD
	A[A C P M]
```

```mermaid
flowchart TD
	A1[I I I I] --> B1[D D I I] --> X1[X]
	A1 --> B2[D I D I] --> C1[I I D I] --> D1[D D  D I]
	C1 --> D2[D I D D]
	D1 --> E2[I D I I]
	D2 --> E1[I I I D]
	E1 --> F1[D D I D]
	F1 --> G3[I D I D]
	G3 --> H1[D D D D]
	E2 --> F2[D D D I] --> G1[I D D I] --> X3[X]
	E2 --> F3[D D I D] 
	F3 --> G2[I D I D]
	G2 --> H2[D D D D]
	A1 --> B3[D I I D] --> X2[X]
	
	
```

## Tabla de movimientos
| Paso |  Orilla izquierda | Acción | Orilla derecha |
|---|---|---|---|
| 1 |  coyote, maíz | Lleva al pollo | campesino, pollo |
| 2 |  campesino, coyote, maíz | Regresa solo | pollo |
| 3 |  maíz | Lleva al coyote | campesino, coyote, pollo |
| 4 | campesino, pollo, maíz | Regresa con el pollo | coyote | 
| 5 |  pollo | Lleva el maíz | campesino, coyote, maíz |
| 6 |  campesino, pollo | Regresa solo | coyote, maíz |
| 7 |   | Lleva al pollo | campesino, coyote, pollo, maíz |

La solución correcta requiere **7 movimientos** y garantiza que todos crucen el río sin problemas.

---

# Problema de los tres caníbales y tres monjes

## Arbol de movimientos
```mermaid
flowchart TD
	A[Inicio] --> B[Cruzan 2 canibales]
	B --> C[Regresa 1 canibal]
	C --> D[Cruzan 2 canibales]
	D --> E[Regresa 1 canibal]
	E --> F[Cruzan 2 monjes]
	F --> G[Regresa 1 monje y 1 canibal]
	G --> H[Cruzan 2 monjes]
	H --> I[Regresa 1 canibal]
	I --> J[Cruzan 2 canibales]
	J --> K[Regresa 1 canibal]
	K --> L[Cruzan 2 canibales]
	L --> M[Fin]
```

## Tabla de movimientos
| Paso | Orilla izquierda | Acción | Orilla derecha |
|---|---|---|---|
| 1 |  3 monjes, 1 caníbal | Cruzan 2 caníbales | 2 caníbales |
| 2 |  3 monjes, 2 caníbales | Regresa 1 caníbal | 1 caníbal |
| 3 |  3 monjes | Cruzan 2 caníbales | 3 caníbales |
| 4 |  3 monjes, 1 caníbal | Regresa 1 caníbal | 2 caníbales |
| 5 |  1 monje, 1 caníbal | Cruzan 2 monjes | 2 monjes, 2 caníbales |
| 6 |  2 monjes, 2 caníbales | Regresa 1 monje y 1 caníbal | 1 monje, 1 caníbal |
| 7 |  2 caníbales | Cruzan 2 monjes | 3 monjes, 1 caníbal |
| 8 |  3 caníbales | Regresa 1 caníbal | 3 monjes |
| 9 | 1 caníbal | Cruzan 2 caníbales | 3 monjes, 2 caníbales |
| 10 |  2 caníbales | Regresa 1 caníbal | 3 monjes, 1 caníbal |
| 11 |   | Cruzan 2 caníbales | 3 monjes, 3 caníbales |

La solución correcta requiere **11 movimientos**.




