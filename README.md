# EDA: Salud mental en el sector tecnológico

Análisis exploratorio de datos (EDA) de la encuesta **OSMI «Mental Health in Tech Survey»** (2014-2016): 1259 respuestas de personas que trabajan en tecnología sobre su salud mental y lo que ofrece su empresa. La variable central es `treatment`: si la persona ha buscado tratamiento.

Proyecto del bootcamp Factoría F5 (IA, Barcelona). El enunciado está en [`BRIEFING.md`](BRIEFING.md).

## Qué encontramos

- Los **antecedentes familiares** son lo que más se relaciona con buscar tratamiento (74 % frente a 36 %).
- **Más apoyo de la empresa**, más gente busca tratamiento (del 37 % con ningún apoyo al 70 % con 3).
- Donde se **protege el anonimato** hay menos miedo a hablar de salud mental (15 % frente a 65 %).

Son relaciones, no causas. El detalle, con gráficos, está en el [informe ejecutivo](report/INFORME.md).

## De dónde salen los datos

- **Fuente:** encuesta de [OSMI (Open Sourcing Mental Illness)](https://osmihelp.org/research), publicada en Kaggle como [Mental Health in Tech Survey](https://www.kaggle.com/datasets/osmi/mental-health-in-tech-survey).
- **Archivo:** `data/survey.csv` (1259 filas, 27 columnas). Es el archivo original: no se modifica.
- **Datos limpios:** `data/survey_clean.csv` lo genera el notebook (sección 6.6) a partir del original.

## Cómo instalar las librerías

Necesitas Python 3 (usamos 3.13) y Git.

```bash
git clone https://github.com/IA-P1-BCN/ai_project_EDA-charlotte_doulcet.git
cd ai_project_EDA-charlotte_doulcet

python3 -m venv .venv
source .venv/bin/activate          # en Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Las librerías principales son `pandas`, `numpy`, `matplotlib`, `seaborn` y `ipykernel` (para el notebook).

## Cómo ejecutar el notebook

1. Abre la carpeta del proyecto en **VS Code** (con las extensiones Python y Jupyter).
2. Abre `eda.ipynb` y elige como kernel el entorno `.venv`.
3. Ejecuta todas las celdas de arriba abajo (**Run All**).

El notebook lee `data/survey.csv` con una ruta relativa, así que hay que abrirlo desde la raíz del proyecto. Al llegar a la sección 6.6 vuelve a escribir `data/survey_clean.csv`.

## Estructura del repositorio

```
├── eda.ipynb            Análisis completo, en español, paso a paso
├── data/
│   ├── survey.csv           Datos originales (no se modifican)
│   └── survey_clean.csv     Datos limpios (los genera el notebook)
├── report/
│   ├── INFORME.md           Informe ejecutivo para una persona no técnica
│   └── figures/             Gráficos del informe
├── BRIEFING.md          Enunciado del proyecto
├── BITACORA.md          Diario de decisiones (en inglés)
└── requirements.txt     Librerías y versiones
```

## Qué hay en el notebook

1. Introducción y 3 preguntas de partida (quién busca tratamiento, si importa lo que ofrece la empresa, quién teme hablar).
2. Carga y primer vistazo a los datos.
3. Calidad de los datos: valores vacíos, duplicados, edades imposibles y `Gender` escrito de 49 formas.
4. Limpieza y creación de variables.
5. Análisis: univariado, bivariado, valores atípicos, agrupaciones, ingeniería de variables, matriz de correlación y comparación por país.
6. Respuesta a las preguntas, conclusiones y límites.

## Tareas y organización

Las tareas están en el proyecto de GitHub IA-P1-BCN (una issue por rama, PR hacia `dev`). Las decisiones y sus motivos están en [`BITACORA.md`](BITACORA.md).
