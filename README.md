# Proyecto I Bases II
Sebastián Alpizar Morales - 2019187543<br>
Melina Valdez Calderón - 2016077990 <br>
Oswaldo Ramírez Fernández- <br>

Para este proyecto se realizó un análisis de eficiencia de tres diferentes modelos de bases de datos: Monolítico SQL, Monolítico de Mongo y Sharded Cluster de Mongo. 

<h2><b>Información Relevnte</b></h2>
El presente proyecto solo se realizó con 3 personas, por lo que el número de shards disminuyó de 3 a 2. Esto incluye una disminución en el número de shard tags por una limitante de los rangos del método de sh.shardCollection de mongo el cuál no aceptaba que 2 tags distintos pertenezcan a un mismo shard.

<br>
<br>

<h2>Descripción</h2>

Este proyecto se realizó por medio de la tecnología de docker. Los datos utilizados fueron obtenidos de: https://www.kaggle.com/joshuadottavio/videogamesales donde se obtuvo un dataset con 66392 datos exactos que se utilizaron para popular las bases de datos. Para el proceso de la carga del .csv se utilizó Logstash como medio de procesamiento y envío de los datos. 

Para las métricas de eficiencia se utiliza la tecnología Jmeter la cua es una herramienta que permite cuantificar la duración de las solicitudes hechas a la base de datos. Para garantizar la equidad en la carga de las solicitudes, se utilizó la tecnología de reenvío de puertos para lograr que todas las solicitudes hagan las misma cantidad de saltos de red para llegar a su destinatario.

Para mayor información del proceso de configuración de los servidores de bases de datos y las tecnologías utilizadas, por favor refiérase a los folders de cada elemento donde encontrará una bitácora de comandos para configurar los servidores por medio de docker.



<h1>Conclusiones Técnicas</h1>
A continuación se presentas las concluiones técnicas obtenidas a partir de las estadísticas. Para las pruebas se ejecutaron <b>12</b> llamadas a base de datos en cada caso. Los ejes son número de llamadas vs tiempo en mili segundos. La guía de colores para las gráfias es de: <b>naranja (SQL), amarillo (Mongo), verde (Mongo Cluster)</b>: 
<br>
<br>

<h2>Llamada con los 66392 Registros</h2>
Esta llamada se realiza con los 3 servidores y el cluster al 100% de funcionalidad. Un dato interesante es que los tiempos de respuesta del mongo monolíto se mantienen ~33% más rápidas que tanto el SQL como el Mongo CLuster. Lo que sugiere que mongo procesa más rápido las solicitudes porque no incrementa la búsqueda en distintos servidores para unificar los datos. 

![ChartTodos](https://i.ibb.co/XZ8KrQK/Todos.png)

<br>
<br>

<h2>Llamada con 3000 registros 1 Shard - Cluster 100%</h2>
Esta llamada consiste en llamar al 3000 registros con la región "América". Esto se realiza tanto en el monolítico SQL como en el monolítico de Mongo para comparar velocidades. El modo Sharded Cluster es menos eficiente que los monolíticos en un ~66%. Esto nos inidca que el modo de sharding no resuelve los queries tan rápido como se anticipaba. Además, se puede apreciar que el SQL incrementa sus tiempos de respuesta conforme aumentan las llamadas, sin embargo los dos servidores de mongo mantienen tiempos de respuesta constantes.

![Chart1Shard100](https://i.ibb.co/N2dzBLV/America.png)


<br>
<br>

<h2>Llamada con 3000 registros 2 Shards - Cluster 100%</h2>
Esta llamada consiste en llamar al 3000 registros con la región "América" y "Europa" para ver el tiempo de una operación OR. Esto se realiza tanto en el monolítico SQL como en el monolítico de Mongo para comparar velocidades. En este caso, el servidor de SQL inicia con una alta velocidad de tan solo 5000ms pero conforme se elevan las llamadas adquiere mayor peso de solicitudes y se entorpece su respuesta en un ~83% en comparación a la respuesta inicial. Mientras que el monolítico de mongo se mantiene constante en aproximademente 20k ms y el cluster en 30k. 

![Chart2Shard100](https://i.ibb.co/VDVyRJd/America-Europa.png)


<br>
<br>

<h2>Llamada con 3000 registros 1 Shard - Cluster 50%</h2>
Esta llamada consiste en llamar al 3000 registros con la región "América". Esto se realiza tanto en el monolítico SQL como en el monolítico de Mongo para comparar velocidades. En este caso el Cluster está a 50% de operabilidad. En este caso con el sistema funcionando a medias el cluster se disminuye con un tiempo de consulta ~46% menos que cuando el cluster está al 100%. Lo que deja en claro una menor carga del lado del cluster para resolver las solicitudes. En cambio, el monolítico de mongo que mantiene eestable en una velocidad parecida a las anteriores (~10k ms). El monolítico de SQL igual se mantiene con esta velocidad aproximada de ~10k ms.

![Chart1Shard50](https://i.ibb.co/Yj4ttvT/America.png)

<br>
<br>

<h2>Llamada con 3000 registros 2 Shards - Cluster 50%</h2>
Esta llamada consiste en llamar al 3000 registros con la región "América" y "Europa" para ver el tiempo de una operación OR. Esto se realiza tanto en el monolítico SQL como en el monolítico de Mongo para comparar velocidades. En este caso con el cluster al 50% podemos ver que se mantiene el tiempo de ~30k ms de las solicitudes, lo que indica menos variación que cuando se buscó con un único shard. Y comparado con la prueba del cluster al 100%, el server monolítico de SQL sigue disparándose en tiempos entre más aumentan las solicitudes superando los esperados ~30k ms que obtiene el cluster en esta prueba. 

![Chart1Shard50](https://i.ibb.co/cTB0SQn/Europa-America.png)