Punto 1
===

Diagrama de arquitectura
---

En el diagrama se grafica una arquitectura en la nube de AWS en la cual tenemos:

El punto de acceso de los clientes, para los cuales se expone una url que resuelve a una ip pública, que pasará por el firewall y luego se resolverá a una ip privada del balanceador de carga. 
El balanceador redirige el tráfico a cualquiera de las dos instancias de web server (apache, nginx, etc), en este punto del gráfico se encuentran también los archivos estáticos (imagenes, logos, estilos,etc) y los archivos de javascript que se usarán para el frontend los cuales deben estar en algun tipo de volúmen o filesystem.
El tráfico es recibido por el web server y pasa al backend para procesar los requerimientos. En este caso ambos backends se conectan tanto a una base de datos SQL como a una no-sql. Ambas tienen un mecanismo de sincronización de datos para mantener la redundancia.
Tanto el frontend como el backend y las dbs están desplegadas en 2 instancias para mantener el esquema de alta disponibilidad. 
Por último, se grafica la conexión que tiene el backend con los dos servicios externos.

Para poder realizar este diagrama utilicé Lucidchart y usé como referencia los siguientes sitios: 
AWS Documentation:
https://docs.aws.amazon.com/?nc2=h_ql_doc_do

Learn About the AWS Architecture In Detail with Best Practices: https://www.projectpro.io/article/aws-architecture-with-diagram/575#mcetoc_1fubcuefvk 

Serverless Computing on AWS, Azure, and Google Cloud:
https://www.ml4devs.com/articles/serverless-architecture-for-microservices-on-aws-vs-google-cloud-vs-azure-as-iaas-caas-paas-faas/

Introduction to AWS Architecture:
https://mindmajix.com/aws-architecture

Guía completa para crear tu diagrama de infraestructura:
https://blog.hackmetrix.com/como-hacer-un-diagrama-de-infraestructura/


Punto 2
===

Despliegue Dockerizado
---
Este punto involucra el despliegue dockerizado de una app con frontend en React y Backend en Django. 
A grandez rasgos dividí el desafío en 3 partes:
1. Iniciar las apps de forma local para verificar posibles errores, requerimientos de instalación adicionales que sean necesarios para considerar en las imágenes de los contenedores o cualquier detalle adicional. 
2. Generar y publicar las imágenes tanto del frontend como del backend, que luego serán usadas para elaborar el docker-compose con todos los servicios. 
3. Generar el yaml de docker-compose con el detalle de los servicios, configuraciones de componentes adicionales (por ejemplo la db postgres, usuarios y variables de entorno, etc)

1.FRONT: A la hora de intentar levantar el frontend me encontré con varios errores que fui anotando para verificar mas tarde, pero en general no tuve mayor inconveniente. Utilicé un modelo de dockerfile en el cual la imagen se construye en partes, es decir, un multi-stage build; con un NodeJs, y un Nginx para almacenar los archivos luego del build del frontend. 

1.BACK: Respecto del proyecto Django, a la hora de instalar los elementos del requirements.txt fui anotando todas aquellas herramientas o paquetes adicionales que eran necesarios para considerarlos en la creación de la imagen del contenedor. Tuve bastantes inconvenientes con la herramienta psycopg2 ya que en la instalación daba error de compilación, y tampoco pude utilizar el binario ya compilado psycopg2-binary en su lugar. Si bien en un principio sospeché que podría ser un problema de compatibilidad por estar trabajando en un linux virtualizado en ARM, luego probé la instalación en un Linux nativo con Intel y tuve el mismo error de compilación. Así que opté por probar otra versión de la herramienta y cambiar la especificación en el requirement.txt para pasar a buildear la imagen. 

2.BUILD: Construí las imagenes y las publiqué en DockerHub para poder usarlas en la elaboración del docker-compose. 

3.DOCKER-COMPOSE: En el archivo docker-compose.yml definí y configuré todos los servicios incluyendo los volúmenes necesarios, puertos, variables de entorno y dependencias entre servicios. 

Luego levanté el compose de servicios y verifiqué que funcionen y ejecuté para el backend los comandos de migrate y makemigrations. 
Como puntos a mejorar, faltaría la configuración en el nginx para que actúe como reversal proxy para redirigir el tráfico. Además tuve un error nuevamente con el adapter del Postgres, que invoucraría una revisión de la versión del psycopg2 (el error indicaba un problema con el timezone configurado). 

Estos últimos pasos serían necesarios para iniciar el proyecto de forma local, ya sea en un entorno nativo o virtualizado, la única consideración es que las imágenes que construí son para arquitecturas intel. 

Para instanciarlo en AWS es necesario seguir los siguientes pasos: 
Crear la instancia EC2 necesaria para contener los servicios. 
Ir a la consola de creación de AMIs de AWS. 
Seleccionar el tipo de sistema (Linux, Windows, etc)
Sellecionar el tipo de Instancia con sus características de cpu, memoria, almacenamiento, red, etc. 
Configurar las reglas de tráfico, las keys de seguridad para poder autenticarnos de forma remota (e instalar el certificado en la máquina desde donde querramos conectarnos). 
En el panel de instancias, iniciar la nueva instancia.
Una vez conectado desde nuestra máquina, podemos instalar e iniciar docker y docker compose.
Clonar el repositorio en la instancia EC2, y ejecutar el docker-compose build y docker-compose up.
Finalmente podemos acceder a través de la ip que nos brinda AWS.   

Las referencias utilizadas fueron: 
Dockerizar proyectos frontend y backend:
https://semaphoreci.com/community/tutorials/dockerizing-a-python-django-web-application
https://blog.logrocket.com/dockerizing-django-app/
https://tiangolo.medium.com/react-in-docker-with-nginx-built-with-multi-stage-docker-builds-including-testing-8cc49d6ec305
https://docs.docker.com/storage/bind-mounts/
https://docs.aws.amazon.com/es_es/AmazonECS/latest/developerguide/ECS_instances.html



Punto 3
===

Pipeline
---

Para el punto 3 partí de la imagen oficial de nginx para crear otra nueva siguiendo las pautas de la prueba, y agregué al repo el index.html con la versión por default del nginx. 
Generé el dockerfile para la creación de la imagen nueva, y le asigné la ruta para que utilice el index.html sobre el cual quedarán reflejados los cambios que luego impactarán en el pipeline. Luego creé la imagen, probé iniciar un container con la misma y verificar que funcione, y la publiqué en dockerhub.
Generé el docker-compose para configurar el servicio, indicando la imagen a utilizar y el puerto, probé que funcione usando el comando “docker compose up” y una vez probado todo, pasé a generar el pipeline, para el cual decidí usar la herramienta que provee Github. 
Si bien partí de un flow que la herramienta sugirió, con la documentación pude llegar a obtener los pasos que necesitaba para que el flow se ejecute cuando se hagan cambios en el index.html, y para que buildee la imagen y la publique en DockerHub. 
Luego modifiqué el index.html para probar la ejecución del flow. No funcionó a la primera xD sino que tuve que leer los logs de cada ejecución e identificar y corregir los distintos errores para poder llegar a una ejecución exitosa. 

Las referencias utilizadas fueron: 
Docker:
https://docs.docker.com/engine/reference/commandline/build/
https://docs.docker.com/get-started/publish-your-own-image/
https://docs.docker.com/compose/gettingstarted/

CI/CD:
https://docs.github.com/es/actions/using-workflows/events-that-trigger-workflows
https://docs.github.com/es/actions/publishing-packages/publishing-docker-images
https://docs.github.com/es/actions/security-guides/encrypted-secrets

Link al repo de la imagen creada:
https://hub.docker.com/repository/docker/camilg/nginx-server/tags?page=1&ordering=last_updated

