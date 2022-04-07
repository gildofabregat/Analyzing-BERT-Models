# Analyzing-BERT-Models

Con motivo de explorar una tarea de clasificación de textos, y dada la facilidad que existe actualmente para explorar modelos de deep learning, debido tanto a la cantidad de código diponible como a los recursos que cada día se van poniendo a disposición del publico con APIs cada vez mas accesibles, en este repositorio encontraras diferentes notebooks donde se explora el rendimiento de modelos basados en BERT para la detección de clickbaits. Mi idea con esto, es explorar las bondades y limitaciones de cada uno de los modelos para la tarea a explorar.

Tarea a explorar: Detección de clicbait
El termino clickbait al responde a la expresión "caer en el cebo" o como se diría en internet, "hacer click en el cebo". Esta técnica surge con el auge de las nuevas tecnologias como medio de información, y consiste en la utilización de mensajes "atractivos" (i.e. sensacionalistas o de dudosa procedencia) para atraer al publico hacia determinadas webs generando así trafico en ellas.

e.g:

    ¡Esta historia es alucinante!
    Lo que sucederá a continuación te sorprenderá…
    ¡No creerás lo que pasó en el siguiente video!
    Antes era una mujer normal, pero ahora mira cómo ha cambiado…
    El antes y el después de…

Con el fin de explorar y evaluar sistemas automáticos para la detección de clickbaits recursos como el Clickbait Corpus 2017 [1] se pusieron a disposición del publico. Este corpus consiste en una extensa colección de tweets extraidos de los 27 mas importantes  medios de comunicación de Estados Unidos.


Modelos explorados:

Por restricciones del enunciado, se prioriza en este estudio la exploración de modelos basados en BERT y semejantes. En total se van a explorar los siguientes modelos:

- BERT:
- Roberta: 
- Albert: Este modelo basado en BERT propone cambios interesantes la aquitectura original, como la reducción de parametros redundantes mediante la compartición de parametros entre layers y la incorporación de SOP (Sentence Order Prediction) en contraposición a NSP (Next Sentence Prediction). A grandes rasgos mientras que NSP busca la coherencia y la estructura a la hora de determinar si una tupla de oraciones son consecutivas, SOP unicamente se centra en la coherencia, reduciendo la complejidad durante el entrenamiento.
https://huggingface.co/albert-base-v2
- Canine-c: Este modelo basado en transformer ha sido entrenado con un corpus multilingue siguiendo un enfoque de autosupervisión similar al planteado en BERT. Entre las particularidades que presenta este modelo se encuentra el uso de una representación interna basada en caracteres con el fin de paliar posibles errores derivados de procesos de tokenización. He considerado este modelo de interes dada su presencia en el estado del arte y su capacidad de adaptación a dominios no libres de errores.
https://huggingface.co/google/canine-c

Resultados:


| Modelo | Precision | Recall | Fscore |
|--------|------------|--------|--------|
|CANINE-C | 0.799 | 0.768 | 0.781 |
|ALBERT-base-v2|0.801|0.761|0.778|
|Roberta|   |    |    |
|BERT |     |    |     |




[1] https://webis.de/data/webis-clickbait-17.html
