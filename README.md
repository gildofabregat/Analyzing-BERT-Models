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

- BERT
- Roberta
- Albert
- Canine-c





[1] https://webis.de/data/webis-clickbait-17.html
