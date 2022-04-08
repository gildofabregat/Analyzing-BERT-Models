# Analyzing-BERT-Models

Con motivo de explorar una tarea de clasificación de textos en este repositorio encontraras diferentes notebooks donde se explora el rendimiento de modelos basados en BERT para la detección de clickbaits. Dada la facilidad que existe actualmente para explorar modelos de deep learning, debido tanto a la cantidad de código diponible como a los recursos y APIs publicos, este repositorio es unicamente para explorar las bondades y limitaciones de cada uno de los modelos para la tarea a explorar.

### Tarea a explorar: Detección de clicbait
El termino clickbait responde a la expresión "caer en el cebo" o como se diría en internet, "hacer click en el cebo". Esta técnica surge con el auge de las nuevas tecnologias como medio de información, y consiste en la utilización de mensajes "atractivos" (i.e. sensacionalistas o de dudosa procedencia) para atraer al publico hacia determinadas webs generando así trafico en ellas.

e.g:

    ¡Esta historia es alucinante!
    Lo que sucederá a continuación te sorprenderá…
    ¡No creerás lo que pasó en el siguiente video!
    Antes era una mujer normal, pero ahora mira cómo ha cambiado…
    El antes y el después de…

Con el fin de explorar y evaluar sistemas automáticos para la detección de clickbaits, recursos como el Clickbait Corpus 2017 [1] se pusieron a disposición del publico. Este corpus consiste en una extensa colección de tweets extraidos de los 27 mas importantes medios de comunicación de Estados Unidos.


### Analisis de los datos:

La tarea se presenta como una problema de clasificación binaria desbalanceada, donde es mucho mayor el numero de instancias de la clase no-clickbait.
    
    Utilizar metricas de evaluación teniendo en cuenta este aspecto puede favorecer el tomar consciencia del rendimiento real del modelo. Adicionalmente, se puede considerar añadir una ponderación sobre las clases durante el entrenamiento, no obstante, esto estableceria un conocimiento a priori sobre la tarea que puede no ocurrir en otros contextos i.e. corpus, etc.

Los terminos mas frecuentes utlizados en la clase clickbait, tienden a ser términos atractivos para el usuario o que en un principio tratan de llamar el interes.

Los expresiones mas frecuentes utilizadas en la clase clickbait, tienden a ser mensajes dirigidos al usuario e.g. "you need to know"... "do it to obtain"...

Analizando la polaridad media de los mensajes de ambas clases, no hay evidentes diferencias. Respecto a la subjectividad, aunque minimas, hay algunas diferencias que podrían ayudar a una mejor segmentación.

### Modelos explorados:

En este se prioriza la exploración de modelos basados en BERT y semejantes. En total se van a explorar los siguientes modelos:

- Baseline: Universal Sentence Encoder + Approximate Nearest Neighbors. Haciendo uso de una representación obtenida mediante universal sentence encoder (DAN - Deep Averaging Network) se presenta un baseline basado en la extracción de vecinos mas cercanos aproximada. Como herramienta se utiliza Annoy para el calculo del indice de vecinos. Como heuristica, se plantea el calculo de los 10 vecinos mas cercanos para una instancia X y la clasificacion de X como parte de la clase mayoritaria entre los vecinos recuperados.

- BERT: Modelo base de BERT. Se ha escogido la versión cased para comprobar la precisión que aporta el hecho que los mensajes analizados esten escritos por medios oficiales de prensa escrita i.e. se puede asumir que estan correctamente escritos, mayusculas incluidas.
https://huggingface.co/bert-base-cased

- Roberta: Pequeña modificación del esquema de BERT, centrada especialmente en el tokenizador utilizado. Este tokenizador se basa en el empleado por modelos como GPT-2, byte-level BPE [2]. Su uso permite tanto una mayor generalización como una mejor transferencia de conocimiento entre idiomas en contextos multilingues.
https://huggingface.co/roberta-base

- Albert: Este modelo basado en BERT propone cambios interesantes sobre la aquitectura original, como la reducción de parametros redundantes mediante la compartición de parametros entre layers y la incorporación de SOP (Sentence Order Prediction) en contraposición a NSP (Next Sentence Prediction). A grandes rasgos mientras que NSP busca la coherencia y la estructura a la hora de determinar si una tupla de oraciones son consecutivas, SOP unicamente se centra en la coherencia, reduciendo la complejidad durante el entrenamiento.
https://huggingface.co/albert-base-v2

- Canine-c: Este modelo basado en transformer ha sido entrenado con un corpus multilingue siguiendo un enfoque de autosupervisión similar al planteado en BERT. Entre las particularidades que presenta este modelo se encuentra el uso de una representación interna basada en caracteres con el fin de paliar posibles errores derivados de procesos de tokenización. He considerado este modelo de interes dada su presencia en el estado del arte y su capacidad de adaptación a dominios no libres de errores.
https://huggingface.co/google/canine-c

### Resultados:


| Modelo | Precision | Recall | Fscore |
|--------|------------|--------|--------|
|USE+ANN|0.780        |0.731| 0.750|
|CANINE-C | 0.799 | 0.768 | 0.781 |
|ALBERT-base-v2|0.801|0.761|0.778|
|Roberta| 0.818  | 0.79   | 0.802   |
|BERT |  0.820   | 0.769   | 0.789    |


Hasta el momento los resultados obtenidos destacan el uso de modelos basados en SOP frente a NSP e.g. Roberta Vs ALBERT. Por otro lado, aunque se espera que los textos esten bien formados (dado los autores de los textos), estos provienen de Twitter, una red social donde se tienden a usar tantos expresiones propias y abreviaturas. En este sentido, tokenizadores como WordPiece (e.g. BERT) entrenados con textos de Wikipedia, etc, ofrecen menos flexibilidad. Resulta destacable el bajo rendimiento de modelos como CANINE, dado que es un modelo que no utiliza tokenizador alguno i.e. unicamente hace split por espacio en blanco.

Respecto a restricciones y limitaciones de este tipo de modelos, no hay sido necesario estudiar extensiones o modelos elaborados para cubrir textos mas alla de los 512 tokens. Es más, para agilizar el computo se ha limitado el tamaño de los textos a 200 tokens.


### Posibles mejoras

- Incorporación de modelos propios del dominio. Modelos como BertTweet [3] pueden suponer interesantes mejoras dado que estan entrenados con datos extraidos de Twitter.
- Estudio de modelos multilingues. Represetaciones multiligues como LABSE y Multilingual Sentence Encoder [4,5] podrian ayudar a minimizar la necesidad de anotar datos en otros idiomas.


## Referencias

[1] https://webis.de/data/webis-clickbait-17.html

[2] https://towardsdatascience.com/byte-pair-encoding-subword-based-tokenization-algorithm-77828a70bee0

[3] https://huggingface.co/vinai/bertweet-base

[4] https://tfhub.dev/google/LaBSE/2

[5] https://tfhub.dev/google/universal-sentence-encoder-multilingual/3
