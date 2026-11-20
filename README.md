# Pobreza multidimensional en Colombia: factores asociados según la ECV 2023

Proyecto del curso **Aprendizaje Estadístico** (Escuela Colombiana de Ingeniería Julio Garavito). Identifica qué características del hogar y del territorio se asocian con la pobreza multidimensional de los hogares colombianos, a partir de los microdatos de la **Encuesta Nacional de Calidad de Vida (ECV) 2023** del DANE.

[![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/APES-M-JeissonS-JuanB/apes-pobreza-multidimensional-ecv2023/blob/main/notebooks/APES_Entrega_Final.ipynb)

## Objetivo

Identificar, a partir de los microdatos de la ECV 2023 del DANE, qué factores socioeconómicos (vivienda, educación, empleo y salud) están más asociados con la pobreza multidimensional de los hogares colombianos, mediante análisis estadístico descriptivo y multivariado.

## Datos

- **Fuente:** [Encuesta Nacional de Calidad de Vida (ECV) 2023, DANE](https://microdatos.dane.gov.co/index.php/catalog/827).
- **Tamaño:** 86.405 hogares y 240.212 personas, con representatividad nacional.
- **Variable objetivo:** `POBRE_IPM`. El IPM no viene calculado en los microdatos, así que se reconstruyó con la metodología del DANE: 15 indicadores en 5 dimensiones (educación, niñez y juventud, trabajo, salud, vivienda y servicios). **Validación contra el DANE 2023:** incidencia de 11,7% de las personas frente a 12,1% oficial, e intensidad de 40,2% frente a 40,1%. A nivel de hogares, la incidencia es 9,5%.

> Los archivos crudos (`data/raw/`) y procesados (`data/processed/`) no están en el repositorio por su tamaño; ver [Cómo ejecutar](#cómo-ejecutar).

## Estructura del repositorio

```
├── data/
│   ├── docs/         # Diccionario de variables y documentación técnica del DANE
│   ├── raw/          # CSV crudos de la ECV 2023 (no versionado)
│   └── processed/    # Bases limpias en .parquet (no versionado)
├── notebooks/
│   ├── APES_Entrega1.ipynb        # Primera entrega: integración, IPM y análisis descriptivo
│   └── APES_Entrega_Final.ipynb   # Entrega final: todo lo anterior + modelado (Sección 9) y conclusiones (Sección 10)
├── figures/          # Gráficos exportados
├── informe/          # Trabajo escrito, ficha resumen y tablas del modelo (informe/tablas/)
└── README.md
```

## Cómo ejecutar

1. Clona el repositorio.
2. Descarga los microdatos de la ECV 2023 desde el [catálogo del DANE](https://microdatos.dane.gov.co/index.php/catalog/827) y deja los CSV en `data/raw/csv/` con los nombres cortos que usa el notebook (`personas.csv`, `vivienda.csv`, `servicios.csv`, etc.).
3. Instala las dependencias: `pandas`, `numpy`, `scipy`, `statsmodels`, `scikit-learn`, `matplotlib`, `seaborn`, `openpyxl`, `pyarrow`.
4. Abre `notebooks/APES_Entrega_Final.ipynb` y ejecútalo de principio a fin.

Para correr solo el modelado (Sección 9), basta con `data/processed/ecv2023_hogares_limpia.parquet` y `data/raw/csv/diseno_muestra.csv`.

## Estructura del notebook final

| Sección | Contenido |
|---|---|
| 0–1 | Setup y carga de los 11 módulos de la ECV |
| 2 | Integración por llaves de hogar y persona (estructura oficial del DANE) |
| 3 | Limpieza y variables derivadas |
| 4 | Reconstrucción y validación de `POBRE_IPM` |
| 5–6 | Análisis univariado y multivariado (ponderado con `FEX_C`) |
| 7–8 | Hallazgos descriptivos y conclusiones de la primera etapa |
| 9 | Modelado con diseño muestral: logit con y sin ingreso, interacción zona × región, multinivel por departamento, logit fraccional, efectos marginales, robustez y evaluación fuera de muestra |
| 10 | Conclusiones finales |

## Resultados principales

**Análisis descriptivo**
1. Entre los hogares pobres, **educación (37,4%) y trabajo (32,4%)** concentran casi el 70% de la privación ponderada.
2. La incidencia varía mucho por **zona y región** (rural 22,4% frente a urbano 5,8% de los hogares; de 2,4% en Bogotá a 20,2% en Orinoquía-Amazonía) y poco por el **sexo del jefe** (10,2% frente a 9,0%).
3. La **informalidad** es la privación más frecuente (69,7%), pero distingue poco: afecta al 99,1% de los pobres y al 66,6% de los no pobres.

**Modelo** (logit con diseño muestral y solo variables externas al IPM; efectos marginales promedio, con todo lo demás constante)

| Factor | Efecto sobre la probabilidad de ser pobre |
|---|---|
| Ingreso per cápita, quintil 5 frente a quintil 1 | −14,0 pp |
| Estrato 6 frente a estrato 1 | −10,6 pp |
| Zona rural frente a urbana | +4,1 pp (de 1,1 a 8,6 pp según la región) |
| Orinoquía-Amazonía frente a región Central | +2,7 pp |
| Edad del jefe, +10 años | +2,5 pp |
| Tamaño del hogar, +1 persona | +1,4 pp |
| Jefatura femenina | +1,4 pp |

- Agregar el ingreso mejora poco la discriminación (AUC de 0,824 a 0,852) y reduce entre 28% y 38% los efectos de la zona, la jefatura femenina y el tamaño del hogar.
- Los resultados se mantienen con el puntaje continuo (`SCORE_IPM`), al excluir a los hogares sin estrato y en una muestra de prueba que el modelo no vio (AUC 0,852, bien calibrado).
- La categoría "sin estrato" se comporta como un indicador de falta de servicios públicos, que es parte del IPM, por lo que no se interpreta como factor.

Son asociaciones, no efectos causales.

## Autores

- Jeisson David Sánchez Gómez
- Juan Daniel Bogotá Fuentes

**Profesor:** Jairo Castrellón Torres

## Agradecimientos

Se utilizó el asistente de inteligencia artificial Claude (Anthropic) como apoyo en la redacción, la revisión del código y la interpretación de resultados.
