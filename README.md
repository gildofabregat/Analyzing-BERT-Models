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

La tarea se presenta como una problema de clasificación binaria desbalanceada, donde es mucho mayor el numero de instancias de la clase no-clickbait:
    
- Utilizar metricas de evaluación teniendo en cuenta este aspecto puede favorecer el tomar consciencia del rendimiento real del modelo. Adicionalmente, se puede considerar añadir una ponderación sobre las clases durante el entrenamiento, no obstante, esto estableceria un conocimiento a priori sobre la tarea que puede no ocurrir en otros contextos i.e. corpus, etc.

- Atendiendo a datos mostrados en el articulo [7] durante la anotación se establecierón criterios de anotación en función de umbrales respecto al concepto de clickbaiting. Las clases con mayor agreement se concentrarón en los extremos (non-clickbait y heavily clickbaiting). El disagreement mostrado durante la anotación puede servir como oraculo del rendimiento ideal esperado por un sistema automatico.


<img src="https://www.researchgate.net/profile/Tim-Gollub/publication/330009436/figure/tbl2/AS:709773737590784@1546234654833/Webis-Clickbait-Corpus-2017-Corpus-acquisition-overview-left-corpus-annotation.png" alt="Imagen extraida del articulo: Webis-Clickbait Corpus 2017-Corpus acquisition overview" width="600"/>

- Los terminos mas frecuentes utlizados en la clase clickbait, tienden a ser términos atractivos para el usuario o que en un principio tratan de llamar el interes.

- Los expresiones mas frecuentes utilizadas en la clase clickbait, tienden a ser mensajes dirigidos al usuario e.g. "you need to know"... "do it to obtain"...

- Analizando la polaridad media de los mensajes de ambas clases, no hay evidentes diferencias. Respecto a la subjectividad, aunque minimas, hay algunas diferencias que podrían ayudar a una mejor segmentación.

- Aplicando técnicas como la presentada en [6] los keywords mas relevantes indican que gran parte de las intancias tratan sobre noticias relacionadas con Donal Trump, entre otros.

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

### Analisis de errores

El modelo entrenado con Roberta ha sido el modelo que mejor rendimiento ha alcanzado, siendo su nivel de error comparable con el nivel de disagreement mostrado durante la anotación del corpus. Los siguientes datos se han obtenido del notebook correspondiente:

Matriz de confusion
![Matriz de confusion Roberta-Base-Cased](https://user-images.githubusercontent.com/7199540/162567238-9e73ea77-94e8-4115-96aa-a8b0141fca7d.png)



Conjunto de los primeros 20 falsos positivos detectados:

```
- Don't abandon the snot if you want to be healthy. #EwwButTrue
- United Takes Added Steps to Win Back Customers and Avoid More Ugly Events
- Shea Moisture put out—and promptly pulled—(another) controversial ad.
- Here's where weed is legal in the U.S.:
- The fascinating hidden history of London's lost rivers
- 21 really cute tattoos that are perfect for couples
- How to get Lady Gaga's mesmerizing eyes from #Coachella
- As per latest leak coming out of #China, the #iPhone8 may sport some truly unique #features
- Asteroid study explains how disaster movies get it wrong  via @NBCNewsMACH
- Big ideas: the airline mess, provocative views from Bridgewater's Dalio. Plus @domino's win!
- This cyclist just conquered the art of aerodynamics 🎥:
- 27 stylish bathing suits you can get on Amazon
- 'Poppers' can cause irreversible damage to your eyes
- Elon Musk has an awkward problem at Tesla: employee parking
- State of emergency declared in Egypt following Palm Sunday church attacks
- MTV gets rid of gender-specific categories for the MTV Movie &amp; TV Awards.
- Senate expected to confirm Neil Gorsuch as Supreme Court justice
- The National Organization for Women call for Bill O'Reilly to be fired as 12 companies pull their ads
- What is the nuclear option? Here is how the maneuver would work:
- Explorer: Along the Mekong River, a Timeless Place Where Time Marches On
```

Como se puede observar el sistema comete errores evaluando como clickbaits titulares como "State of emergency...", "... nuclear option...", "... to Win..." y "... if you want to be healthy".

---

Conjunto de los primeros 20 falsos negativos detectados:

```
- He worked in a factory floor. He now helps build the robots that are replacing human labor
- Congratulations, Kalpit Veerval 👏
- Jealous wife chops off her husband's PENIS with a pair of scissors after growing 'tired of his womanising'
- Latest Uber controversy sheds light on how companies use your emails:
- "Why is Bill Clinton given a pass over the other Bills?"
- Guys, the new “Zelda” game is so freakin’ fun
- What the French elections mean for Brexit
- Did Von Miller wear a better Coachella outfit than Cam Newton? 🤔
- How the shale boom turned the world's energy markets upside down:
- Opinions: A young prince is reimagining Saudi Arabia. Can he make his vision come true?
- What to watch for in the first round of the French presidential election
- Chris Brown's new documentary is coming to theaters and shit gets real
- Dana White reveals just how much money Conor McGregor could bank for Floyd Mayweather fight
- Riding the wave: Meet the surfer who helped 7 million people get clean water
- Here's what the 'Game of Thrones' stars look like in real life
- The internet has found Tom Hardy's doppelganger. 👀
- Check out the full credits for Harry Styles' #SignoftheTimes single
- REVEALED: Kim’s plan to kidnap Western tourists from South Korea
- A roundup of top #entertainment stories from the day that was
- America's airlines are shameless. But United just set a new low | Arwa Mahdawi
```


### Posibles mejoras

- Incorporación de modelos propios del dominio. Modelos como BertTweet [3] pueden suponer interesantes mejoras dado que estan entrenados con datos extraidos de Twitter.
- Estudio de modelos multilingues. Represetaciones multiligues como LABSE y Multilingual Sentence Encoder [4,5] podrian ayudar a minimizar la necesidad de anotar datos en otros idiomas.
- Dado que Roberta es un modelo case sensitive y la tarea en cuestión, probablemente, no requiere de la consideración de esta feature, resulta de interes la exploración de modelos no case sensitive. Extendiendo esta hipotesis, he explorado el uso de bert-base-uncased, con los siguientes resultados:

| Modelo | Precision | Recall | Fscore |
|--------|------------|--------|--------|
|BERT |  0.820   | 0.769   | 0.789    |
|BERT-uncased |  0.798   | 0.787   | 0.792    |


## Referencias

[1] https://webis.de/data/webis-clickbait-17.html

[2] https://towardsdatascience.com/byte-pair-encoding-subword-based-tokenization-algorithm-77828a70bee0

[3] https://huggingface.co/vinai/bertweet-base

[4] https://tfhub.dev/google/LaBSE/2

[5] https://tfhub.dev/google/universal-sentence-encoder-multilingual/3

[6] https://www.sciencedirect.com/science/article/abs/pii/S0020025519308588

[7] https://arxiv.org/abs/1812.10847#:~:text=The%20Clickbait%20Challenge%202017%3A%20Towards%20a%20Regression%20Model%20for%20Clickbait%20Strength,-Martin%20Potthast%2C%20Tim&text=Clickbait%20has%20grown%20to%20become,their%20websites%20using%20clickbait%20messages.
