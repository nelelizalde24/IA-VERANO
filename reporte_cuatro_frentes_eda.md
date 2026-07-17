# Reporte EDA — Operación Cuatro Frentes
**Analista:** [Nelson Jesus Elizalde / 21120199]

## Misión 1 — Semáforo Académico
- **Pregunta de negocio**: Podemos identificar al cierre del parcial si un alumno esta en **riesgo bajo, medio o alto de reprobar** para activar interveciones tempranas?
- **Tipo propuesto:** Clasificacion multiclase — **Justificación:** La vairable objetivo `riesgo` toma valores categoricos (verde, amarillo, rojo), no un valor numerico continuo. Por lo tanto, el objetivo es asignar cada alumno a una clase entre tres posibles.
- **Define Y:** `riesgo` (verde/amarrillo/rojo)
- **Variable X :** 
    - `asistencia_pct` : menor asistencia suele asociarse con mayor riesgo academico.
    - `tareas_entregadas` : refleja cumplimiento y constacia; menos teras suele indicar mayor probalilidad de bajo desempeño.
    - `promedio_parciales` : es una medida directa de rendimiento durante el periodo.
    - `horas_plataforma` : aproxima nivle de estudios/participacion en LMS.
    - `reprobadas_previas` : historial academico: mas materias reprobadas puede aumentar riesgo fueturo.
    - `turno` : posible efecto contextual (rutina, disponibilidad de tiempo).
- **EDA - patrones:** 
    1. Gradiente claro por riesgo en desempeño y asistencia
        - Verde: asistencia 91, promedio 8.4, reprobadas previas 0.2
        - Amarilla: asistencia 76, promedio 6.8, reprobadas 0.9
        - Roja; asistemcia 52, promedio 5.0, reporbadas 2.4
    2. Patrones coherentes en la muestra de 12 filas.

       Casos rojos concentrean baja asistencia, pocas tareas y mas reprobadas.

       Casos verdes muestran lo contrario.

- **Distribucion de Y:** 

    Distrubucion (N=300):
    - Verde: 120 (40%)
    - Amarillo: 105 (35%)
    - Rojo: 75 (25%) 
    
    Interpretacion:
    - No esta extremadamente desbalaciado, pero si hay clase minoritaria (`rojo`).
    - No conviene usar solo accuracy, porque podira ocultar bajo desempeño en `rojo`.

    Metricas recomendadas:
    - F1 macro (trata todas las clases por igual).
    - Recall por clase, especialmente recall de `rojo` (importante no dejar pasar alumnos en alto riesgo).
    - Matriz de confusion para ver errores entre clases vecinas (amarillo ↔ rojo).

- **Calidad de datos:**
    Problema realista propuesto: faltantes en `horas_plataforma` por fallas de registro del LMS.

    Como detectarlo;
    - Conteo de nulos por columna (`isna().sum()`).
    - % de faltantes por variable.
    - Verificar si faltantes se concentran por `urno` o por grupos de riego (sesgo de medicion).

- **Fuga de infromacion:**
    
    ¿Usarias calificacion final del curso como X?
    No.Seria fuga de informacion si el semaforo se calcula al cierre del parcial, por que la calificacion final ocurre despues y contiene informacion futura del resultado que queremos anticipar.Eso inflaria artificialmete el rendimiento del modelo.

- **Propuesta post-EDA:**

    - Modelo razonable: Random Forest Classifier (multiclase).
    - Condicion del dataset que debe cumplirse: que las etiquetas de `riesgo` esten bien definidas y consistentes (misma regla para todos los alumnos), y que no haya variables con fuga temporal.

## Misión 2 — Alerta de Churn
- **Pregunta de negocio:**
    ¿Podemos dectectar a mitad de semestre que alumno tiene alta probabilidad de abandonar la materia para intervenir a tiempo?

- **Tipo de problema propuesto:** Clasisficacion binaria — **Justificación:** La variable objetivo `abandona` toma valores 0/1 (permanece/abandona), por lo que es una categorica binaria, no una variable numerica continua.

- **Calculo de casos positivos**
    Con (N=500) y (14%) de `abandona=1`

            500 * 0.4 = 70

    Casos positivos = 70 (abandona=1).

    Casos negativos = 430 (abandona=0).

    Si alguien reporta "**86% de aciertos**" sin contexto, puede ser engañoso:un modelo trivial que siempre prediga "0 = no abandona" ya lograria 86% accuracy (430/500), pero no detectaria ningun abandono real (recall de la clase 1= 0).

- **Valores faltantes:**
    Hipotisis: los NA no son aleatorios; podria significar que el alumno no entrego actividad, y eso se asocia con abandono (la pista dice reacion fuerte).

    Tratamineto recomendado:
    1.  Crear bandera `tiene_calif` (1 si hay nota, 0 si NA).
    2. Imputar `calif_actividad_1 (mediana) para no perder filas.
    3. Mantener ambas variables en el modelo para capturar "valor" + "ausencia informativa".

- **Leakage:**
    **¿Incluir "fecha de baja definitiva" o " nota final" como X?**
    No,Ambas contienen informacion porterior al momento de prediccion (mitad de semestre) y revelan directa o indirectamente el resultado.Eso produce fuga y metricas artificialmente altas.

- **EDA:**

    **a) ¿Como esta distribuida Y?**
    `abadona=0` 86% vs `abandona=1` 14%
    Hay desbalance importante: la clase positiva (abandono) es minoritaria.

    **b) ¿Hay outliers en `dias_sin_login`?**
    En la muestra hay valores altos (21, 25, 30) frente a otros bajos (1-6).
    No necesariamente son errores: puede ser casos reales de desconexion fuerte. Se deben revisar con boxplot/IQR y validar reglas de negocio (ej. maximos posibles por fecha de corte).

    **c) ¿Los casos `abandona=1` se concetran en alumnos que trabajan?**
    En la muestra: 
    - `abandono=1`: 5 casos (203,204,206,208,210)
    - De esos, `trabaja=1`:4/5 (80%)
    Se observan concentracion en quienes trabajan en esta muestra; coherente con la pista "debil-moderada" en dataset completo (confirmar con tabla cruzada en N=500).

- **Comparacion con Mision 1:**
    **Similitudes:**
    - En ambas, Y es categoria ⇒ problema de clasificacion.
    - En ambas hay riesgo de leakage temporal.
    - En ambas EDA debe revisar distribucion de clases y calidad de datos.

    **Diferencia:**
    - Mision 1: clasificar milticlase (`verde/amarillo/rojo`),desbalance moderado.
    - Mision 2: clasificacion binaria (`0/1`), desbalance mas fuerte (14% posistivos), por lo que el foco en recall/precision de la clase positiva es mas critico.

- **Propuesta post-EDA (modelo + metrica):**

    **Modelo razonable:** Regresion Logistica (baseline) o Random Forest con pesos de clase.

    **Metrica coherentes:**
    - **Recall de clase 1 (abandona):** prioritario para no dejar casos de riesgo sin detectar.
    - **Precision clase 1:** para no saturar intervencion con falsos positivos.
    - **F1 de clase 1 y PR-AUC** (util en desbalance).
    - Matriz de confucion

    **Costo de equivcarse:**
    - **Falso negativo** (predecir que no abandona cuando si abandona) suele ser mas costoso academicamente, porque se pierde oportunidad de intervencion
    - Por esos conviene optimizar recall de la clase positiva, manteniendo una precision razonable.

## Misión 3 — Pronóstico de Puntaje
- Pregunta de negocio:
- **Tipo propuesto:** [clasificar / predecir] — justificación:
- Qué se gana/pierde si se convierte a aprobado/reprobado:
- Outliers / errores de captura:
- Métricas (2):
- Modelo propuesto:

## Misión 4 — Tiempo de Estudio
- Pregunta de negocio:
- **Tipo propuesto:** [clasificar / predecir] — justificación:
- Hipótesis dificultad → horas:
- Cola larga: ¿borrar o conservar?
- Alternativa de binarizar Y:
- Métricas y modelo + 2 chequeos EDA:

## Síntesis (máx. 8 líneas)
1. Tabla resumen de *mis* cuatro propuestas de tipo (M1–M4).
2. Una pista que usé para decidir “clase vs número” en cualquier misión.
3. Frase final: “El tipo de problema se deduce de la pregunta y de Y porque…”