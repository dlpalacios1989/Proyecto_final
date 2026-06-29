# Proyecto_final

# Proyecto final QUBO–QAOA con dataset INEGI 4×4

## Resumen

Este repositorio contiene una versión adaptada del notebook original, sin depender de la estructura molecular/autograder, para una instancia educativa de matching bipartito 4×4 formulada como QUBO y resuelta con validación clásica exacta y QAOA local.

La instancia usa datos agregados de vivienda por entidad federativa, inspirados en el portal recomendado por la rúbrica: INEGI Banco de Indicadores / Indicadores por entidad federativa.


## Estructura

```text
proyecto_qubo_qaoa_inegi/
├── data/
│   └── dataset_real_4x4.csv
├── README.md
└── proyecto_qubo_qaoa.ipynb
```

## Dataset

- Fuente: INEGI, Banco de Indicadores / Indicadores por entidad federativa.
- Portal recomendado en la rúbrica: https://www.inegi.org.mx/servicios/api_indicadores.html
- Consulta: 2026-06-29.
- Granularidad: datos agregados por entidad federativa.
- Dominio: servicios básicos y conectividad en vivienda.

### Lado A

Las cuatro entidades seleccionadas son:

1. Chiapas
2. Guerrero
3. Oaxaca
4. Veracruz

La regla de selección fue usar entidades del sur-sureste mexicano donde las brechas de servicios y conectividad permiten construir una matriz de déficits interpretable.

### Lado B

Las cuatro líneas de atención agregadas son:

1. Priorizar agua entubada
2. Priorizar drenaje
3. Priorizar conectividad a Internet
4. Priorizar equipamiento digital

## Variable binaria

La variable binaria se define como:

```text
x_ij = 1 si la entidad a_i se empareja con la línea de atención b_j
x_ij = 0 en caso contrario
```

## Score

El archivo `data/dataset_real_4x4.csv` incluye una cobertura porcentual agregada para cada par entidad–línea de atención.

El score se calcula como:

```text
S_ij = 100 - cobertura_pct_ij
```

Por lo tanto, un valor mayor de `S_ij` representa mayor déficit relativo y mayor prioridad educativa dentro del modelo.

## Matriz S

| Entidad | Agua | Drenaje | Internet | Computadora |
|---|---:|---:|---:|---:|
| Chiapas | 26.6 | 10.4 | 78.3 | 81.5 |
| Guerrero | 25.2 | 11.5 | 65.3 | 77.3 |
| Oaxaca | 24.4 | 19.2 | 70.6 | 79.4 |
| Veracruz | 17.5 | 8.2 | 54.8 | 72.2 |

## Formulación QUBO

El objetivo original es maximizar:

```text
sum_ij S_ij x_ij
```

con restricciones uno-a-uno:

```text
sum_j x_ij = 1  para cada entidad
sum_i x_ij = 1  para cada línea de atención
```

El QUBO minimiza:

```text
E(x) = -sum_ij S_ij x_ij
       + lambda_A sum_i (sum_j x_ij - 1)^2
       + lambda_B sum_j (sum_i x_ij - 1)^2
```

La penalización se calcula automáticamente en el notebook con base en la magnitud de los scores.

## Resultado clásico esperado

La validación por permutaciones factibles encuentra como mejor asignación:

| Entidad | Línea asignada |
|---|---|
| Chiapas | Priorizar conectividad a Internet |
| Guerrero | Priorizar agua entubada |
| Oaxaca | Priorizar drenaje |
| Veracruz | Priorizar equipamiento digital |

Score total clásico:

```text
194.9
```

## QAOA local

El notebook ejecuta una simulación vectorial local con 16 variables binarias, optimización COBYLA y muestreo local. La comparación final se reporta automáticamente en las secciones de métricas y gráficas.

## Riesgos éticos y sesgos

- No se usan datos personales.
- No se usan datos de pacientes, hogares individuales ni identificadores sensibles.
- Los datos están agregados por entidad federativa.
- El modelo fuerza una asignación uno-a-uno, lo cual simplifica excesivamente una decisión pública real.
- Las coberturas agregadas pueden ocultar desigualdades internas municipales o rurales/urbanas.
- El resultado solo es educativo y no debe usarse para asignar recursos reales.

## Cómo ejecutar

1. Abrir `proyecto_qubo_qaoa.ipynb` en Google Colab.
2. Verificar que `data/dataset_real_4x4.csv` esté en la ruta indicada.
3. Ejecutar `Runtime → Run all`.
4. Revisar las secciones de validación clásica, QAOA local y comparación final.



