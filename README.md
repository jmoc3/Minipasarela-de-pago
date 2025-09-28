# Mini pasarela de pago

Aplicacion: https://frontend-pasarela-one.vercel.app/

El codigo funete de la aplicacion se encuentra disponible en dos repositorios publicos que pueden mirar en los siguientes enlaces:

- [frontendPasarela](https://github.com/jmoc3/frontendPasarela)
- [backendPasarela](https://github.com/jmoc3/backendPasarela)

La razon de esto radica en que es necesario que cada uno se encuentre desplegado en servicios distintos. En este caso **Vercel** para el frontend (React) y **Railway** para el backend (Laravel).

## Stack usado y detalles de infraestructura
El proyecto se encuentra construido sobre las siguientes tecnologias:
- React - Typescript
- Laravel
- Mysql

Uno de los motivos por los que se usan estas herramientas como base del codigo ( aparte de por ser las principales requeridas en el puesto de trabajo y porque son con las que mas experiencia tengo ) es por la organizacion que me permiten tener en el desarrollo de la estructura del proyecto. 

En la parte del frontend, al **React** ser un framework bastante robusto en su implementacion de componentes (.tsx) y gracias a librerias para el manejo del estado global (como **Zustand** usado en el proyecto) me permite administrar los archivos de la manera en como me parezca mas conveniente. Obviamente cierto libertinaje puede causar un desastre si no se tiene un buen concepto de lo que es una aplicacion modular, mas sin embargo, teniendo en cuenta buenas practicas, patrones de diseños y claridad en la definicion de flujos, esto permite establecer el orden para mantener facilidad de lectura y debugging en el proyecto. Esto es una aplicacion pequeña y seguramente varios archivos creados puedan parecer innecesarios debido a la poca cantidad de codigo que se encuentra en ellas (como por ejemplo los store de las entidades 'Divisas' y 'TiposDocumentos'), pero algo que mas resalto de mi codigo es una de las caracteristicas mas importantes a la hora de hablar de escalabilidad y uno de los principios SOLID que considero mas relevante y es permitir que el codigo se encuentre abierto a extensiones y cerrado a modificaciones (Principio abierto/cerrado). Cada archivo dentro de las carpetas tiene su razon de ser y su propio espacio para en caso tal se quiera implementar una funcionalidad, no sea mas que utlizar algo que ya esta implementado o extenderlo para su uso, un ejemplo perfecto seria el [Store de Facturas](https://github.com/jmoc3/frontendPasarela/blob/main/src/stores/models/Facturas.ts) que tiene cada funcion especializada en su propia responsabilidad por lo que segun necesidad se puede ir reutilizando esas funcion es otras funciones a la hora de extender las acciones del store.

En la parte del back, con **Laravel** se siguio el clasico patron modelo - vista - controlador con el mismo enfoque que se lleva trabajando y es el de hacer codigo organizado, en el que el cliente hace una peticion a una ruta de la api, la ruta va al controlador donde se gestiona los objetos de peticion y respuesta aparte de hacer uso de alguna funcion configurada en el servicio de la entidad, y el servicio es el que hace la consulta o la accion sobre la base datos con los modelos que ofrece eloquent que uno tambien debe configurar. Internamente en esta parte no hay mucha magia aparte del flujo y de como se manejan los datos, estbaa pensando en depronto usar **Redis** para el almacenamiento en cache solamente para mostrar que se como se maneja pero la verdad lo vi super innecesario teniendo en cuenta que nisiquiera suponiendo que esto es sobre un proyecto grande real valdria la pena, por lo menos no guardarlo en cache, debido a la cantidad de posibles registros que se pueden almacenar. Lo que si se podria pensar es en un sistema de colas con los Streams de redis para evitar saturacion por cantidad de peticiones, pero no se me viene a la mente otro uso. 

¿Porque mySQL y no alguna noSQL? pienso que es mas sencillo gestionar las no relacionales, y evidenciar que se manejar relaciones foraneas y normalizacion de tablas en entornos como **mySQL** me parece un dato mas importante a evidenciar en este tipo de proyectos simples.

----

### Posibles mejoras

#### Mejorar el responsive
Debajo de los 800 pixeles de ancho de la pantalla siento que se puede ajustar mejor la vista, depronto con un estilo distinto, para que el usuario pueda ver mas informacion de la tabla y sea mucho mas agradable.

#### Animaciones
Hay animaciones sencillas respecto a cambios de colores y de componentes de carga y asi, pero hubiese sido chevre hacer una especie de animacion suave para el modal de registro, o de los modales en general, para que sea menos brusco su aparicion en la mitad de la pantalla.

#### Consulta por usuario
Hacer una conexion de la tabla usuario con la de facturas para filtrar por usuario y que cada usuario pueda ver simplemente los registros que hizo pondria la aplicacion en un ambiente mas real.

#### Manejo de peticiones
Como decia en el parrafo hablando de la parte del backend, implementar un sistema de colas para el registro gradual en la tabla de facturas, se setea un producer que seria un endpoint en nuestra api para registrar los datos en el stream de redis, y se configura un worker en un comando que este escuchando a esa lista cada ciertos segundos y agarre en batches los "mensajes" pendientes enviados por el producer, si hay varios los inserta en batches, si no igual lo inserta y descartamos cualquier posibilidad de saturacion del servidor.

---


## Instrucciones para uso local

Para la ejecucion del proyecto en un entorno local, seguir los siguiente pasos:

### backendpasarelapago
* Clonar el repo -> [backend](https://github.com/jmoc3/backendPasarela)
* Ubicarse en la carpeta del proyecto
```
> composer install
> npm install
> php artisan migrate --seed
```
* Dirigirse al archivo cors.php, comentar la linea 9 y descomentar la 8
```
> php artisan serve
```
Recordar que es necesario que se encuentre en la carpeta www ya sea con laragon (recomendado) o xampp para que pueda funcionar todo correcto.

### frontendpasarelapago
* Clonar el repo -> [frontend](https://github.com/jmoc3/frontendPasarela)
* Ubicarse en la carpeta del proyecto
```
> npm install
> npm run dev
```

_Att: Orejarena_