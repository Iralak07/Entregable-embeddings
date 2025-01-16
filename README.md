# Entregable-embeddings

Instalación, configuración, crear y add embedings, realizar consultas y usar condiciones y filtros avanzados en las consultas

Crea un notebook en python que permita realizar las operaciones de un CRUD, el readme debe contener
1. Explicar cada uno de los métodos de consulta en el CRUD
2. Explicar como configurar la  base de datos vectorial
3. Explicar el funcionamiento de los embedding y si configuracion
4. Debe contener imagenes de uso y ejemplos

# Instalacion y configuracion

    !pip install chromadb
    !pip install langchain pypdf
    !pip install -U langchain-community
    !pip install gradio

Antes que nada tendremos que instalar las herramientas necesarias para poder realizar el siguiente proyecto:

1. ChromaDB

    Instalación: !pip install chromadb
    Descripción:
    ChromaDB es una base de datos vectorial diseñada para trabajar con datos semánticos representados como embeddings. Su principal objetivo es facilitar la indexación, búsqueda y gestión de datos en espacios vectoriales.
        Permite almacenar, consultar y buscar documentos basados en similitudes semánticas.
        Ofrece integración con funciones de embeddings personalizadas y modelos predefinidos, como SentenceTransformers.
        Ideal para casos de uso como chatbots, motores de búsqueda semántica y sistemas de recomendación.

2. LangChain

    Instalación: !pip install langchain
    Descripción:
    LangChain es una biblioteca diseñada para facilitar la creación de aplicaciones basadas en inteligencia artificial utilizando modelos de lenguaje grande (LLMs).
        Proporciona herramientas para gestionar y conectar LLMs con fuentes externas de datos, como bases de datos vectoriales.
        Permite realizar consultas avanzadas utilizando embeddings, integrándolos con flujos de trabajo personalizados.
        Compatible con modelos de IA populares como OpenAI GPT y transformadores basados en Hugging Face.

3. PyPDF

    Instalación: !pip install pypdf
    Descripción:
    PyPDF es una biblioteca ligera para trabajar con documentos PDF. Es útil para extraer texto y contenido de archivos PDF que pueden ser utilizados como entradas para generar embeddings.
        Permite leer, dividir, y procesar documentos PDF de manera eficiente.
        Muy útil en casos donde necesitas transformar documentos no estructurados en datos para consulta semántica.

4. LangChain-Community

    Instalación: !pip install -U langchain-community
    Descripción:
    Una extensión de LangChain que proporciona funcionalidades avanzadas y módulos desarrollados por la comunidad para mejorar la integración de LLMs con otras herramientas.
        Ofrece compatibilidad ampliada para trabajar con bases de datos vectoriales y diferentes fuentes de datos.
        Facilita la creación de aplicaciones más robustas y adaptables para casos específicos de uso.

5. Gradio

    Instalación: !pip install gradio
    Descripción:
    Gradio es una biblioteca que permite crear interfaces web interactivas de manera sencilla. Es ideal para probar y compartir proyectos relacionados con          inteligencia artificial.
        Permite crear aplicaciones con interfaces gráficas sin necesidad de conocimientos avanzados de diseño web.
        Se utiliza para realizar consultas, visualizar resultados y trabajar de forma interactiva con embeddings y bases de datos vectoriales.
        Perfecto para prototipos y demostraciones rápidas de modelos y flujos de trabajo.



## Importación de ChromaDB y Configuración del Cliente Persistente

    import chromadb
    client = chromadb.PersistentClient()
    
Al utilizar PersistentClient, estamos configurando ChromaDB para que almacene los datos en el disco, garantizando que los datos persistan entre sesiones. Esto es útil para manejar grandes cantidades de información y mantener el acceso continuo a la base de datos.

## Importamos las herramientas necesarias que utilizaremos

    from langchain.document_loaders import PyPDFLoader
    from langchain.text_splitter import CharacterTextSplitter
    from langchain.embeddings import HuggingFaceEmbeddings
    from langchain.vectorstores import Chroma

PyPDFLoader (de langchain.document_loaders)

  - Permite cargar documentos PDF y extraer su contenido como texto estructurado.
  - Es útil para procesar documentos grandes y convertirlos en datos que pueden ser utilizados para generar embeddings.

CharacterTextSplitter (de langchain.text_splitter)

  - Divide el texto en fragmentos más pequeños basados en caracteres.
  - Esto facilita el procesamiento de grandes volúmenes de texto y permite crear embeddings para fragmentos individuales.

HuggingFaceEmbeddings (de langchain.embeddings)

  - Utiliza modelos preentrenados de Hugging Face para generar embeddings de texto.
  - Convierte texto en representaciones vectoriales, capturando su significado semántico.

Chroma (de langchain.vectorstores)

  - Actúa como una base de datos vectorial para almacenar y gestionar los embeddings generados.
  - Permite realizar consultas basadas en similitud semántica entre vectores, útil para búsquedas avanzadas.

## Montar Google Drive en Google Colab

Este comando conecta una cuenta de Google Drive al entorno de Google Colab. Permite acceder a los archivos y carpetas de Google Drive directamente desde el entorno de Colab. Los archivos de Google Drive estarán disponibles bajo la ruta /content/drive dentro del entorno de Colab.

## Carga y División de Documentos PDF para Procesamiento

En este proceso, estamos utilizando herramientas de LangChain para cargar un archivo PDF y dividirlo en fragmentos manejables. Este procedimiento es clave para trabajar con documentos extensos y prepararlos para su análisis o conversión en embeddings para bases de datos vectoriales.

    loader = PyPDFLoader("/content/drive/MyDrive/sucesiones.pdf")
    pages = loader.load_and_split()


## División de Documentos en Fragmentos de Texto

    split = CharacterTextSplitter(chunk_size=240, separator = '.\n')
    textos = split.split_documents(pages) # Lista de textos

En este fragmento de código, estamos utilizando un objeto de LangChain llamado CharacterTextSplitter para dividir el contenido de un documento en fragmentos más pequeños según un tamaño definido y un separador específico.
Entrada: pages, que es una lista de páginas o fragmentos de texto extraídos previamente del documento PDF.
Qué hace: Aplica el objeto split a cada página o fragmento de pages para dividirlo en secciones más pequeñas.
Resultado: Devuelve una lista de fragmentos de texto (textos), cada uno con un tamaño cercano al máximo definido (chunk_size=240) y dividido en puntos y saltos de línea.

## Creación de una Función de Incrustación con HuggingFace

    embedding_function = HuggingFaceEmbeddings(model_name="all-MiniLM-L6-v2")

En esta línea, estamos creando un objeto de tipo HuggingFaceEmbeddings que servirá como función de incrustación (embedding function). Este objeto convierte textos en vectores numéricos utilizando un modelo de lenguaje previamente entrenado.

¿Por qué es importante?

Conversión a Vectores: Los textos no pueden ser procesados directamente en una base de datos vectorial. Es necesario convertirlos en representaciones numéricas (embeddings).
Modelo Preentrenado: Usamos un modelo ya entrenado para obtener embeddings de alta calidad sin necesidad de entrenamiento adicional.
Aplicaciones: Esta función será clave para tareas como:
        - Búsqueda semántica.
        - Comparación de similitud entre textos.
        - Almacenamiento en bases de datos vectoriales.

## Creación y Carga de una Base de Datos Vectorial con Chroma

      Chroma.from_documents(textos, embedding_function,  persist_directory="./data")
      db = Chroma(persist_directory="./data", embedding_function=embedding_function)

Este código realiza dos pasos principales relacionados con la creación y gestión de una base de datos vectorial utilizando Chroma.

1 - Chroma.from_documents(textos, embedding_function,  persist_directory="./data")

Crea una base de datos vectorial a partir de una lista de documentos (textos) y una función de incrustación (embedding_function).
Parámetros: textos: Lista de documentos previamente segmentados (en este caso, los textos divididos en fragmentos). embedding_function: Función que convierte cada documento en un vector numérico.persist_directory="./data": Especifica el directorio donde se almacenará la base de datos de forma persistente.

2 - db = Chroma(persist_directory="./data", embedding_function=embedding_function)

Carga la base de datos vectorial ya creada desde el directorio persistente ("./data") para realizar consultas u otras operaciones.
Parámetros: persist_directory="./data": Ubicación de la base de datos previamente guardada. embedding_function: La misma función de incrustación utilizada durante la creación, necesaria para garantizar consistencia en las consultas.

## Interfaz de Consulta para Base de Datos Vectorial con Gradio

Este fragmento de código implementa una interfaz gráfica de usuario utilizando Gradio para realizar consultas en una base de datos vectorial (db). Permite a los usuarios escribir preguntas o búsquedas y obtener los documentos más relevantes de la base de datos.

![Screenshot from 2025-01-16 15-51-12](https://github.com/user-attachments/assets/df366673-51ad-4099-b1d3-14dd23d6a713)

![Screenshot from 2025-01-16 15-51-36](https://github.com/user-attachments/assets/0dc2ea53-3555-4e2e-b5f0-b9b325f96004)


## Actualizacion de documento

Al igual que las bases de datos relacionales, puedes actualizar los valores de las colecciones. Para actualizar el texto y los metadatos, proporcionaremos el ID específico del registro y el nuevo texto.

    collection = db.get()

    if textos and isinstance(textos, list) and len(textos) > 0:
        textos[0].metadata = {
            "tag": "demo"
        }
    
        # updating the vector store
        db.update_document(
            document=textos[0],
            document_id=collection['ids'][0]
        )
    
        collection = db.get(where={"tag" : "demo"})
    else:
        print("No documents found in the collection.")

Este código actualiza los metadatos de un documento en una base de datos vectorial y luego realiza una consulta para recuperar los documentos que coincidan con un criterio específico.
Explicación de los Pasos:

  Obtener la Colección de Documentos (db.get):
        Recupera todos los documentos almacenados en la base de datos vectorial y los almacena en la variable collection.

  Validar y Modificar el Primer Documento:
        Verifica que textos:
            Exista y sea una lista.
            Contenga al menos un documento.
        Si estas condiciones se cumplen:
            Modifica los metadatos del primer documento (textos[0]) para agregar un campo tag con el valor "demo".

  Actualizar el Almacén Vectorial:
        Usa el método update_document para aplicar los cambios al documento en la base de datos vectorial, identificándolo mediante su document_id.

  Consultar Documentos con Metadatos Específicos:
        Realiza una búsqueda en la base de datos para recuperar todos los documentos cuyo metadato tag tenga el valor "demo".

  Manejo de Casos Sin Documentos:
        Si no hay documentos en textos, muestra un mensaje indicando que no se encontraron documentos en la colección.


## Filtrado de Documentos en la Base de Datos Vectorial

    collection = db.gnet(where={"tag" : "demo"})
    collection

Este código utiliza el parámetro where para filtrar los documentos de una base de datos vectorial, devolviendo solo aquellos que cumplen con una condición específica en sus metadatos.

Explicación de los Pasos:

  Filtrar Documentos con el Parámetro where:
  La función db.get(where={"tag": "demo"}) recupera únicamente los documentos cuya clave de metadatos tag tiene el valor "demo".
  Este filtrado se realiza directamente en la base de datos vectorial, optimizando la búsqueda.

  Resultado (collection):

  La variable collection contendrá una estructura que incluye los documentos filtrados, sus contenidos, metadatos y embeddings.

![Screenshot from 2025-01-16 17-50-27](https://github.com/user-attachments/assets/38232a02-2139-4bc6-89c1-bcfb32d090c0)

## Eliminación de Documentos en la Base de Datos Vectorial

    collection = db.get()
    db._collection.count()
    db._collection.delete(where={'$and' : [
    { 'page' : {'$gte': 1}},
    {'page' : {'$lte': 10 }}
    ]}, where_document={'$contains': 'herederos' })

print("despues de la eliminacion", db._collection.count())

db._collection.count():

Este comando devuelve el número total de documentos almacenados en la colección db._collection.
db._collection es una referencia interna a la colección de documentos en la base de datos (Chroma en este caso).
count() devuelve el número de elementos dentro de la colección en ese momento.

db._collection.delete(where={'$and': [{ 'page': {'$gte': 1}}, {'page': {'$lte': 10 }}]}, where_document={'$contains': 'herederos'}):

Este comando elimina los documentos que cumplan ciertas condiciones definidas en los filtros de where y where_document.
where: Es un filtro para buscar documentos basados en ciertos criterios. En este caso, estamos buscando documentos cuya página (page) esté entre 1 y 10, es decir, aquellos que estén en el rango de páginas de 1 a 10 ($gte es "greater than or equal to" o "mayor o igual a", y $lte es "less than or equal to" o "menor o igual a").
    where_document: Este filtro busca dentro del contenido de los documentos las palabras o frases que coincidan con la cadena 'herederos'. El operador $contains significa que el contenido del documento debe contener esta palabra clave.
    En resumen, este comando eliminará de la colección todos los documentos que estén en las páginas de 1 a 10 y que contengan la palabra 'herederos' en su contenido.

print("despues de la eliminacion", db._collection.count()):

Después de la eliminación de los documentos, se ejecuta nuevamente db._collection.count(), lo que muestra el número total de documentos que quedan en la colección después de haber eliminado los documentos que coincidían con los filtros anteriores.
Esto te permite ver cuántos documentos han sido eliminados de la colección.


## Eliminacion de una coleccion

    db.delete_collection()

db.delete_collection():

Este comando elimina por completo la colección de documentos que has creado o cargado en la base de datos Chroma.
En lugar de eliminar documentos específicos de la colección (como lo hicimos previamente con el comando delete con filtros), este comando borra toda la colección y todos los documentos que contiene.
Es decir, después de ejecutar este comando, todos los documentos, metadatos, vectores y cualquier dato asociado con esa colección se eliminan permanentemente de la base de datos. No quedará ninguna referencia ni documento de la colección eliminada.
Precaución: Este comando es irreversible, por lo que debes asegurarte de que realmente quieres eliminar la colección completa antes de ejecutarlo.

