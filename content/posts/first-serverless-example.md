---
title: "Un mini ejemplo de Serverless"
categories: [ "Serverless" ]
tags: [ "AWS", "Serverless Framework", "Typescript" ]
date: 2020-05-20
draft: false
---

Buenas! Hoy toca hacer un poco de cacherreo con Serverless. Hoy veremos un pequeño ejemplo en el que trabajaremos 
directamente sobre el [ServerlessFramework](https://www.serverless.com/) y [Amazon Web Services](https://aws.amazon.com/es/) 
(AWS a partir de ahora) para los conceptos básicos y hacer nuestra primera aplicación serverless.

## Sobre qué vamos a trabajar
Nuestra primera aplicación serverless va a ser una API HTTP, es decir, un backend al que hagamos peticiones
 de tipo GET, POST, etc y este nos responda. Para ello necesitaremos de dos servicios de AWS.

### [API Gateway](https://aws.amazon.com/es/api-gateway/)
Este servicio de AWS nos proporciona la gestión y despliegue de una API. Es aquí donde definiremos cuales son
 las rutas que soporta nuestra api y los métodos (GET, POST...) para cada una de ellas.

### [AWS Lambda](https://aws.amazon.com/es/lambda/)
Este servicio es un servicio de computación en el que nosotros definimos un bloque de código que ha de ser 
ejecutado en ciertas circunstancias. Este bloque de código que definimos no será más que una función con unos parámetros de entrada y una salida.

### ¿Cómo juntamos esto?
Ahora que hemos visto por encima ambos servicios, la pregunta es cómo juntar ambas cosas. Resulta que AWS nos 
facilita la vida haciendo uso de "eventos" de manera que, en nuestro caso, podemos definir en nuestro API Gateway que cuando se llame al endpoint /hello con el método GET, este haga la llamada a un Lambda que nosotros tengamos definido para que gestione la llamada al endpoint y genere una respuesta.
Pongamos un diagrama: 
![image|690x190](/img/first-serverless-example/lambda-api-architecture.png)
Es relativamente simple cuando lo vemos con el diagrama. El cliente lanza la peticion http que es capturada por 
el API Gateway. Esta después hace la invocación de la funcion Lambda. Cuando la ejecución de esta termina, manda 
la respuesta al quien la invocó, en este caso el API Gateway que mandará la respuesta al cliente.

## El código
Antes de empezar deberemos hacer unos pasos previos de configuración para poder usar la CLI del serverless 
framework (que debemos instalar - seguir los pasos de [aquí](https://www.serverless.com/framework/docs/getting-started/)).
Lo primero que deberemos tener es una cuenta de AWS. Para ello tenemos que irnos a este 
[enlace](https://portal.aws.amazon.com/billing/signup?nc2=h_ct&src=header_signup&redirect_url=https%3A%2F%2Faws.amazon.com%2Fregistration-confirmation&language=es_es#/start) 
y registrarnos.

Una vez hecho esto, ya podremos acceder a todos los servicios de AWS y para configurar la CLI debemos hacer uso 
de un servicio llamardo IAM, en el cual crearemos unas credenciales y las configuraremos con la CLI de serverless 
siguiendo los pasos descritos en este [video](https://www.youtube.com/watch?v=KngM5bfpttA). 

Bueno, por fin llega la parte divertida, así que vamos a ello. Creamos una carpeta y una vez dentro de ella, haremos 
uso del siguiente comando:

```sls create --template aws-nodejs-typescript```

Este comando nos construirá a partir de la plantilla que le especifiquemos, en nuestro caso NodeJS + TypeScript, los 
ficheros necesarios para empezar con nuestro proyecto serverless.

![image](/img/first-serverless-example/example-folder-structure.png) 

El fichero más relevante de nuestro proyecto es el *serverless.yml* ya que es en donde definimos nuestra aplicación. 
En él incluiremos toda la información necesaria para el despliegue de nuestra aplicación. Este fichero es un fichero 
en formato yaml dividido por secciones en las cuales podremos definir por ejemplo, los permisos que tendrán cada Lambda 
(Roles del servicio IAM de AWS que conceden permisos a cada función lambda para acceder por ejemplo, a lectura sobre 
una base de datos).

Por ahora lo que nos interesa para nuestra primera aproximación es la sección *provider* y la sección *functions*.

### Serverless.yml - Provider
En esta sección definiremos, por ahora, tres cosas fundamentales. Primero el nombre del proveedor de cloud que vamos a 
usar, en nuestro caso AWS. El runtime, que especifica el entorno de ejecución que usaremos, que en nuestro caso será 
nodejs12.x para que nuestras lambdas interpreten nodejs en su ejecucion. La región en la que será desplegada nuestra 
aplicación, podemos poner la que [prefiramos](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html#concepts-available-regions), 
pero siempre intentar que esté lo más cerca posible de nuestra ubicación. En este caso voy a usar eu-west-2 que 
corresponde a Londres. Por último definiremos la variable stage que nos sirve para definir el contexto en el que 
desplegaremos, por lo que por ahora lo setearemos a dev.

![image](/img/first-serverless-example/example-provider-section.png) 

### Serverless.yml - Functions
En esta sección definiremos todas nuestras funciones lambda así como los eventos para los que se lanzarán.

![image](/img/first-serverless-example/example-functions-section.png)

Como vemos en el ejemplo que nos genera la plantilla, define una función *hello* cuyo handler, es decir, función 
(hablando en código) que se va a ejecutar cuando se lance el lambda, es la función *hello* que está definida en el 
fichero handler.

Dentro de la función tenemos el campo de *events* que es una lista de los eventos que harán que se ejecute la función. 
En nuestro caso el evento que definimos es *http* para especificar que es el API Gateway el que va a invocar al lambda 
cuando le llegue una petición. Como vemos, dentro de la especificación del evento http podemos definir el método de 
la petición y la ruta.

En este sentido no estamos limitados, podemos definir peticiones tipo GET, POST, DELETE, etc. Tenemos otros tipos de 
eventos como la invocación del lambda cuando se publique en un topic de [SNS](https://aws.amazon.com/es/sns/).

 ### Nuestra función
Si nos metemos en nuestro fichero handler.ts podremos ver la definición de la función que se ejecutará en nuestra 
lambda. Los parámetros que recibe nuestra función van en función del tipo de evento que hace que se ejecute nuestro 
lambda. En nuestro caso, lo que recibiremos por ser del tipo http API, recibiremos como parámetro la 
[siguiente estructura](https://github.com/awsdocs/aws-lambda-developer-guide/blob/master/sample-apps/nodejs-apig/event.json) 
que tendrá toda la información necesaria para poder gestionar la petición.

Veamos ya el primer ejemplo de función.

![image](/img/first-serverless-example/example-function-code.png)

En nuestro caso al haber usado la plantilla que hace uso de typescript, el IDE nos ayudará con los tipos de manera 
que podemos tirar de autocompletado en caso de que no sepamos el contenido en concreto de los parámetros de la función.

Del mismo modo que nuestra función recibirá unos parámetros definidos, nosotros debemos devolver la respuesta de una 
manera en concreto para que el API Gateway lo pueda interpretar y poder responder al cliente. La manera en que tiene 
que estar definida la respuesta será:

![image](/img/first-serverless-example/response-structure.png) 

Como vemos en el objeto response deberemos definir el statusCode, las cabeceras y el body que a modo de curiosidad, 
tiene que ser de tipo string.

## Desplegar
Hemos visto las cosas básicas con las que podemos trabajar haciendo uso del framework pero nos queda la parte más 
importante, el despliegue...

Para ello sólo tendremos que hacer uso de un comando `sls deploy` que lo que hace es parsear el yaml en código 
[CloudFormation](https://aws.amazon.com/es/cloudformation/) (lenguaje de [Infraestrucure as Code](https://en.wikipedia.org/wiki/Infrastructure_as_code) 
propio de AWS) que genera un stack (conjunto de recursos dentro de AWS) en el servicio CloudFormation. También 
genera un archivo comprimido que sube a un [Bucket de S3](https://aws.amazon.com/es/s3/) para que las lambdas 
puedan ejecutar el código.

![image|468x500](/img/first-serverless-example/deploy.png)

El comando nos saca por pantalla un resumen de los recursos desplegados, entre los que están los endpoints definidos 
por lo que podemos probar con nuestro cliente http preferido.

![image|690x375](/img/first-serverless-example/api-call.png)

## Conclusiones
Entiendo que de primeras puede parcer mucha información pero realmente hay más adaptación de conceptos al mundo cloud 
de AWS que conceptos que no están presentes en las apis tradicionales.

En los próximos posts, veremos como gestionar los endpoints, es decir, distintos métodos, parámetros de ruta, etc. 
Cómo hacer uso de [DynamoDB](https://aws.amazon.com/es/dynamodb/) junto con como asignar permisos a las funciones.
