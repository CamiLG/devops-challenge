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

