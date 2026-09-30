# Demostración y Solución del Problema de Mínimos Cuadrados Ordinarios (MCO)

*Universidad Militar Nueva Granada — Matemáticas Aplicadas y Computacionales*

---

## 1. Planteamiento del Problema

Dado un conjunto de $m$ observaciones empíricas y $n$ variables explicativas (con $m > n$), el modelo lineal se formula matricialmente como el sistema **sobredeterminado**:

$$
A x = b \quad (1)
$$

donde:

- $A \in \mathbb{R}^{m \times n}$ representa la matriz de diseño.
- $x \in \mathbb{R}^{n}$ es el vector de parámetros a estimar.
- $b \in \mathbb{R}^{m}$ es el vector de observaciones de la variable de respuesta.

Ante la imposibilidad de hallar una solución exacta (ya que $b \notin C(A)$, el espacio columna de $A$), el método de mínimos cuadrados busca **proyectar ortogonalmente** $b$ sobre $C(A)$. Se define la estimación del vector observacional como $\hat{b} = A\hat{x} \in C(A)$, reduciendo así la distancia entre el valor real y el subespacio proyectado.

---

## 2. Estructura Formal y Solución Analítica

### Definición 1 (Vector de Error)

El vector de residuos $e \in \mathbb{R}^{m}$ correspondiente al error de ajuste se define formalmente como:

$$
e = b - \hat{b} = b - A\hat{x} \quad (2)
$$

### Axioma 1 (Ecuaciones Normales)

Para que el residuo $e$ sea ortogonal a la imagen de $A$, el estimador óptimo debe satisfacer de manera fundamental las **ecuaciones normales**:

$$
A^{T} A \hat{x} = A^{T} b \quad (3)
$$

### Proposición 1 (Deducción del Estimador Óptimo)

Bajo la condición de que la matriz de diseño tenga rango columna completo, es decir, $\text{rango}(A) = n$, la matriz **Gramiana** $(A^{T} A) \in \mathbb{R}^{n \times n}$ es estrictamente invertible.

**Desarrollo analítico.** Multiplicando ambos lados de las ecuaciones normales por la inversa $(A^{T} A)^{-1}$, aislamos directamente el vector de parámetros:

$$
\begin{aligned}
(A^{T} A)^{-1} (A^{T} A) \hat{x} &= (A^{T} A)^{-1} A^{T} b \quad &(4) \\
I_n \, \hat{x} &= (A^{T} A)^{-1} A^{T} b \quad &(5)
\end{aligned}
$$

Obteniendo el **estimador analítico general de mínimos cuadrados**:

$$
\boxed{\hat{x} = (A^{T} A)^{-1} A^{T} b} \quad (6)
$$

---

## 3. Aplicación: Caso de Regresión Lineal Simple

Para el modelo de una recta con ecuación $y = mx + b$, adaptamos los coeficientes ordenando respecto a la pendiente ($m$) y el intercepto ($b$):

$$
y_i = m \cdot x_i + b \cdot 1 \quad (7)
$$

Definimos la estructuración matricial del modelo:

**Vector de coeficientes desconocidos:**

$$
\hat{x} = \begin{bmatrix} m \\ b \end{bmatrix}
$$

**Matriz de diseño para $N$ observaciones:**

$$
A = \begin{bmatrix} x_1 & 1 \\ x_2 & 1 \\ \vdots & \vdots \\ x_N & 1 \end{bmatrix} \quad (8)
$$

$$
A^{T} A = \begin{bmatrix} x_1 & x_2 & \cdots & x_N \\ 1 & 1 & \cdots & 1 \end{bmatrix} \begin{bmatrix} x_1 & 1 \\ x_2 & 1 \\ \vdots & \vdots \\ x_N & 1 \end{bmatrix} = \begin{bmatrix} \sum x_i^{2} & \sum x_i \\ \sum x_i & N \end{bmatrix} \quad (9)
$$

El producto matricial fundamental $A^{T} A$ resulta en una matriz **simétrica de dimensiones $2 \times 2$**. Sustituyendo en la expresión óptima, los parámetros se obtienen evaluando:

$$
\begin{bmatrix} m \\ b \end{bmatrix} = \left( \begin{bmatrix} \sum x_i^{2} & \sum x_i \\ \sum x_i & N \end{bmatrix} \right)^{-1} A^{T} y \quad (10)
$$

---

## 4. Equivalencia Computacional en Python

La fundamentación algebraica anterior es exactamente la que ejecuta el entorno numérico de **NumPy**.

### 4.1. Construcción de la Matriz de Diseño

```python
A_lin = np.vstack([x, np.ones(len(x))]).T
```

- `np.ones(len(x))`: crea el vector de unos para asociarlo al intercepto $b$.
- `np.vstack([...])`: une el vector $x$ y el de unos en dos filas ($2 \times N$).
- `.T`: transpone la matriz resultante a dimensión $N \times 2$, correspondiente a $A$.

### 4.2. Resolución del Sistema

```python
m_lin, c_lin = np.linalg.lstsq(A_lin, y, rcond=None)[0]
```

La función `np.linalg.lstsq` resuelve internamente las ecuaciones normales mediante una **descomposición de rango completo** para hallar $\hat{x} = (A^{T} A)^{-1} A^{T} y$.

### 4.3. Proyección Ortogonal de la Respuesta

```python
ajuste_lin = m_lin * x + c_lin
```

En álgebra lineal, este paso equivale formalmente a computar la proyección de estimación:

$$
\hat{y} = A \hat{x} \quad (11)
$$

obteniendo el **vector de respuesta proyectado** sobre el subespacio del modelo.
