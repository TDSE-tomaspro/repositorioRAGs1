# Tutorial de LangChain: Creando Cadenas con LLMs

Una implementación para principiantes del [Tutorial de Cadenas con LLMs de LangChain](https://python.langchain.com/docs/tutorials/llm_chain/), que demuestra cómo construir una aplicación conversacional simple usando LangChain y la API de Groq.

---

## Tabla de Contenidos

- [Arquitectura del Proyecto](#arquitectura-del-proyecto)
- [Componentes](#componentes)
- [Prerrequisitos](#prerrequisitos)
- [Instalación](#instalación)
- [Configuración](#configuración)
- [Ejecución del Código](#ejecución-del-código)
- [Resumen del Código](#resumen-del-código)
- [Ejemplo de Salida](#ejemplo-de-salida)
- [Referencias](#referencias)

---

## Arquitectura del Proyecto

```
┌─────────────────────────────────────────────────┐
│                  Entrada del Usuario            │
│             (pregunta / prompt)                 │
└───────────────────────┬─────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────┐
│           ChatPromptTemplate                    │
│  Formatea la pregunta en un prompt estructurado│
│  con un mensaje de sistema y un mensaje humano  │
└───────────────────────┬─────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────┐
│               ChatGroq (LLM)                    │
│  Envía el prompt formateado a la API de Groq    │
│  y devuelve un objeto AIMessage                 │
└───────────────────────┬─────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────┐
│            StrOutputParser                      │
│  Extrae la respuesta en texto plano del         │
│  AIMessage devuelto por el modelo               │
└───────────────────────┬─────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────┐
│                  Salida                         │
│            (respuesta en texto plano)           │
└─────────────────────────────────────────────────┘
```

Los tres componentes están conectados usando el **Lenguaje de Expresión de LangChain (LCEL)** con el operador de tubería (`|`):

```python
chain = prompt | llm | output_parser
```

---

## Componentes

| Componente | Descripción |
|-----------|-------------|
| `ChatPromptTemplate` | Define la estructura del prompt enviado al modelo. Combina un mensaje de *sistema* (establece el rol del asistente) con un mensaje *humano* (la pregunta del usuario). |
| `ChatGroq` | Un envoltorio (wrapper) para la API de Chat de Groq. Acepta el prompt formateado y devuelve una respuesta del modelo. |
| `StrOutputParser` | Convierte el objeto `AIMessage` devuelto por el LLM en una cadena de texto simple de Python. |

---

## Prerrequisitos

- Python **3.9** o superior
- Una **Clave de API de Groq** ([puedes crear una aquí](https://console.groq.com/keys))

---

## Instalación

1.  **Clona el repositorio**

    ```bash
    git clone https://github.com/TDSE-tomaspro/ntroduction_to_Creating_RAGs_1.git
    cd ntroduction_to_Creating_RAGs_1
    ```

2.  **Crea un entorno virtual** (recomendado)

    ```bash
    python -m venv .venv
    source .venv/bin/activate
    ```

3.  **Instala las dependencias**

    ```bash
    pip install -r requirements.txt
    ```

---

## Configuración

Para este tutorial, la forma más segura y recomendada de gestionar tu clave de API de Groq es mediante un archivo `.env`.

### 1. Crear el archivo .env

En la raíz del proyecto, crea un archivo llamado `.env` y añade tu clave:

```text
GROQ_API_KEY=tu_clave_de_groq_aqui
```

*Nota: El archivo `.env` ya está incluido en `.gitignore`, por lo que nunca se subirá a GitHub.*

### 2. Instalación de dependencias adicionales

Este método requiere la librería `python-dotenv`:

```bash
pip install python-dotenv
```

---

## Ejecución del Código

1.  Abre el archivo `tutorial.ipynb` en un editor compatible con Jupyter Notebooks (como VS Code).
2.  Asegúrate de haber configurado tu `GROQ_API_KEY` como se describe arriba.
3.  Ejecuta cada celda del cuaderno en orden, de arriba hacia abajo.

La última celda construirá la cadena y la ejecutará con tres preguntas de ejemplo en español, imprimiendo las respuestas directamente en la salida de la celda.

---

## Resumen del Código

### `tutorial.ipynb`

```python
# 1. Plantilla de Prompt — estructura la conversación
prompt = ChatPromptTemplate.from_messages([
    ("system", "Eres un asistente servicial. Responde a la pregunta del usuario de forma clara y concisa."),
    ("human", "{question}"),
])

# 2. Modelo de Lenguaje — llama a la API de Groq
llm = ChatGroq(model="llama-3.1-8b-instant", temperature=0.7)

# 3. Parser de Salida — extrae el texto de la respuesta
output_parser = StrOutputParser()

# 4. Cadena — conecta los tres componentes usando LCEL
chain = prompt | llm | output_parser

# 5. Invocación — ejecuta la cadena con una pregunta
response = chain.invoke({"question": "¿Qué es LangChain?"})
```

**Funciones clave:**

| Función | Descripción |
|----------|-------------|
| `build_chain(model_name, temperature)` | Construye y devuelve la cadena del LLM. |
| `ask(question, chain)` | Invoca la cadena con una pregunta y devuelve la respuesta. |

---

## Ejemplo de Salida

```
Pregunta: ¿Qué es LangChain y para qué se utiliza?
Respuesta:   LangChain es un marco de código abierto para construir aplicaciones impulsadas por modelos de lenguaje grandes (LLM). Proporciona herramientas y abstracciones para crear aplicaciones que pueden razonar, actuar y personalizarse con datos.

Se utiliza para:
* **Crear chatbots y asistentes virtuales:** LangChain facilita la creación de interfaces conversacionales que pueden interactuar con los usuarios de forma natural.
* **Resumir texto:** Puede resumir documentos largos en resúmenes concisos.
* **Responder preguntas:** Puede responder preguntas sobre un conjunto de datos o un dominio de conocimiento específico.
* **Generar texto:** Puede generar texto creativo, como poemas, guiones o correos electrónicos.
------------------------------------------------------------
Pregunta: Explica el concepto de plantillas de prompt en términos sencillos.
Respuesta:   Imagina que tienes un formulario con espacios en blanco. Una plantilla de prompt es como ese formulario, pero para un modelo de lenguaje.

En lugar de escribir la misma instrucción una y otra vez, creas una plantilla con "marcadores de posición" (variables). Luego, llenas esos marcadores de posición con información específica cada vez que usas la plantilla.

Por ejemplo, una plantilla podría ser: "Traduce la siguiente frase al {idioma}: {frase}".

Aquí, `{idioma}` y `{frase}` son los marcadores de posición. Puedes reutilizar esta plantilla para traducir cualquier frase a cualquier idioma, simplemente cambiando los valores de los marcadores de posición.

En resumen, las plantillas de prompt son una forma de crear instrucciones reutilizables y dinámicas para los modelos de lenguaje.
------------------------------------------------------------
...
```

---

## Referencias

- [LangChain LLM Chain Tutorial](https://python.langchain.com/docs/tutorials/llm_chain/)
- [Documentación de LangChain](https://python.langchain.com/docs/introduction/)
- [LangChain Expression Language (LCEL)](https://python.langchain.com/docs/concepts/lcel/)
- [Documentación de la API de Groq](https://console.groq.com/docs)
