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
https://docs.github.com/es/actions/using-workflows/events-that-trigger-workflows
https://docs.github.com/es/actions/publishing-packages/publishing-docker-images
https://docs.github.com/es/actions/security-guides/encrypted-secrets


