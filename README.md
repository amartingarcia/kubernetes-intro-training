# k8s_training

k8s
-------------
**Índice**   
1. [Chapter 1. From Monolith to Microservices](#chapter1)
2. [Chapter 2. Container Orchestration](#chapter2)
3. [Chapter 3. Kubernetes](#chapter3)
4. [Chapter 4. Kubernetes Architecture](#chapter4)
5. [Chapter 5. Installing Kubernetes](#chapter5)
6. [Chapter 6. Minikube - A Local Single-Node Kubernetes Cluster](#chapter6)
7. [Chapter 7. Accessing Minikube](#chapter7)
8. [Chapter 8. Kubernetes Building Blocks](#chapter8)
9. [Chapter 9. Authentication, Authorization, Admission Control](#chapter9)
10. [Chapter 10. Services](#chapter10)
11. [Chapter 11. Deploying a Stand-Alone Application](#chapter11)
12. [Chapter 12. Kubernetes Volume Management](#chapter12)
13. [Chapter 13. ConfigMaps and Secrets](#chapter13)
14. [Chapter 14. Ingress](#chapter14)
15. [Chapter 15. Advanced Topics](#chapter15)
16. [Chapter 16. Kubernetes Community](#chapter16)

## Chapter 1. From Monolith to Microservices<a name="chapter1"></a>
## 1. From Monolith to Microservices
### 1.1. The legacy Monolith
La roca, representa la aplicación del monolito (capas sedimentadas de características y lógica redundante traducidas en miles de lineas de código, escritas en un lenguaje de programación no tan moderno, basado en patrones y principios de arquitectura anticuados)

Las nuevas características y mejoras añaden complejidad al código, haciendo el desarrollo más desafiante, los tiempos de carga, compilación y construcción aumentan con cada nueva actualización.

Al ser un software grande y único que crece continuamente, tiene que funcionar en un solo sistema que tiene que satisfacer requisitos de computación, memoria, almacenamiento y redes. El hardware de tal capacidad es a la vez complejo y costoso.

Como toda la aplicación del monolito se ejecuta como un solo proceso, la ampliación de las características individuales del monolito es casi imposible. Internamente soporta un número codificado de conexiones y operaciones. Sin embargo, el escalado de toda la aplicación significa desplegar manualmente una nueva instancia del monolito en otro servidor, típicamente detrás de un balanceador de carga, otra solución costosa.

Durante las actualizaciones, parches o migraciones de la aplicación del monolito, se producen tiempos de inactividad y hay que planificar ventanas de mantenimiento ya que se espera que las interrupciones del servicio afecten a los clientes. Si bien existen soluciones para minimizar los tiempos de inactividad de los clientes mediante la configuración de las aplicaciones de monolitos en una configuración activa/pasiva en HA, todavía puede ser un desafío para los ingenieros de sistemas mantener todos los sistemas al mismo nivel de parches.


### 1.2. The Modern Microservice.
Similitudes:
- Monolito       --> Gran roca
- Microservicios --> Guijarros

Los guijarros representan la totalidad de la roca individualmente. Además son faciles de seleccionar y agrupar en base al color, tamaño y forma.
Los microservicios pueden ser desplegados individualmente en servidores separados provistos con los recuros necesarios para cada servicio.
La arquitectura basada en microservicios está alineada con la Arquitectura Dirigida por Eventos y Arquitectura Orientada a Servicios, donde las aplicaciones complejas están compuestas por pequeños procesos que se comunican entre si por medio de APIs.
Los microservicios se desarrollan en un lenguaje moderno y el más adecuado para cada servicio. Permite elegir hardware para cada servicio.
La naturaleza distribuida de los servicios añade complejidad a la arquitectura, pero añade beneficios en cuanto a la escalabilidad, además de no tener inactividad.


### 1.3. Refactoring
Una aplicación multiproceso (monolito), no puede funcionar como un microservicio. Por lo tanto, se necesita refactorizar (Big-Bang ó refactorización incremental):
* __Big-bang:__                    
Bloquea el desarrollo y nuevas características para centrarse en la refactorización.
* __Refactorización incremental:__ 
Permite que se desarrollen nuevas características y se apliquen como microservicios modernos que puedan comunicarse con la API. 
Mientras tantos las características actuales del monolito se refactorizaran, y este irá desapareciendo.


### 1.4. Challenges
Para algunas aplicaciones, puede ser más económico reconstruirlas que refactorizarlas.
Las aplicaciones estrechamente unidas a bases de datos, son candidatas poco válidas para la refactorización.

Una vez que el monolito ha sobrevivido a la refactorización, el siguiente reto es diseñar mecanismos o encontrar herramientas adecuadas para mantener vivos todos los módulos desacoplados.
Si se despliegan muchos módulos en un solo servidor, lo más probable es que las diferentes librerias y el entorno del runtime, puedan entrar en conflicto. 
Esto obliga a separar modulos por servidor, y no es una forma económica de gestión de recursos. 
Entonces aparecieron los contenedores, que proporcionan entornos encapsulados. 
El amplio soporte de los contenedores aseguró la portabilidad  de las aplicaciones del bare-metal a las máquinas virtuales, pero esta vez con múltiples aplicaciones desplegadas en el mismo servidor.



## Chapter 2. Container Orchestration<a name="chapter2"></a>
## 2. Container Orchestration
### 2.1. What Are Containers?
* __Contenedores:__ 
Son aplicaciones que ofrecen alto rendimiento y escalabilidad. Son los más adecuados para ofrecer microservicios, ya que proporcionan entornos virtuales portatiles y aislados.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/2.1_Containers.png)

* __Microservicios:__
Son aplicaciones ligeras escritas en varios lenguajes modernos con dependencias, librerias y requisitos especificos. 
Los contenedores encapsulan los microservicios y sus dependencias pero no los ejecutan directamente. Los contenedores ejecutan imágenes de contenedores.

* __Imagen de contenedor:__
Agrupa la aplicación junto con su runtime y sus dependencias, ofreciendo un entorno ejecutable aislado.


### 2.2. What Is Container Orchestration?
En los entornos de desarrollo la ejecución de contenedores en un solo host, puede ser una opción. Sin embargo, para entornos productivos ya no es una opción viable, ya que debe cumplir una seria de requisitos:
* Tolerancia a fallos.
* Escalabildiad a petición.
* Uso óptimo de los recursos.
* Autodescubrimiento para la comunicación entre si.
* Accesibilidad desde el exterior.
* Actualizaciones y rollbacks sin downtime.

Los orquestadores de contenedores es una herramienta que permite automatizar los despliegues y la gestión de contenedores, al mismo tiempo que cumple con los requisitos anteriores. 


### 2.3. Container Orchestrators
Algunos orquestadores:
* AWS ECS
* Azure Container Instances
* Nomad
* Kubernetes
* Docker Swarm


### 2.4. Why Use Container Orchestrators?
Aunque podemos mantener manualmente un par de contenedores, los orquestadores facilitan mucho las tareas.

La mayoría de contenedores pueden:
* Agrupar host mientras se crear un cluster.
* Programar contenedores para que corran en cluster en funcion de la disponibilidad de los recursos.
* Permite que los contenedores de cluster se comuniquen entre si.
* Gestionar y optimizar el uso de recursos.
* Permitir una implementación de políticas para el acceso seguro a las aplicaciones que corren en los contenedores.


### 2.5. Where to Deploy Container Orchestrators?
Los orquestadores pueden ser desplegados en bare-metal, máquinas virtuales, on-premise o nube pública.



## Chapter 3. Kubernetes<a name="chapter3"></a>
## 3. Kubernetes
### 3.1. What Is Kubernetes?
Kubernetes es un sistema de código abierto (OpenSource) para automatizar el despliegue, el escalado y la gestión de aplicaciones en contenedores.

Viene del griego, que significa timonel. También se le conoce como k8s.
Se inspira mucho en el sistema de __Google Borg__, un orquestador de contenedores escrito en Go.
Kubernetes fue iniciado por Google, y con el lanzamiento de la v1 en 2015, fue donado a CNCF.

### 3.2. From Borg to Kubernetes
El sistema de Borg de Google, es un administrador de Clusteres que ejecuta cientos de miles de trabajos, de muchos miles de aplicaciones diferentes, a través de varios clusteres, cada uno con decenas de miles de máquinas.

Durante más de una década, Borg ha sido el secreto de Google, gestionando servicios como Gmail, Drive, Maps, etc.

Algunas características de kubernetes  heredadas de Borg son:
* __API Servers__
* __Pods__
* __IP-per-Pod__
* __Services__
* __Labels__


### 3.3. Kubernetes Features I
Kubernetes ofrece un conjunto de características para la orquestación:
* __Automatic bin packing:__
Programan en base a los recuros necesarios y limitaciones, para maximizar su utilización sin sacrificar la disponibilidad.
* __Self-healing:__
Reemplaza y reprograma automáticamente los contenedores de los __Nodes__ que fallan. Mata y reinicia los contenedores que no responden a los HealthCheck. También, envita que se dirija el tráfico a contenedores que no responden.
* __Horizontal scaling:__
se escalan manual o automáticamente las aplicaciones, en base a métricas.
* __Service discovery and Load Balancing:__
Los contenedores reciben sus propias direcciones IPs, mientras que se asigna un único DNS a un conjunto de contenedores para ayudar al balanceo de carga.


### 3.4. Kubernetes Features II
* __Automated rollouts and rollbacks:__
Despliegan y realizan __rollback__ sin problema en base a las actualizaciones de la aplicación y los cambios de configuración, supervisando constantemente la salud de la aplicación, evitando así inactividad.
* __Secret and configuration management:__
Gestiona los __secrets__ y detalles de la configuración de una aplicación por separado de la imagen del contenedor, con el fin de evitar la reconsctrucción de la imagen respectiva. Los __secrets__ consisten en información confidencial que se le pasa a la aplicación sin revelar el contenido, como en GitHub.
* __Storage orchestration:__
Montan automáticamente soluciones de __storage__ definidas por software en contenedores de almacenamiento local, proveedores de nube externos o sistemas de almacenamiento en red.
* __Batch execution:__
Apoya la ejecución por lotes, los trabajos de larga duración y reemplaza los contenedores que fallan.

Muchas otras características están por llegar aunque se encuentran en base beta. Otras, ya están estabales y aportan grandes beneficios como el control de acceso basado en roles (RBAC), estable desde la 1.8.


### 3.5. Why Use Kubernetes?
K8s es portatil y extensible. La arquitectura de k8s es modular y enchufable. 
No solo orquesta aplicaciones de tipo microservicio desacoplado, si no que su arquitectura sigue patrones de los microservicios desacoplados. 
Pueden escribirse recursos personalizados, operadores APIs, reglas de programación o plugins.


### 3.6. Kubernetes Users
Algunos usuarios que utilizan k8s:
* BlaBlaCar
* eBay
* IBM
* Huawei
* ING


### 3.7. Cloud Native Computing Foundation (CNCF)
Es uno de los proyectos alojados por Linux-Foundation. 
Tiene como objetivo acelarar la adopción de contenedores, microservicios y aplicaciones nativas de la nube.

Algunos proyectos:
* Kubernetes
* Prometheus
* Envoy
* CoreDNS
* containerd
* Fluentd

Incuvando:
* CRI-O y rkt
* Linkerd
* etcd
* gRPC
* CNI
* Harbor
* Helm
* Rook y Vitess
* Notary
* TUF
* NATS
* Jaeger y OpenTracing
* Open Policy Agent


### 3.8. CNCF and Kubernetes
Para k8s, CNCF:
* Hogar neutral para la marca registrada, hace cumplir el uso adecuado.
* Proporciona una licencia para escanear el código del nucleo y del proveedor.
* Ofrece oritentación jurídica sobre cuestiones de patentes y derechos de autor.
* Crea un plan de estudios de aprendizaje de código abierto, formación y certificación tanto para los administradores como para los desarrolladores de k8s.
* Gestiona un grupo de trabajo de conformidad con el software.
* Comercializa activamente Kubernetes.



## Chapter 4. Kubernetes Architecture<a name="chapter4"></a>
## 4. Kubernetes Architecture
### 4.1. Kubernetes Architecture
A un nivel muy alto, kubernetes cuenta con los siguientes componentes principales:

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/4.1_KubernetesArchitecture.png)

* Uno o más __Master Nodes__.
* Uno o más __Worder Nodes__.
* Base de datos distribuida, key-value, __etcd__.


### 4.2. Master Node
El __Master Node__, proporciona un entorno de ejecución, es el reponsable de gestionar el estado del cluster de k8s, y es el cerebro detrás de todas las operaciones.
Para comunicarse con el cluster, los usuarios envian solicitudes al __Master Node__ a través de la __CLI__, un panel de control de interfaz de usuario o una interfaz gráfica de programación __(API)__.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/4.2_KubernetesMasterNode.png)

El plano de control mantiene un registro de todos los objectos de k8s en el sistema, y ejecuta bucles de control continuos para gestionar el estado de esos objetos.
Es importante mantener el plano de control. Perder el plano de control puede introducir tiempos de inactividad, causando la interrumpción del servicio a los clientes. 
Para asegurar tolerancia a fallos, se añaden réplicas del __Master Node__ al cluster, configurandolo así, en modo HA.
Solo uno de los __Master Node__ administra activamente el cluster, el resto de componentes permanencen sincronizados con el resto de __Master Replicas__. 

Para mantener el estado del cluster de k8s, todos los datos de la configuración del cluster se guardan en __etcd__. 
Sin embargo, __etcd__ es un almacenamiento __key-value distribuido__, que solo guarda datos relacionados con el estado del cluster. 
Está configurado en el __Master Node__ y ubicado en un host dedicado, para evitar las posibilidades de pérdidas.


### 4.3. Master Node Components
Un __Master Node__ tiene los siguientes componentes:
* __API Server__
* __Scheduler__
* __Controller managers__
* __etcd__


### 4.4. Master Node Components: API Server (kube-apiserver)
* Todas las tareas administrativas, están coordinadas por el. 
* Intercepta las llamadas RESTfull de los usuarios, operadores y agentes externos, las valida y las procesa. 
* Durante el procesamiento, lee el estado del cluster desde __etcd__, y despues de la llamada, el estado resultante del cluster de k8s se guarda en __etcd__.
* __API Server__, es el único componente que habla con __etcd__, tanto para leer como para escribir, actuando como una interferfaz de intermediario.
* Es configurable, también admite la adición de API Servers personalizados, cuando el API Server primario se convierte en un proxy de todos los servidores API.


### 4.5. Master Node Components: Scheduler
El role del __Scheduler__ es asignar nuevos objetos a los __Pods__ de los __Nodes__. 
Durante el proceso de programación, las decisiones se toman en base al estado actual del cluster, y a los requisitos de los nuevos objetos.
El __scheduler__, obtiene de __etcd__, a través de __API Server__, los datos de uso de recursos de cada nodo del cluster.
También recibe de __API Server__, los requisitos del nuevo objeto. 
Los requisitos pueden incluir las restricciones que establecen los usuarios y los operadores. 
El __Scheduler__ también tiene en cuenta los requisitos de calidad (QoS), la localización de los datos, la afinidad, la antiafinidad, tolerancia, etc.

Es altamente configurable, si se añaden más __Schedulers__, es necesario indicar en el objeto el nombre del __Scheduler__, sino lo gestionará el de por defecto.

> Es bastante importante y complejo este componente.


### 4.6. Master Node Components: Controller Managers
Son componentes del plano de control en el __Master Node__ que regulan el estado del cluster. 
Son bucles de vigilancia que se ejecutan continuamente y comparan el estado deseado del cluster (proporcionado por los nuevos objectos) con el estado actual (proporcionado de etcd a traves de __API Server)__.
En caso de desajuste, se toman medidas correctivas para hacer coincidir con el estado deseado.

Los __Controller Managers__, son los encargados de actuar cuando los __Nodes__ no están disponibles, garantizar que el número de __Pods__ son el esperado, de crear __Endpoints__, __Service Accounts__, y __API access tokens__.
Es el encargado de interactuar con la infraestructura subyacente de un proveedor de nubes cuando los __Nodes__ no están disponibles, de gestionar volumenes de almacenamiento, equilibrar el balancedo de carga y el enrutamiento.


### 4.7. Master Node Components: etcd
Es un __almacenamiento de datos clave-valor distribuido__ que se utiliza para persistir el estado de cluster. 
Los nuevos datos se escriben en el almacen solo añadiendolos, los datos nunca son reemplazados. 
Los datos obsoletos se compactan periódicamente para minimizar el tamaño de __etcd__.

> Solo el API Server es capaz de comunicarse con etcd.

La herramienta de gestión (CLI) de __etcd__ proporciona capacidades de copias de seguridad, snapshots, y restauración. 

> Para los entornos productivos, es importante replicarlos en HA.

Algunas herramientas de arranque de clusters de k8s, por defecto, aprovisionan __Master Nodes__ de etcd aplilados, en los que __etcd__ se ejecuta junto a los demás componentes del __Master Node__ y comparte recursos con ellos. 
Puede aislarse el __etcd__ en un host separado, reduciendo así posibilidades de fallo. 
Tanto las configuraciónes en el mismo host o en otro, adminten configuración en HA.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/4.7_MasterandFollowers.png)

> etcd está escrito en Go.
Además de almacenar el estado del cluster, también se almacenan detalles de configuración como subredes, ConfigMaps, secrets, etc.


### 4.8. Worker Node
Un __Worder Node__, proporciona un entorno de ejecución para las aplicaciones clientes. 
Aunque son microservicios en contenedores, estas aplicaciones están encapsuladas en __Pods__, controladas por componentes del __Master Node__.
Los __Pods__ se programan en los __Worker Node__, donde se encuentran los recursos de computación, memoria, almacenamiento y la red para hablar entre ellos y el mundo exterior.
> Un Pod, es la unidad mínima de k8s.  Es una colección lógica de uno o más contenedores juntos.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/4.8_KubernetesWorkerNode.png)

Además, para acceder a las aplicaciones del mundo exterior, nos conectamos a los __Worker Nodes__ y no al __Master Node__.


### 4.9. Worker Node Components
Un __Worker Node__, cuenta con los siguientes componentes:
* __Container Runtime__
* __kubelet__
* __kube-proxy__
* __Addons__ para DNS, Dashboards, monitorización y registros de logs.


### 4.10. Worker Node Components: Container Runtime
Aunque k8s es un motor de orquestación de contenedores, no tiene la capacidad de manejar directamente los contenedores. 
Para ejecutar y gestionar el ciclo de vida de un contenedor, se requiere de un __runtime__ en el __Node__ en el que se va a programar un __Pod__ y sus contendores. 
k8s soporta:
* __Docker__: utiliza como runtime, containerd, es el más utilizado con k8s.
* __CRI-O__: contenedor ligero para k8s, soporta registros de imagenes Docker.
* __containerd__: simple y portatil, proporciona robustez.
* __rkt__: motor nativo para pods, disponible para imagenes docker.
* __rktlet__:


### 4.11. Worker Node Components: kubelet
__kubelet__ es un agente que se ejecuta en cada __Node__ y se comunica con los componentes del plano de control del __Master Node__. 
Recibe las definiciones del __Pod__, principalmente del __API Server__, e interactúa con el __runtime__ del contenedor en el __Node__ para ejecutar los contenedores asociados al __Pod__. 
También supervisa la salud de los contenedores en ejecución del __Pod__.

__kubelet__ se conecta al __runtime__ del contenedor mediante la Interfaz de Tiempo de Ejecución del Contenedor __(CRI)__. 
> CRI consiste en buffers de protocolo, gRPC API, y librerías.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/4.11_ContainerRuntimeInterface.png)

__kubelet__ que actúa como cliente grpc se conecta a la __CRI__, que actúa como servidor grpc para realizar operaciones de contenedor e imagen. 
__CRI__ implementa dos servicios: __ImageService__ y __RuntimeService__:

* __ImageService__ es responsable de todas las operaciones relacionadas con la imagen.
* __RuntimeService__ es responsable de todas las operaciones relacionadas con el __Pod__ y el contenedor.

> Los __runtime__ de los contenedores solían estar codificados en duro en Kubernetes, pero con el desarrollo de CRI, Kubernetes es más flexible ahora y utiliza diferentes __runtimes__ de los contenedores sin necesidad de recompilar. 
Cualquier __runtime__ de contenedor que implemente la CRI puede ser usado por Kubernetes para gestionar __Pods__, contenedores e imágenes de contenedores.


### 4.12. Worker Node Components: kubelet - CRI shims
Ejemplos de CRI:

* __dockershim:__ los contenedores se crean usando Docker instalado en los nodos de los trabajadores. Internamente, Docker utiliza __containerd__ para crear y gestionar los contenedores.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/4.12_dockershim.png)

* __cri-containerd:__ podemos usar directamente el contenedor de la descendencia más pequeña de Docker para crear y gestionar los contenedores.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/4.12_cri-containerd.png)

* __CRI-O:__ permite utilizar cualquier runtime compatible con la Iniciativa de Contenedor Abierto __(OCI)__ con los Kubernetes.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/4.12_CRI-O.png)


### 4.13. Worker Node Components: kube-proxy
El __kube-proxy__ es el agente de red que se ejecuta en cada __Node__ responsable de las actualizaciones dinámicas y del mantenimiento de todas las reglas de red del __Node__. 
Absorbe los detalles de la red de __Pods__ y reenvía las solicitudes de conexión a __Pods__.


### 4.14. Worker Node Components: Addons
Los __addons__ son características y funcionalidades de los clústeres que aún no están disponibles en los Kubernetes, por lo que se implementan a través de __Pods__ y servicios de terceros.

* __DNS__ - el clúster DNS es un servidor DNS necesario para asignar los registros DNS a los objetos y recursos de Kubernetes.
* __Dashboard__ - una interfaz de usuario de propósito general basada en la web para la gestión de clústeres.
* __Monitoreo__ - recopila las métricas de los contenedores a nivel de clúster y las guarda en un almacén central de datos.
* __Registro__ - recoge los registros de los contenedores a nivel de grupo y los guarda en un almacén central de registros para su análisis.


### 4.15. Networking Challenges
Las aplicaciones basadas en microservicios desacoplados dependen en gran medida de la conexión en red para imitar el estrecho acoplamiento que una vez estuvo disponible en la era monolítica. 
La conexión en red, en general, no es la más fácil de entender e implementar. Kubernetes no es una excepción - como un orquestador de microservicios en contenedores es necesario abordar 4 desafíos de red distintos:

* Comunicación de contenedor a contenedor dentro de los __Pods__.
* La comunicación de __Pod a Pod__ en el mismo __Node__ y a través de otros __Nodes__ de otros clusters.
* La comunicación de __Pod a Servicio__ dentro del mismo __namespaces__ y a través de los __namespaces__ de los clústeres.
* Comunicación externa al servicio para que los clientes accedan a las aplicaciones de un cluster.

Todos estos desafíos de red deben ser abordados antes de desplegar un cluster de Kubernetes.


### 4.16. Container-to-Container Communication Inside Pods
Haciendo uso de las características del kernel del sistema operativo del host subyacente, el __runtime__ de un contenedor crea un espacio de red aislado para cada contenedor que inicia. 
En Linux, ese espacio de red aislado se denomina __network namespace__. 
Un __network namespaces__ se comparte entre contenedores, o con el sistema operativo del host.
Cuando se inicia un __Pod__, se crea un __network namespace__ dentro del __Pod__, y todos los contenedores que se ejecutan dentro del __Pod__ compartirán ese __network namespace__ para que puedan hablar entre ellos a través del localhost.


### 4.17. Pod-to-Pod Communication Across Nodes
En un grupo de Kubernetes los __Pods__ están programados en __Nodes__ al azar. 
Independientemente de su __Node__ anfitrión, se espera que los __Pods__ puedan comunicarse con todos los demás __Pods__ del cluster, todo esto sin la implementación de la Traducción de Direcciones de Red (NAT). 
Este es un requisito fundamental de cualquier implementación de red en los kubernetes.

El modelo de red de los Kubernetes tiene como objetivo reducir la complejidad, y trata a los __Pods__ como máquinas virtuales en una red, en la que cada máquina virtual recibe una dirección IP, por lo que cada __Pod__ recibe una dirección IP. 
Este modelo se denomina __"IP por PC"__ y garantiza la comunicación entre los __Pods__, al igual que las máquinas virtuales pueden comunicarse entre sí.

Sin embargo, no nos olvidemos de los contenedores. Comparten el espacio de nombres de la red del __Pod__ y deben coordinar la asignación de puertos dentro del __Pod__ como lo harían las aplicaciones en una VM, todo ello mientras pueden comunicarse entre sí en el host local - dentro del __Pod__. 
Sin embargo, los contenedores se integran con el modelo general de red de Kubernetes mediante el uso de la Interfaz de Red de Contenedores __(CNI)__ soportada por los plugins CNI. 
La __CNI__ es un conjunto de especificaciones y librerías que permiten a los plugins configurar la red de contenedores. 
Aunque hay unos pocos plugins de kernel, la mayoría de los plugins CNI son soluciones de red definidas por software (SDN) de terceros que implementan el modelo de red de Kubernetes. 
Además de abordar el requisito fundamental del modelo de red, algunas soluciones de red ofrecen soporte para las políticas de red. 
__Flannel__, Weave, __Calico__ son sólo algunas de las soluciones SDN disponibles para los clusters de Kubernetes.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/4.17_ContainerNetworkInterface.png)

El __runtime__ del contenedor descarga la asignación de IP a CNI, que se conecta al plugin configurado subyacente, como Bridge o MACvlan, para obtener la dirección IP. Una vez que la dirección IP es dada por el plugin respectivo, CNI la reenvía al tiempo de ejecución del contenedor solicitado.


### 4.18. Pod-to-External World Communication
Para que una aplicación en contenedor que se ejecuta en __Pods__ dentro de un clúster de Kubernetes se pueda desplegar con éxito, se requiere accesibilidad desde el mundo exterior. 
Kubernetes permite la accesibilidad externa a través de __services__, construcciones complejas que encapsulan definiciones de reglas de red en __Nodes__ de clúster. 
Al exponer los __services__ al mundo externo con __kube-proxy__, las aplicaciones se hacen accesibles desde fuera del cluster a través de una IP virtual.


DUDAS:
* Plano de control, que es exactamente?
* etcd y hosts externos

## Chapter 5. Installing Kubernetes<a name="chapter5"></a>
## 5. Installing Kubernetes
## 5.1. Kubernetes Configuration
Los kubernetes pueden ser instalados usando diferentes configuraciones. 
A continuación se presentan brevemente los cuatro tipos principales de instalación:

* __Instalación de un Single-Node todo en uno:__
En esta configuración, todos los componentes maestros y trabajadores están instalados y funcionando en un solo __Node__. 
Aunque es útil para el aprendizaje, desarrollo y pruebas, no debe utilizarse en la producción. 
__Minikube__ es un ejemplo de ello.

* __Instalación de un Single-Node, etc., de un solo Master Node y de varios Worker Node:__
En esta configuración, tenemos un __Master Node__ único, que también ejecuta una instancia de un solo __Node etcd__. 
Múltiples __Workers Nodes__ están conectados al __Master Node__.

* __Instalación de un Single-Node etcd, multi-Master y multi-Worker:__
En esta configuración, tenemos __Multi-Master Nodes__ configurados en modo HA, pero tenemos una instancia de un solo __Node etcd__. __Multi-Worker Nodes__ están conectados a los __Master Nodes__.

* __Instalación de Multi-Node etcd, Multi-Master y Multi-Worker:__
En este modo, __etcd__ está configurado en modo HA agrupado, los __Master Nodes__ están todos configurados en modo HA, conectando a __Multi-Worker Nodes__. 

> __Esta es la configuración de producción más avanzada y recomendada.__


## 5.2. Infrastructure for Kubernetes Installation
Una vez que decidimos el tipo de instalación, también tenemos que tomar algunas decisiones relacionadas con la infraestructura:

* ¿Debemos establecer los k8s en metal desnudo, nube pública o nube privada?
* ¿Qué sistema operativo subyacente deberíamos utilizar? ¿Deberíamos elegir RHEL, CoreOS, CentOS, u otra cosa?
* ¿Qué solución de red deberíamos utilizar?
* Y así sucesivamente.


## 5.3. Localhost Installation
Estas son sólo algunas de las opciones de instalación del host local disponibles para desplegar clusters de Kubernetes de uno o varios __Nodes__ en nuestra estación de trabajo/portátil:

* __Minikube__ - cluster de Kubernetes locales de un __Single-Node__.
* __Docker Desktop__ - __Single-Node__ de cluster local de Kubernetes para Windows y Mac.
* __CDK en LXD__ - cluster local __Multi-Node__ con contenedores de LXD.

> Minikube es la forma preferida y recomendada para crear una configuración de Kubernetes todo en uno localmente.


## 5.4. On-Premise Installation
Los kubernetes pueden instalarse en las máquinas virtuales y en el metal desnudo.

* __VMs en las instalaciones:__
Los kubernetes pueden instalarse en las máquinas virtuales creadas a través de Vagrant, VMware vSphere, KVM u otra herramienta de gestión de la configuración (CM) junto con un software de hipervisor. Hay diferentes herramientas disponibles para automatizar la instalación, como __Ansible__ o __kubeadm__.
* __Bare Metal en las instalaciones:__
Los kubernetes pueden ser instalados en bare-metal, en RHEL, CoreOS, CentOS, Fedora, Ubuntu, etc.


## 5.5. Cloud Installation
kubernetes pueden ser instalados y administrados en casi cualquier entorno de nube:

* __Soluciones alojadas:__
Algunos de los proveedores que ofrecen soluciones alojadas para Kubernetes son:
    * Motor de Kubernetes de Google (GKE).
    * Servicio de Kubernetes Azules (AKS).
    * Servicio de Contenedores Elásticos del Amazonas para Kubernetes (EKS).
    * DigitalOcean Kubernetes.
    * OpenShift Dedicado.
    * Plataforma9.
    * IBM Cloud Kubernetes Service.

* __Soluciones Turnkey:__
Kubernetes gestionado, como por ejemplo:
    * Motor de Computación de Google (GCE).
    * Amazon AWS (AWS EC2).
    * Microsoft Azure (AKS).

* __Soluciones "llave en mano" en las instalaciones:__
Las Soluciones On-Premise instalan Kubernetes en nubes privadas internas seguras con sólo unos pocos comandos:
    * GKE On-Prem de Google Cloud.
    * IBM Cloud Private.
    * OpenShift Container Platform de Red Hat.


## 5.6. Kubernetes Installation Tools/Resources 
Algunas herramientas/recursos útiles disponibles:

* __kubeadm:__
Es una forma segura y recomendada de arrancar un cluster de Kubernetes de __Single-Node__ o __Multi-Node__.

* __kubespray:__
(antes conocido como __kargo__), podemos instalar clusters de Kubernetes en HA en AWS, GCE, Azure, OpenStack, o bare-metal. Está basado en __Ansible__, y está disponible en la mayoría de las distribuciones de Linux.

* __kops:__
Podemos crear, destruir, actualizar y mantener clusters de Kubernetes de producción y HA desde la línea de mando. También puede aprovisionar las máquinas. Actualmente, AWS está oficialmente respaldada. La compatibilidad con GCE está en fase beta, y VMware vSphere en fase alfa, y otras plataformas están previstas para el futuro.

* __kube-aws:__
Podemos crear, actualizar y destruir clusters de Kubernetes en AWS desde la línea de comandos. 



## Chapter 6. Minikube - A Local Single-Node Kubernetes Cluster<a name="chapter6"></a>
## 6. Minikube - A Local Single-Node Kubernetes Cluster
## 6.1. Requirements for Running Minikube
__Minikube__ está disponible para Linux, MacOS o Windows. Sin embargo, para aprovechar al máximo todas las características que ofrece Minikube, se debe instalar un Hipervisor de Tipo 2 en tu equipo local, para que funcione en conjunto con Minikube. 

__Minikube__ construye toda su infraestructura siempre y cuando el Hipervisor de Tipo 2 esté instalado en nuestra estación de trabajo. Minikube invoca el Hipervisor para crear una sola VM que luego alberga un clúster de Kubernetes de un solo nodo. Por lo tanto, necesitamos asegurarnos de que tenemos el hardware y el software necesario requerido por Minikube para construir su entorno. A continuación se describen los requisitos para ejecutar Minikube en nuestra estación de trabajo local:

* __kubectl:__
__kubectl__ es un binario utilizado para acceder y gestionar cualquier grupo de Kubernetes. Se instala por separado de Minikube.

* __Hipervisor de tipo 2:__
    * En Linux VirtualBox o KVM
    * En macOS VirtualBox, HyperKit o VMware Fusion
    * En Windows VirtualBox o Hyper-V

> NOTA: Minikube soporta una opción __--vm-driver=none__ que ejecuta los componentes de Kubernetes directamente en el sistema operativo del host y no dentro de una VM. Con esta opción se requiere una instalación __Docker__ y un sistema operativo Linux en la estación de trabajo local, pero no una instalación de hipervisor. Si utiliza __--vm-driver=none__, asegúrese de especificar una red de puente para Docker. De lo contrario, podría cambiar entre los reinicios de la red, causando la pérdida de conectividad a su clúster.

* La virtualización de VT-x/AMD-v debe estar habilitada en la estación de trabajo local en el BIOS.

* Conexión a Internet en la primera ejecución de Minikube - para descargar paquetes, dependencias, actualizaciones y sacar las imágenes necesarias para iniciar el cluster de Minikube Kubernetes. Las ejecuciones subsiguientes requerirán una conexión a Internet sólo cuando las nuevas imágenes Docker necesiten ser extraídas de un repositorio de contenedores o cuando las aplicaciones desplegadas en contenedores lo necesiten. Una vez que una imagen ha sido extraída puede ser reutilizada sin una conexión a Internet.



## Chapter 7. Accessing Minikube<a name="chapter7"></a>
## 7. Accessing Minikube
## 7.1. Accessing Minikube
Se puede acceder a cualquier grupo de Kubernetes que funcione bien a través de cualquiera de los siguientes métodos:

* Herramientas y scripts de la Interfaz de Línea de Comando (CLI).
* Interfaz de usuario basada en la web (Web UI) desde un navegador web.
* APIs de CLI o programáticamente.

Estos métodos son aplicables a todos los grupos de Kubernetes. 


## 7.2. Accessing Minikube: Command Line Interface (CLI)
__kubectl__ es el cliente de la Interfaz de Línea de Comando (CLI) de Kubernetes para gestionar los recursos y aplicaciones del cluster. Puede utilizarse de forma autónoma o como parte de scripts y herramientas de automatización. Una vez que se han configurado todas las credenciales y puntos de acceso al clúster necesarios para kubectl, se puede utilizar de forma remota desde cualquier lugar para acceder a un clúster. 

En capítulos posteriores, utilizaremos kubectl para desplegar aplicaciones, gestionar y configurar los recursos de Kubernetes.


## 7.3. Accessing Minikube: Web-based User Interface (Web UI)
El Dashboard de Kubernetes proporciona una Interfaz de Usuario basada en la Web (Web UI) para interactuar con un grupo de Kubernetes para gestionar recursos y aplicaciones en contenedores. En uno de los últimos capítulos, la utilizaremos para desplegar una aplicación en contenedor.


## 7.4. Accessing Minikube: APIs
Como sabemos, Kubernetes tiene el servidor API, y los operadores/usuarios se conectan a él desde el mundo externo para interactuar con el cluster. Usando tanto la CLI como la Web UI, podemos conectarnos al servidor API que se ejecuta en el nodo maestro para realizar diferentes operaciones. Podemos conectarnos directamente al servidor de la API utilizando sus puntos finales de la API y enviarle comandos, siempre y cuando podamos acceder al nodo maestro y tengamos las credenciales adecuadas.

A continuación, podemos ver una parte del espacio de la API HTTP de Kubernetes:

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/7.3_HTTPAPISpaceofKubernetes.png)

El espacio de la API HTTP de los gobernantes se puede dividir en tres grupos independientes:

* Grupo central (/api/v1)
Este grupo incluye objetos como Pods, Servicios, nodos, espacios de nombres, configmaps, secretos, etc.

* Grupo de nombres
Este grupo incluye objetos en formato /apis/$NAME/$VERSION. Estas diferentes versiones de la API implican diferentes niveles de estabilidad y soporte:
Nivel Alfa - puede ser eliminado en cualquier momento, sin previo aviso. Por ejemplo, /apis/batch/v2alpha1.
Nivel Beta - está bien probado, pero la semántica de los objetos puede cambiar de forma incompatible en una posterior versión beta o estable. Por ejemplo, /apis/certificados.k8s.io/v1beta1.
Nivel estable - aparece en el software publicado para muchas versiones posteriores. Por ejemplo, /apis/networking.k8s.io/v1.

* A nivel de todo el sistema
Este grupo consiste en puntos finales de la API de todo el sistema, como /healthz, /logs, /metrics, /ui, etc.
Podemos conectarnos a un servidor de la API directamente llamando a los respectivos puntos finales de la API o a través del CLI/Web UI.

A continuación veremos cómo podemos acceder al entorno de Minikube que hemos configurado en el capítulo anterior.


## 7.5. kubectl
__kubectl__ se instala generalmente antes de instalar el Minikube, pero también podemos instalarlo después. Una vez instalado, kubectl recibe su configuración automáticamente para el acceso al cluster de Kubernetes de Minikube. Sin embargo, en otras configuraciones de clúster de Kubernetes, es posible que necesitemos configurar los puntos de acceso al clúster y los certificados requeridos por kubectl para acceder al clúster.

Existen diferentes métodos que pueden utilizarse para instalar kubectl, que se mencionan en la documentación de Kubernetes. Para obtener los mejores resultados, se recomienda mantener kubectl en la misma versión que los Kubernetes dirigidos por Minikube - en el momento en que se escribió el curso la última versión estable era la v1.14.1. A continuación, veremos algunos pasos para instalarlo en sistemas Linux, MacOS y Windows.

## 7.6. Installing kubectl on Linux
Para instalar kubectl en Linux, siga las siguientes instrucciones:

Descargue el último binario estable de kubectl, hágalo ejecutable y muévalo al PATH:

> $ curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && chmod +x kubectl && sudo mv kubectl /usr/local/bin/

NOTA: Para descargar y configurar una versión específica de kubectl (como la v1.14.1), emita el siguiente comando:

> $ curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.14.1/bin/linux/amd64/kubectl && chmod +x kubectl && sudo mv kubectl /usr/local/bin/


## 7.7. Installing kubectl on macOS
Hay dos maneras de instalar kubectl en macOS: manualmente y usando el administrador de paquetes de Homebrew. A continuación, daremos instrucciones para ambos métodos.

Para instalar kubectl manualmente, descargue el último binario estable de kubectl, hágalo ejecutable y muévalo al PATH con el siguiente comando:

> $ curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/darwin/amd64/kubectl && chmod +x kubectl && sudo mv kubectl /usr/local/bin/

NOTA: Para descargar y configurar una versión específica de kubectl (como la v1.14.1), emita el siguiente comando:

> $ curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.14.1/bin/darwin/amd64/kubectl && chmod +x kubectl && sudo mv kubectl /usr/local/bin/

Para instalar kubectl con el administrador de paquetes de Homebrew, emita el siguiente comando:

$ brew install kubernetes-cli


## 7.8. kubectl Configuration File
Para acceder al clúster de Kubernetes, el cliente de kubectl necesita el punto final del nodo maestro y las credenciales adecuadas para poder interactuar con el servidor de la API que se ejecuta en el nodo maestro. Al iniciar Minikube, el proceso de inicio crea, por defecto, un archivo de configuración, config, dentro del directorio.kube (a menudo denominado archivo dot-kube-config), que reside en el directorio principal del usuario. El archivo de configuración tiene todos los detalles de conexión requeridos por kubectl. Por defecto, el binario de kubectl analiza este archivo para encontrar el punto final de conexión del nodo maestro, junto con las credenciales. Para ver los detalles de conexión, podemos ver el contenido del archivo ~/.kube/config (en Linux) o ejecutar el siguiente comando:

```yaml
$ kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /home/student/.minikube/ca.crt
    server: https://192.168.99.100:8443
  name: minikube
contexts:
- context:
    cluster: minikube
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /home/student/.minikube/client.crt
    client-key: /home/student/.minikube/client.key
``` 


Una vez instalado kubectl, podemos obtener información sobre el clúster de Minikube con el comando kubectl cluster-info: 

```
$ kubectl cluster-info
Kubernetes master is running at https://192.168.99.100:8443
KubeDNS is running at https://192.168.99.100:8443//api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
``` 

Para seguir depurando y diagnosticando los problemas de los racimos, usa el 'kubectl cluster-info dump'.

Puedes encontrar más detalles sobre las opciones de la línea de comandos de kubectl aquí.

Aunque para el clúster de Kubernetes instalado por Minikube el archivo ~/.kube/config se crea automáticamente, este no es el caso de los clústeres de Kubernetes instalados por otras herramientas. En otros casos, el archivo de configuración tiene que ser creado manualmente y a veces reconfigurado para adaptarse a varias configuraciones de red y de cliente/servidor.


## 7.9. Kubernetes Dashboard
Como se ha mencionado anteriormente, el Tablero de Kubernetes proporciona una interfaz de usuario basada en la web para la gestión del clúster de Kubernetes. Para acceder al Dashboard desde Minikube, podemos utilizar el comando minikube dashboard, que abre una nueva pestaña en nuestro navegador web, mostrando el Dashboard de los Kubernetes:

``` 
$ minikube dashboard
``` 

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/7.9_KubernetesDashboard.png)

NOTA: En caso de que el navegador no abra otra pestaña y no muestre el Tablero de Control como se esperaba, verifique la salida en su terminal ya que puede mostrar un enlace para el Tablero de Control (junto con algunos mensajes de error). Copie y pegue ese enlace en una nueva pestaña de su navegador. Dependiendo de las características de su terminal, es posible que sólo tenga que hacer clic o hacer clic con el botón derecho del ratón en el enlace para abrirlo directamente en el navegador. El enlace puede tener un aspecto similar:

```
http://127.0.0.1:37751/api/v1/namespaces/kube-system/services/http:kubernetes-dashboard:/proxy/
```

Lo más probable es que la única diferencia sea el número del PUERTO, que arriba es 37751. Su número de puerto puede ser diferente.

Después de un cierre de sesión/login o un reinicio de su estación de trabajo, el comportamiento normal debe ser esperado (donde el comando minikube dashboard abre directamente una nueva pestaña en su navegador mostrando el Dashboard).


## 7.10. The 'kubectl proxy' Command
Al emitir el comando proxy kubectl, kubectl se autentica con el servidor API en el nodo maestro y hace que el Dashboard esté disponible en una URL ligeramente diferente a la anterior, esta vez a través del puerto proxy 8001.

Primero, emitimos el comando kubectl proxy:

```
$ kubectl proxy
Starting to serve on 127.0.0.1:8001
```

Bloquea la terminal mientras el proxy esté funcionando. Con el proxy ejecutándose podemos acceder al Dashboard a través de la nueva URL (sólo tienes que hacer clic en ella abajo - debería funcionar en tu estación de trabajo). Una vez que detenemos el proxy (con CTRL + C) el Dashboard ya no es accesible.

```
http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy/#!/overview?namespace=default 
```

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/7.10_KubernetesDashboardovertheproxy.png)


## 7.11. APIs - with 'kubectl proxy'
Cuando se ejecuta el proxy de kubectl, podemos enviar solicitudes a la API a través del localhost en el puerto proxy 8001 (desde otra terminal, ya que el proxy bloquea la primera terminal):

```json
$ curl http://localhost:8001/
{
 "paths": [
   "/api",
   "/api/v1",
   "/apis",
   "/apis/apps",
   ......
   ......
   "/logs",
   "/metrics",
   "/openapi/v2",
   "/version"
 ]
}
```

Con la solicitud de rizo anterior, solicitamos todos los puntos finales de la API al servidor de la API. Al hacer clic en el enlace anterior (en el comando curl), se abrirá la misma salida de listado en una pestaña del navegador.

Podemos explorar todas las combinaciones de rutas con curl o en un navegador, por ejemplo:

```
http://localhost:8001/api/v1

http://localhost:8001/apis/apps/v1

http://localhost:8001/healthz

http://localhost:8001/metrics
```

## 7.12. APIs - without 'kubectl proxy'
Cuando no se utiliza el proxy kubectl, es necesario autenticarse en el servidor de la API cuando se envían las solicitudes de la API. Podemos autenticarnos proporcionando un token portador al emitir un rizo, o proporcionando un conjunto de claves y certificados.

Un token portador es un token de acceso que es generado por el servidor de autenticación (el servidor de la API en el nodo maestro) y devuelto al cliente. Utilizando ese token, el cliente puede conectarse de nuevo al servidor de la API de Kubernetes sin necesidad de proporcionar más detalles de autenticación y, a continuación, acceder a los recursos.

Consigue el token:

```
$ TOKEN=$(kubectl describe secret -n kube-system $(kubectl get secrets -n kube-system | grep default | cut -f1 -d ' ') | grep -E '^token' | cut -f2 -d':' | tr -d '\t' | tr -d " ")
```
Get the API server endpoint:

```
$ APISERVER=$(kubectl config view | grep https | cut -f 2- -d ":" | tr -d " ")
```

Confirm that the APISERVER stored the same IP as the Kubernetes master IP by issuing the following 2 commands and comparing their outputs:

```
$ echo $APISERVER
https://192.168.99.100:8443

$ kubectl cluster-info
Kubernetes master is running at https://192.168.99.100:8443 ...
```

Access the API server using the curl command, as shown below:

```json
$ curl $APISERVER --header "Authorization: Bearer $TOKEN" --insecure
{
 "paths": [
   "/api",
   "/api/v1",
   "/apis",
   "/apis/apps",
   ......
   ......
   "/logs",
   "/metrics",
   "/openapi/v2",
   "/version"
 ]
}
```

En lugar del token de acceso, podemos extraer el certificado de cliente, la clave de cliente y los datos de la autoridad de certificación del archivo .kube/config. Una vez extraídos, se codifican y luego se pasan con un comando curl para la autenticación. El nuevo comando curl es similar a:

```
$ curl $APISERVER --cert encoded-cert --key encoded-key --cacert encoded-ca
```



## Chapter 8. Kubernetes Building Blocks<a name="chapter8"></a>
## 8. Kubernetes Building Blocks
## 8.1. Kubernetes Object Model
Kubernetes tiene un modelo de objetos abundante, que representa diferentes entidades persistentes en el grupo de Kubernetes. Esas entidades describen:

* Qué aplicaciones contenedoras estamos ejecutando y en qué __Node__.
* Consumo de recursos de la aplicación
* Diferentes políticas adjuntas a las aplicaciones, como políticas de reinicio/actualización, tolerancia a fallos, etc.

Con cada objeto, declaramos nuestra sección __spec__. El sistema de Kubernetes gestiona la sección __status__ de los objetos, donde registra el estado real del objeto. En un momento dado, el Plano de Control de Kubernetes intenta hacer coincidir el estado real del objeto con el estado deseado del mismo.

Algunos ejemplos de objetos son __Pods__, __ReplicaSets__, __Deployments__, __Namespaces__, etc.

Cuando se crea un objeto, la sección de datos de configuración del objeto que se encuentra debajo del campo __spec__ debe enviarse al API Server de Kubernetes. La sección __spec__ describe el estado deseado, junto con alguna información básica, como el nombre del objeto. La solicitud de la API para crear un objeto debe tener la sección __spec__, así como otros detalles. Aunque el __API Server__ acepta archivos de definición de objetos en formato JSON, la mayoría de las veces proporcionamos dichos archivos en formato YAML, que se convierten por __kubectl__ en una carga útil JSON y se envían al __API Server__.

Ejemplo de un objecto en formato YAML:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.11
        ports:
        - containerPort: 80
```

Analizando el código del objecto los campos son los siguientes:
* El campo __apiVersion__ es el primer campo obligatorio, y especifica el API endpoint en el Api Server al que queremos conectarnos; debe coincidir con una versión existente para el tipo de objeto definido. 
* El segundo campo obligatorio es __kind__, que especifica el tipo de objeto - en nuestro caso es __Deployment__, pero puede ser __Pod__, __Replicaset__, __Namespace__, __Service__, etc. 
* El tercer campo requerido, __metadata__, contiene la información básica del objeto, como el __name__, __lables__, __namespace__, etc. Nuestro ejemplo muestra dos campos __spec__ (__spec__ y __spec.template.spec__). 
* El cuarto campo __spec__, requerido, marca el comienzo del bloque que define el estado deseado del objeto __Deployment__. En nuestro ejemplo, queremos asegurarnos de que 3 __Pods__ están funcionando en un momento dado (__replicas__). Los __Pods__ se crean utilizando la __template__ de __Pods__ definida en __spec.template__. Un objeto anidado, como el __Pod__ que forma parte de un __Deployment__, retiene sus __metadata__ y su __spec__ y pierde la __apiVersión__ y el __kind__ - ambos son reemplazados por la __template__. En __spec.template.spec__, definimos el estado deseado del __Pod__. Nuestro __Pod__ crea un único contenedor que ejecuta la imagen __nginx:1.15.11__ del Docker Hub.

Una vez creado el objeto de despliegue, el sistema de Kubernetes adjunta el campo de __status__ al objeto.


## 8.2. Pods
Un __Pod__ es el objeto más pequeño y simple de los Kubernetes. Es la unidad de despliegue en los Kubernetes, que representa una única instancia de la aplicación. Un __Pod__ es una colección lógica de __uno o más contenedores__, que:

* Están programados juntos en el mismo host con el __Pod__.
* Compartir el mismo network __namespace__.
* Tener acceso a montar el mismo external storage (__volumes__).

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/8.2_Pods.png)

Los __Pods__ son efímeros por naturaleza y no tienen capacidad de auto-curarse a sí mismos. Por eso se usan con __controllers__ que se encargan de la replicación de los __Pods__, la tolerancia a los fallos, la autocuración, etc. Ejemplos de __controllers__ son: __Deployments__, __ReplicaSet__, __ReplicationController__, etc.

Ejemplo de un objeto __Pod__ en formato YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.15.11
    ports:
    - containerPort: 80
```

El campo __apiVersion__ debe especificar __v1__ para la definición del objeto __Pod__. El segundo campo obligatorio es el __kind__ que especifica el tipo de objeto __Pod__. El tercer campo requerido, __metadata__, contiene el __name__ y el __label__ del objeto. El cuarto campo requerido, __spec__, marca el comienzo del bloque que define el estado deseado del objeto __Pod__, también llamado __PodSpec__. Nuestro Pod crea un único contenedor que ejecuta la imagen __nginx:1.15.11__ de Docker Hub.


## 8.3. Labels
Las __Labels__ son pares de __clave-valor__ unidos a objetos kubernetes (por ejemplo, __Pods__, __ReplicaSet__). Las __labels__ se utilizan para organizar y seleccionar un subconjunto de objetos, según los requisitos establecidos. Muchos objetos pueden tener la misma o las mismas __labels__. Las __labels__ no proporcionan unicidad a los objetos. Los __controllers__ utilizan las __labels__ para agrupar lógicamente los objetos desacoplados, en lugar de utilizar los nombres o las identificaciones de los objetos.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/8.3_Labels.png)

En la imagen de arriba, hemos usado dos __Label__ keys: app y env. Basándonos en nuestros requisitos, hemos dado diferentes valores a nuestros cuatro __Pods__. La __label__ env=dev selecciona y agrupa lógicamente los dos primeros __Pods__, mientras que la __label__ app=frontend selecciona y agrupa lógicamente los dos __Pods__ de la izquierda. Podemos seleccionar uno de los cuatro __Pods__  seleccionando dos Etiquetas: app=frontend y env=qa.


## 8.4. Label Selectors
Los __controllers__ utilizan __Label Selectors__ para seleccionar un subconjunto de objetos. Kubernetes soporta dos tipos de __Selectors__:

* __Equality-Based Selectors__ (basados en igualdad):
Permiten filtrar los objetos en función de las key-value de las __Labels__. El emparejamiento se logra usando los operadores __=__, __==__ (iguales, usados indistintamente), o __!=__ (no iguales). Por ejemplo, con __env==dev__ o __env=dev__ estamos seleccionando los objetos en los que la clave __Label__ key-value, env-dev. 
* __Set-Based Selectors__ (basados en conjunto):
Permiten filtrar los objetos en base a un conjunto de valores. Podemos utilizar los operadores __in__, __notin__ para __Label values__, y __exist/does not exist__ para __Label keys__. Por ejemplo, con __env in (dev,qa)__ seleccionamos objetos en los que __env__ esté como __dev o qa__; con __!app__ seleccionamos objetos sin Label key __app__.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/8.4_Selectors.png)


## 8.5. ReplicationControllers
Aunque ya no es un método recomendado, un __ReplicationController__ es un __controller__ que asegura que un número específico de __replicas__ de un __Pod__ esté funcionando en un momento dado. Generalmente, no desplegamos un __Pod__ de forma independiente, ya que no podría reiniciarse por sí mismo si se terminara por error. El método recomendado es utilizar algún tipo de contolador de replicación para crear y gestionar los __Pods__. 

El controlador por defecto es un __Deployment__ que configura un __ReplicaSet__ para gestionar el ciclo de vida de los __Pods__.


## 8.6. ReplicaSets I
Un __ReplicaSet__ es la próxima generación de __ReplicationController__.

> Los __ReplicaSets__ soportan tanto __selectors__ basados en igualdad como en conjuntos, mientras que los __ReplicationControllers__ sólo soportan selectores basados en igualdad. Actualmente, esta es la única diferencia.

Con la ayuda del __ReplicaSet__, podemos escalar el número de __Pods__ que ejecutan una imagen específica de la aplicación del contenedor. La escalada puede ser realizada manualmente o a través del uso de un __autoscaler__.

A continuación, pueden ver una representación gráfica de un __ReplicaSet__, donde hemos establecido el número de __replicas__ a 3 para un __Pod__.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/8.6_ReplicaSet.png)


## 8.7. ReplicaSets II
Continuando con el mismo ejemplo y supongamos que una de los __Pods__ se ve obligado a terminar (debido a la insuficiencia de recursos, el tiempo de espera, etc.), y el estado actual ya no se corresponde con el estado deseado.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/8.7_ReplicaSet.png)


## 8.8. ReplicaSets III
El __ReplicaSet__ detectará que el estado actual ya no coincide con el estado deseado. El __ReplicaSet__ creará un __Pod__ adicional, asegurando así que el estado actual coincide con el estado deseado.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/8.8_ReplicaSet.png)

Los __ReplicaSets__ pueden ser usados independientemente como __controllers__ de __Pod__, pero sólo ofrecen un conjunto limitado de características. 

> Un conjunto de características complementarias son proporcionadas por __Deployments__, los controladores recomendados para la orquestación de __Pods__. __Deployments__ gestiona la creación, eliminación y actualización de los __Pods__. Un __Deployment__ crea automáticamente un __ReplicaSet__, que luego crea un __Pod__. No hay necesidad de administrar los __ReplicaSets__ y los __Pods__ por separado, el __Deployment__ los administrará en nuestro nombre.


## 8.9. Deployments I
Los __Deployments__ proporcionan actualizaciones declarativas de los __Pods__ y los __ReplicaSets__. El __DeploymentController__ es parte del administrador de controladores del __Master Node__, y asegura que el estado actual siempre coincida con el estado deseado. Permite actualizaciones y degradaciones de aplicaciones sin problemas a través de despliegues y retrocesos, y gestiona directamente sus __ReplicaSets__ para el escalado de las aplicaciones. 

En el siguiente ejemplo, un nuevo __Deployment__ crea __ReplicaSet__ A que luego crea 3 __Pods__, con cada __Pods Template__ configurada para ejecutar una imagen de contenedor __nginx:1.7.9__. En este caso, el __ReplicaSet__ A se asocia con __nginx:1.7.9__ representando un estado del __Deployment__. Este estado particular se registra como la Revisión 1.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/8.9_Deployment.png)


## 8.10. Deployments II
Ahora, en el __Deployment__, cambiamos la __Pdos Template__ y actualizamos la imagen del contenedor de __nginx:1.7.9__ a __nginx:1.9.1__. El __Deployment__ dispara un nuevo __ReplicaSet__ B para la nueva imagen del contenedor versionada 1.9.1 y esta asociación representa un nuevo estado registrado del __Deployment__, Revisión 2. Transicionará los dos __ReplicaSets__, desde el __ReplicaSet__ A con 3 __Pods__ versionados 1.7.9 al nuevo __ReplicaSet__ B con 3 nuevos __Pods__ versionados 1.9.1, o desde la Revisión 1 a la Revisión 2, es una actualización continua del __Deployment__. 

Un rolling update se activa cuando actualizamos __Pods Template__ para un despliegue. Operaciones como escalar o etiquetar el despliegue no activan una rolling update, por lo tanto no cambian el número de revisión.

Una vez que se haya completado el rolling update, el __Deployment__ mostrará tanto las réplicas de los conjuntos A y B, donde A se escala a 0 __Pods__, y B se escala a 3 __Pods__. Así es como el __Deployment__ registra sus ajustes de configuración de estado previo, como Revisiones. 

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/8.10_Deployment.png)


## 8.11. Deployments III
Una vez que el __ReplicaSet__ B y sus 3 __Pods__ versionados 1.9.1 están listos, el __Deployment__ comienza a gestionarlos activamente. Sin embargo, el __Deployment__ mantiene sus estados de configuración previos guardados como Revisiones que juegan un factor clave en la capacidad de __rollback__ del __Deployment__ - regresando a un estado de configuración previo conocido. En nuestro ejemplo, si el rendimiento del nuevo nginx:1.9.1 no es satisfactorio, el __Deployment__ puede ser retrocedido a una Revisión previa, en este caso de la Revisión 2 a la Revisión 1 ejecutando nginx:1.7.9. 

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/8.11_Deployment.png)


## 8.12. Namespaces 
Si varios usuarios y equipos utilizan el mismo clúster de Kubernetes podemos dividir el clúster en subclústeres virtuales utilizando __Namespaces__. Los nombres de los recursos/objetos creados dentro de un __Namespace__ son únicos, pero no en los __Namespaces__ del cluster.

Para listar todos los __Namespaces__, podemos ejecutar el siguiente comando:

```
$ kubectl get namespaces
NAME              STATUS       AGE
default           Active       11h
kube-node-lease   Active       11h
kube-public       Active       11h
kube-system       Active       11h
```

Generalmente, Kubernetes crea cuatro __Namespaces__ por defecto: 
* __kube-system:__
Contiene los objetos creados por el sistema, principalmente los agentes del plano de control.
* __kube-public:__
Es un __namespace__ inseguro y legible por cualquiera, utilizado para la exposición de información pública no sensible sobre el cluster.
* __kube-node-lease:__
*******************************************************************
* __default:__
Contiene los objetos y recursos creados por los administradores y desarrolladores.

El último espacio de nombres es kube-node-lease, que contiene objetos de nodo de alquiler utilizados para los datos de los latidos del nodo. Sin embargo, una buena práctica es crear más espacios de nombres para virtualizar el grupo para los usuarios y los equipos de desarrollo.

Con las __Resource Quotas__, podemos dividir los recursos del clúster dentro de __Namespaces__.

## Chapter 9. Authentication, Authorization, Admission Control<a name="chapter9"></a>
## 9. Authentication, Authorization, Admission Control
Cada solicitud de API que llega al __API Server__ tiene que pasar por tres etapas diferentes antes de ser aceptada por el servidor y actuar en consecuencia:
* Authentication.
* Authorization
* Admission Control stages of Kubernetes API requests.


## 9.1. Authentication, Authorization, and Admission Control - Overview
Para acceder y administrar cualquier recurso u objeto de Kubernetes en el clúster, necesitamos acceder a un punto final de la API específico en el servidor de la API. Cada solicitud de acceso pasa por las tres etapas siguientes:

* Authentication:
Inicia la sesión de un usuario.
* Authorization:
Autoriza las solicitudes de API añadidas por el usuario conectado.
* Admission Control:
Módulos de software que pueden modificar o rechazar las solicitudes en base a algunas comprobaciones adicionales, como una __Quota__ preestablecida. 

La siguiente imagen muestra las etapas anteriores:

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/9.1_AccesingApi.png)

## 9.2. Authentication I
Kubernetes no tiene un objeto llamado usuario, ni almacena nombres de usuario u otros detalles relacionados en su almacén de objetos. Sin embargo, incluso sin eso, los Kubernetes pueden utilizar los nombres de usuario para el control de acceso y el registro de solicitudes, que exploraremos en este capítulo.

Kubernetes tiene dos tipos de usuarios:

* Usuarios normales
Se gestionan fuera del clúster de Kubernetes a través de servicios independientes como certificados de usuario/cliente, un archivo con nombres de usuario/contraseñas, cuentas de Google, etc.
* Cuentas de servicio
Con los usuarios de la Cuenta de Servicio, los procesos del grupo se comunican con el servidor de la API para realizar diferentes operaciones. La mayoría de los usuarios de cuentas de servicio se crean automáticamente a través del servidor API, pero también pueden crearse manualmente. Los usuarios de cuentas de servicio están vinculados a un espacio de nombres determinado y montan las credenciales respectivas para comunicarse con el servidor de la API como secretos.

Si se configuran correctamente, los Gobernantes también pueden admitir solicitudes anónimas, junto con las solicitudes de los Usuarios Normales y las Cuentas de Servicio. También se admite la suplantación de identidad de un usuario para que éste pueda actuar como otro usuario, una función útil para los administradores a la hora de solucionar problemas con las políticas de autorización.


## 9.3. Authentication II
Para la autenticación, Kubernetes utiliza diferentes módulos de autenticación:

* Certificados de cliente
Para habilitar la autenticación de certificados de cliente, necesitamos hacer referencia a un archivo que contenga una o más autoridades de certificación pasando la opción --client-ca-file=SOMEFILE al servidor de la API. Las autoridades de certificación mencionadas en el archivo validarían los certificados de cliente presentados al servidor de la API. Al final de este capítulo también se encuentra disponible un vídeo de demostración que cubre este tema.
* Archivo de tokens estático
Podemos pasar un archivo que contenga tokens portadores predefinidos con la opción --token-auth-file=SOMEFILE al servidor de la API. Actualmente, estos tokens durarían indefinidamente, y no pueden ser cambiados sin reiniciar el servidor de la API.
* Tokens de arranque
Esta característica se encuentra actualmente en estado beta y se utiliza principalmente para el bootstrapping de un nuevo grupo de Kubernetes.
* Archivo de contraseñas estáticas
Es similar al Archivo de Token Estático. Podemos pasar un archivo que contenga los detalles básicos de autenticación con la opción --basic-auth-file=SOMEFILE. Estas credenciales durarían indefinidamente, y las contraseñas no pueden ser cambiadas sin reiniciar el servidor de la API.
* Fichas de cuentas de servicio
Se trata de un autentificador habilitado automáticamente que utiliza fichas al portador firmadas para verificar las solicitudes. Estos tokens se adjuntan a los Pods mediante el Controlador de Admisión de Cuentas de Servicios, que permite a los procesos del grupo hablar con el servidor de la API.
* Tokens de conexión OpenID
OpenID Connect nos ayuda a conectar con proveedores de OAuth2, como Azure Active Directory, Salesforce, Google, etc., para descargar la autenticación a servicios externos.
* Autenticación de Token Webhook
Con la autenticación basada en Webhook, la verificación de los tokens portadores puede ser descargada a un servicio remoto.
* Autenticación del proxy
Si queremos programar una lógica de autenticación adicional, podemos usar un proxy de autenticación.

Podemos habilitar múltiples autenticadores, y el primer módulo para autenticar con éxito la solicitud provoca un cortocircuito en la evaluación. Para tener éxito, debemos habilitar al menos dos métodos: el autentificador de tokens de la cuenta de servicio y uno de los autentificadores de usuario.


## 9.4. Authorization I
Después de una autenticación exitosa, los usuarios pueden enviar las solicitudes de la API para realizar diferentes operaciones. A continuación, esas solicitudes de API son autorizadas por los gobernantes mediante diversos módulos de autorización.

Algunos de los atributos de las solicitudes de la API que son revisados por los Kubernetes incluyen usuario, grupo, extra, recurso o espacio de nombres, por nombrar algunos. A continuación, estos atributos se evalúan en función de las políticas. Si la evaluación es satisfactoria, entonces la solicitud será permitida, de lo contrario será denegada. De manera similar al paso de autenticación, la autorización tiene múltiples módulos/autorizadores. Se puede configurar más de un módulo para un grupo de Kubernetes, y cada módulo se comprueba en secuencia. Si algún autorizador aprueba o rechaza una solicitud, entonces esa decisión se devuelve inmediatamente.

A continuación, hablaremos de los autorizadores que son apoyados por los Kubernetes.


## 9.5. Authorization II
Módulos de autorización (Parte 1):

* Autorizador de nodos
La autorización de nodos es un modo de autorización con fines especiales que autoriza específicamente las solicitudes de API realizadas por kubelets. Autoriza las operaciones de lectura de kubelets para servicios, puntos finales, nodos, etc., y escribe operaciones para nodos, pods, eventos, etc. Para más detalles, por favor revise la documentación de Kubernetes.

* Autorizador del Control de Acceso Basado en Atributos (ABAC)
Con el autorizador ABAC, Kubernetes concede acceso a las solicitudes de API, que combinan políticas con atributos. En el siguiente ejemplo, el usuario estudiante sólo puede leer Pods en el espacio de nombres lfs158.

```json
{
  "apiVersion": "abac.authorization.kubernetes.io/v1beta1",
  "kind": "Policy",
  "spec": {
    "user": "student",
    "namespace": "lfs158",
    "resource": "pods",
    "readonly": true
  }
}
```
Para habilitar el autorizador de ABAC, necesitaríamos iniciar el servidor de la API con la opción --authorization-mode=ABAC. También necesitaríamos especificar la política de autorización con --authorization-policy-file=PolicyFile.json. Para obtener más detalles, consulte la documentación de Kubernetes.

* Autorizador de Webhook
Con el autorizador Webhook, Kubernetes puede ofrecer decisiones de autorización a algunos servicios de terceros, que se devolverían verdaderas para una autorización exitosa, y falsas para un fracaso. Para habilitar el autorizador Webhook, necesitamos iniciar el servidor API con la opción --authorization-webhook-config-file=SOME_FILENAME, donde SOME_FILENAME es la configuración del servicio de autorización remota. Para obtener más detalles, consulte la documentación de Kubernetes.

## 9.6. Authorization III
Módulos de autorización (Parte 2):

* Autorizador del control de acceso basado en funciones (RBAC)
En general, con el RBAC podemos regular el acceso a los recursos en base a los roles de los usuarios individuales. En los Kubernetes, podemos tener diferentes roles que pueden ser adjuntados a temas como usuarios, cuentas de servicio, etc. Al crear los roles, restringimos el acceso a los recursos mediante operaciones específicas, como crear, obtener, actualizar, parchear, etc. Estas operaciones se denominan verbos.

En RBAC, podemos crear dos tipos de roles:

    * Rol
      Con Role, podemos conceder acceso a los recursos de un espacio de nombres específico.

    * ClusterRole
      La función de grupo puede utilizarse para conceder los mismos permisos que la función, pero su ámbito de aplicación abarca todo el grupo.

      En este curso, nos centraremos en el primer tipo, el Rol. A continuación encontrarán un ejemplo:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: lfs158
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]

```
Como pueden ver, crea un papel de lector de pods, que sólo tiene acceso a leer los Pods de lfs158 Namespace. Una vez creado el rol, podemos vincular a los usuarios con RoleBinding.

Hay dos tipos de RoleBindings:

__RoleBinding__
Nos permite vincular a los usuarios al mismo espacio de nombres que un Rol. También podríamos referirnos a un Rol del Cluster en RoleBinding, lo cual otorgaría permisos a los recursos del espacio de nombres definidos en el Rol del Cluster dentro del espacio de nombres de RoleBinding.

__ClusterRoleBinding__
Nos permite conceder acceso a los recursos a nivel de grupo y a todos los Espacios de Nombres.

En este curso, nos centraremos en el primer tipo, RoleBinding. A continuación, encontrarán un ejemplo:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: pod-read-access
  namespace: lfs158
subjects:
- kind: User
  name: student
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io

```
Como pueden ver, da acceso al usuario estudiante para leer los Pods de lfs158 Namespace.

Para habilitar el autorizador RBAC, necesitaríamos iniciar el servidor de la API con la opción --authorization-mode=RBAC. Con el autorizador RBAC, configuramos dinámicamente las políticas. Para obtener más detalles, consulte la documentación de Kubernetes.


## 9.7.Admission Control
El control de admisión se utiliza para especificar las políticas de control de acceso granular, que incluyen la autorización de contenedores privilegiados, la comprobación de la cuota de recursos, etc. Forzamos estas políticas usando diferentes controladores de admisión, como ResourceQuota, DefaultStorageClass, AlwaysPullImages, etc. Sólo entran en vigor después de que las solicitudes de la API son autenticadas y autorizadas.

Para utilizar los controles de admisión, debemos iniciar el servidor de la API de Kubernetes con los plugins --enable-admission-plugins, que toma una lista ordenada y delimitada por comas de los nombres de los controladores:

```yaml
--enable-admission-plugins=NamespaceLifecycle,ResourceQuota,PodSecurityPolicy,DefaultStorageClass
```

Kubernetes tiene algunos controladores de admisión habilitados por defecto. Para más detalles, revise la documentación de Kubernetes.


## 9.8. Authentication and Authorization Exercise Guide
Esta guía de ejercicios asume el siguiente entorno, que por defecto utiliza el certificado y la clave de /var/lib/minikube/certs/, y el modo RBAC para la autorización:

            Minikube v1.0.1
            Kubernetes v1.14.1
            Docker 18.06.3-ce

Esta guía de ejercicios se puede utilizar junto con el vídeo de demostración que sigue en la página siguiente y se ha actualizado para el entorno mencionado anteriormente, mientras que el vídeo presenta una distribución de Minikube más antigua con Kubernetes v1.9. 

Iniciar Minikube:

```
$ minikube start
```

Ver el contenido del archivo de configuración del cliente kubectl, observando el único minicubo de contexto y el único minicubo de usuario, creado por defecto:

```yaml
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority: /home/student/.minikube/ca.crt
    server: https://192.168.99.100:8443
  name: minikube
contexts:
- context:
    cluster: minikube
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /home/student/.minikube/client.crt
    client-key: /home/student/.minikube/client.key
```

Create lfs158 namespace:

```
$ kubectl create namespace lfs158

namespace/lfs158 created
```

Create rbac directory and cd into it:

```
$ mkdir rbac

$ cd rbac/
```

Create a private key for the student user with openssl tool, then create a certificate signing request for the student user with openssl tool:

```
~/rbac$ openssl genrsa -out student.key 2048

Generating RSA private key, 2048 bit long modulus (2 primes)
.................................................+++++
.........................+++++
e is 65537 (0x010001)

~/rbac$ openssl req -new -key student.key -out student.csr -subj "/CN=student/O=learner"
```

Create a YAML configuration file for a certificate signing request object, and save it with a blank value for the request field: 

```yaml
~/rbac$ vim signing-request.yaml

apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: student-csr
spec:
  groups:
  - system:authenticated
  request: <assign encoded value from next cat command>
  usages:
  - digital signature
  - key encipherment
  - client auth
```

View the certificate, encode it in base64, and assign it to the request field in the signing-request.yaml file:

```yaml
~/rbac$ cat student.csr | base64 | tr -d '\n'

LS0tLS1CRUd...1QtLS0tLQo=

~/rbac$ vim signing-request.yaml

apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: student-csr
spec:
  groups:
  - system:authenticated
  request: LS0tLS1CRUd...1QtLS0tLQo=
  usages:
  - digital signature
  - key encipherment
  - client auth
```

Create the certificate signing request object, then list the certificate signing request objects. It shows a pending state:

```
~/rbac$ kubectl create -f signing-request.yaml

certificatesigningrequest.certificates.k8s.io/student-csr created

~/rbac$ kubectl get csr

NAME          AGE   REQUESTOR       CONDITION
student-csr   27s   minikube-user   Pending
```

Approve the certificate signing request object, then list the certificate signing request objects again. It shows both approved and issued states:

```
~/rbac$ kubectl certificate approve student-csr

certificatesigningrequest.certificates.k8s.io/student-csr approved

~/rbac$ kubectl get csr

NAME          AGE   REQUESTOR       CONDITION
student-csr   77s   minikube-user   Approved,Issued
```

Extract the approved certificate from the certificate signing request, decode it with base64 and save it as a certificate file. Then view the certificate in the newly created certificate file:

```
~/rbac$ kubectl get csr student-csr -o jsonpath='{.status.certificate}' | base64 --decode > student.crt

~/rbac$ cat student.crt

-----BEGIN CERTIFICATE-----
MIIDGzCCA...
...
...NOZRRZBVunTjK7A==
-----END CERTIFICATE-----
```

Configure the student user's credentials by assigning the key and certificate: 

```
~/rbac$ kubectl config set-credentials student --client-certificate=student.crt --client-key=student.key

User "student" set.
```

Create a new context entry in the kubectl client's configuration file for the student user, associated with the lfs158 namespace in the minikube cluster:

```
~/rbac$ kubectl config set-context student-context --cluster=minikube --namespace=lfs158 --user=student

Context "student-context" created.
```

View the contents of the kubectl client's configuration file again, observing the new context entry student-context, and the new user entry student:

```
~/rbac$ kubectl config view
```

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /home/student/.minikube/ca.crt
    server: https://192.168.99.100:8443
  name: minikube
contexts:
- context:
    cluster: minikube
    user: minikube
  name: minikube
- context:
    cluster: minikube
    namespace: lfs158
    user: student
  name: student-context
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /home/student/.minikube/client.crt
    client-key: /home/student/.minikube/client.key
- name: student
  user:
    client-certificate: /home/student/rbac/student.crt
    client-key: /home/student/rbac/student.key
```

While in the default minikube context, create a new deployment in the lfs158 namespace:

```
~/rbac$ kubectl -n lfs158 create deployment nginx --image=nginx:alpine

deployment.apps/nginx created
```

From the new context student-context try to list pods. The attempt fails because the student user has no permissions configured for the student-context:

```
~/rbac$ kubectl --context=student-context get pods

Error from server (Forbidden): pods is forbidden: User "student" cannot list resource "pods" in API group "" in the namespace "lfs158"
```

The following steps will assign a limited set of permissions to the student user in the student-context. 

Create a YAML configuration file for a pod-reader role object, which allows only get, watch, list actions in the lfs158 namespace against pod objects. Then create the role object and list it from the default minikube context, but from the lfs158 namespace:

```
~/rbac$ vim role.yaml
```

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: lfs158
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

```
~/rbac$ kubectl create -f role.yaml

role.rbac.authorization.k8s.io/pod-reader created

~/rbac$ kubectl -n lfs158 get roles

NAME         AGE
pod-reader   57s
```

Create a YAML configuration file for a rolebinding object, which assigns the permissions of the pod-reader role to the student user. Then create the rolebinding object and list it from the default minikube context, but from the lfs158 namespace:

```
~/rbac$ vim rolebinding.yaml
```

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-read-access
  namespace: lfs158
subjects:
- kind: User
  name: student
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

```
~/rbac$ kubectl create -f rolebinding.yaml 

rolebinding.rbac.authorization.k8s.io/pod-read-access created

~/rbac$ kubectl -n lfs158 get rolebindings

NAME              AGE
pod-read-access   23s
```

Now that we have assigned permissions to the student user, we can successfully list pods from the new context student-context.

```
~/rbac$ kubectl --context=student-context get pods

NAME                    READY   STATUS    RESTARTS   AGE
nginx-77595c695-f2xmd   1/1     Running   0          7m41s
```


## Chapter 10. Services<a name="chapter10"></a>
## 10. Services
Aunque la arquitectura impulsada por los microservicios tiene por objeto desacoplar los componentes de una aplicación, los microservicios siguen necesitando agentes para vincularlos o agruparlos lógicamente y balancear la carga a los que forman parte de ese conjunto lógico.

En este capítulo, aprenderemos sobre los __Services__, utilizados para agrupar __Pods__ para proporcionar puntos de acceso comunes del mundo exterior a las aplicaciones en contenedores. Aprenderemos sobre el demonio __kube-proxy__, que se ejecuta en cada __Worker Node__ para proporcionar acceso a los servicios. También hablaremos del descubrimiento de servicios y de los tipos de servicios, que deciden el alcance del acceso a un servicio.


## 10.1. Connecting Users to Pods
Para acceder a la aplicación, un usuario/cliente debe conectarse a los __Pods__. Como los __Pods__ son de naturaleza efímera, los recursos como las direcciones IP que se le asignan no pueden ser estáticos. Los __Pods__ pueden ser terminados abruptamente o reprogramados en base a los requerimientos existentes.

Tomemos, por ejemplo, un escenario en el que un usuario/cliente se conecta a un __Pod__ utilizando su dirección IP.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/10.1_AccessPod_A.png)

Inesperadamente, el Pod al que está conectado el usuario/cliente se termina, y un nuevo __Pod__ es creado por el controlador. El nuevo __Pod__ tendrá una nueva dirección IP, que no será conocida automáticamente por el usuario/cliente del __Pod__ anterior.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/10.1_AccessPod_B.png)

Para superar esta situación, Kubernetes proporciona una abstracción de nivel superior llamada __Services__, que agrupa lógicamente los __Pods__ y define una política para acceder a ellos. Esta agrupación se realiza a través de __Labels__ y __Selectors__.


## 10.2. Services
En la siguiente representación gráfica, __app__ es la __Label key__, __frontend__ y __db__ son los __Label values__ para las diferentes vainas.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/10.2_Services_A.png)

Usando los selectores __app==frontend__ y __app==db__, agrupamos los __Pods__ en dos conjuntos lógicos: uno con 3 Pods, y otro con un solo Pod.

Asignamos un nombre a la agrupación lógica, llamada __Service__. En nuestro ejemplo, creamos dos Servicios, __frontend-svc__, y __db-svc__, y tienen los Selectors __app==frontend__ y __app==db__, respectivamente.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/10.2_Services_B.png)

Los servicios pueden exponer __Pods__ individuales, __ReplicaSets__, __Deployments__, __DaemonSets__ y __StatefulSets__.


## 10.3. Service Object Example
Un servicio se define como en el siguiente fichero:

```yaml
kind: Service
apiVersion: v1
metadata:
  name: frontend-svc
spec:
  selector:
    app: frontend
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5000
```

En este ejemplo, estamos creando un __Service__ de __frontend-svc__ seleccionando todos los __Pods__ que tienen el Label _key=app_ establecida en __value=frontend__. Por defecto, cada __Service__ recibe una dirección IP enrutada sólo dentro del clúster, conocida como __ClusterIP__. En nuestro ejemplo, tenemos 172.17.0.4 y 172.17.0.5 como ClusterIPs asignados a nuestros Servicios __frontend-svc__ y __db-svc__, respectivamente. 

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/10.3_AccessingThePodsUsingServiceObject.png)

El usuario/cliente se conecta ahora a un servicio a través de su __ClusterIP__, que reenvía el tráfico a uno de los Pods adjuntos a él. Un __Service__ provee balanceo de carga por defecto mientras selecciona los __Pods__ para el reenvío de tráfico.

Mientras que el __Service__ reenvía el tráfico a los __Pods__, podemos seleccionar el __targetPort__ en el __Pod__ que recibe el tráfico. En nuestro ejemplo, el __Service__ de __frontend-svc__ recibe las solicitudes del usuario/cliente en el puerto 80 y luego reenvía estas solicitudes a uno de los __Pods__ adjuntos en el __targetPort__ 5000. Si el __targetPort__ no está definido explícitamente, entonces el tráfico será reenviado a los __Pods__ en el puerto en el que el __Service__ recibe el tráfico.

Un conjunto lógico de la dirección IP de un Pod, junto con el __targetPort__ se denomina __Service endpoint__. En nuestro ejemplo, el __frontend-svc Service__  tiene 3 endpoints: 10.0.1.3:5000, 10.0.1.4:5000, y 10.0.1.5:5000. Los __endpoints__ son creados y gestionados automáticamente por el __Service__, no por el administrador del clúster de Kubernetes. 


## 10.4. kube-proxy
Todos los __Worker Node__ ejecutan un demonio llamado __kube-proxy__, que vigila el __API Server__ en el __Master Node__ para la adición y eliminación de __Services__ y __endpoints__. En el ejemplo siguiente, para cada nuevo __Service__, en cada __Node__, el __kube-proxy__ configura las reglas de iptables para capturar el tráfico para su __ClusterIP__ y lo reenvía a uno de los __Service's endpoints__. Por lo tanto, cualquier Node puede recibir el tráfico externo y luego enrutarlo internamente en el cluster en base a las reglas iptables. Cuando se elimina el __Service__, el __kube-proxy__ elimina las correspondientes reglas iptables en todos los Nodes también.


![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/10.4_kube-proxyServicesEndpoints.png)


## 10.5. Service Discovery
Como los __Services__ son el principal modo de comunicación en K8s, necesitamos una forma de descubrirlos en tiempo de ejecución. Kubernetes admite dos métodos para descubrir los __Services__:

* __Environment Variables:__
Tan pronto como el __Pod__ se inicia en cualquier Worker Node, el demonio __kubelet__ que se ejecuta en ese Node añade un conjunto de variables de entorno en el __Pod__ para todos los __Services__ activos. Por ejemplo, si tenemos un __Service__ activo llamado redis-master, que expone el puerto 6379, y su ClusterIP es 172.17.0.6, entonces, en un __Pod__ recién creado, podemos ver las siguientes variables de entorno:

```
REDIS_MASTER_SERVICE_HOST=172.17.0.6
REDIS_MASTER_SERVICE_PORT=6379
REDIS_MASTER_PORT=tcp://172.17.0.6:6379
REDIS_MASTER_PORT_6379_TCP=tcp://172.17.0.6:6379
REDIS_MASTER_PORT_6379_TCP_PROTO=tcp
REDIS_MASTER_PORT_6379_TCP_PORT=6379
REDIS_MASTER_PORT_6379_TCP_ADDR=172.17.0.6
```

Con esta solución, tenemos que ser cuidadosos al pedir nuestros __Services__, ya que los __Pods__ no tendrán las variables de entorno establecidas para los __Services__ que se crean después de que se creen los __Pods__.

* __DNS__
Kubernetes tiene un complemento para el DNS, que crea un registro DNS para cada __Service__ y su formato es __my-svc.mi-nombreespacio.svc.cluster.local__. Los __Services__ dentro del mismo __Namespace__ encuentran otros servicios sólo por su nombre. Si añadimos un __Service__ redis-master en mi __Namespace__, todos los __Pods__ en el mismo __Namespace__ buscan el __Service__ sólo por su nombre, redis-master. Los __pods__ de otros __Namespaces__ buscan el mismo __Service__ añadiendo el respectivo __Nampespace__ como sufijo, como redis-master.my-ns. 

Esta es la solución más común y altamente recomendada. Por ejemplo, en la imagen de la sección anterior, hemos visto que se configura un DNS interno, que mapea nuestros __Services__ frontend-svc y db-svc a 172.17.0.4 y 172.17.0.5, respectivamente. 


## 10.6. ServiceType
Al definir un __Service__, también podemos elegir su ámbito de acceso. Podemos decidir si el __Service__:

* Es sólo accesible dentro del cluster.
* Es accesible desde el interior del cluster y el mundo exterior.
* Se traza un mapa de una entidad que reside dentro o fuera del cluster.

El alcance del acceso se decide por el _ServiceType_, que puede ser configurado al crear el __Service__.


## 10.7. ServiceType: ClusterIP and NodePort
__ClusterIP__ es el _ServiceType_ por defecto. Un __Service__ recibe una dirección IP virtual, conocida como su __ClusterIP__. Esta dirección IP virtual se utiliza para comunicarse con el __Service__ y sólo se puede acceder a ella dentro del clúster. 

Con el __NodePort ServiceType__, además de un __ClusterIP__, un puerto alto, elegido dinámicamente del rango predeterminado 30000-32767, se asigna al __Service__ respectivo, de todos los __Worker Node__. Por ejemplo, si el __NodePort__ mapeado es 32233 para el frontend-svc, entonces, si nos conectamos a cualquier Worker Node en el puerto 32233, el Node redirigirá todo el tráfico al __ClusterIP__ asignado - 172.17.0.4. Si preferimos un número de puerto alto específico en su lugar, entonces podemos asignar ese número de puerto alto al __NodePort__ del rango por defecto. 

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/10.7_NodePort.png)

El __NodePort__ _ServiceType_ es útil cuando queremos que nuestros servicios sean accesibles desde el mundo exterior. El usuario final se conecta a cualquier Worker Node  en el puerto alto especificado, que envía la solicitud internamente al __ClusterIP__ del __Service__, y luego la solicitud se envía a las aplicaciones que se ejecutan dentro del clúster. Para acceder a varias aplicaciones del mundo externo, los administradores pueden configurar un proxy inverso, es decir, un __Ingress__, y definir reglas que apunten a los __Services__ dentro del clúster.


## 10.8. ServiceType: LoadBalancer
Con el __LoadBalancer__ _ServiceType_:

* __NodePort__ y __ClusterIP__ se crean automáticamente, y el balanceador de carga externo se dirigirá a ellos.
* El __Service__ está expuesto en un puerto estático en cada Worker Node.
* El __Service__ se expone externamente utilizando la función de balanceo de carga del proveedor de nubes subyacente.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/10.8_LoadBalancer.png)

El __LoadBalancer__ _ServiceType_ sólo funcionará si la infraestructura subyacente soporta la creación automática de __Load Balancers__ y tiene el respectivo soporte en los Kubernetes, como es el caso de la plataforma de Google Cloud y AWS. Si no se configura tal característica, el campo de dirección IP del __LoadBalancer__ no se rellena, y el __Service__ funcionará de la misma manera que un __Service__ de tipo __NodePort__.


## 10.9. ServiceType: ExternalIP
Un __Service__ puede ser mapeado a una __ExternalIP__ si puede enrutar a uno o más de los Worker Nodes. El tráfico que entra en el cluster con el __ExternalIP__ (como IP de destino) en el puerto de __Service__, se enruta a uno de los __Service endpoints__. Este tipo de servicio requiere un proveedor de nube externo como Google Cloud Platform o AWS.

![alt text](https://github.com/amartingarcia/k8s_training/blob/master/images/10.9_ExternalIP.png)

Tenga en cuenta que las __ExternalIP__ no son gestionadas por Kubernetes. El administrador del clúster tiene que configurar el enrutamiento que asigna la dirección de__ ExternalIP__ a uno de los Nodes.


## 10.10. ServiceType: ExternalName
__ExternalName__ es un _ServiceType_ especial, que no tiene __Selectors__ y no define ningún endpoint. Cuando se accede dentro del cluster, devuelve un registro __CNAME__ de un __Service__ configurado externamente.

El principal caso de uso de este _ServiceType_ es hacer que los __Services__ configurados externamente como my-database.example.com estén disponibles para las aplicaciones dentro del clúster. Si el __Service__ definido externamente reside dentro del mismo espacio de nombres, el uso del nombre my-database lo haría disponible para otras aplicaciones y servicios dentro de ese mismo __Namespace__.



## Chapter 11. Deploying a Stand-Alone Application<a name="chapter11"></a>
## 11. Deploying a Stand-Alone Application
## 11.1. Deploying an Application Using the Dashboard I
## 11.2. Deploying an Application Using the Dashboard II
## 11.3. Deploying an Application Using the Dashboard III
## 11.4. Exploring Labels and Selectors I
## 11.5. Exploring Labels and Selectors II
## 11.6. Exploring Labels and Selectors III
## 11.7. Deploying an Application Using the CLI I
## 11.8. Deploying an Application Using the CLI II
## 11.9. Exposing an Application I
## 11.10. Exposing an Application II
## 11.11. Accessing an Application
## 11.12. Liveness and Readiness Probes
## 11.13. Liveness
## 11.14. Liveness Command
## 11.15. Liveness HTTP Request
## 11.16. TCP Liveness Probe
## 11.17. Readiness Probes


## Chapter 12. Kubernetes Volume Management<a name="chapter12"></a>
## 12. Kubernetes Volume Management
## 12.1. Volumes
## 12.2. Volume Types
## 12.3. PersistentVolumes
## 12.4. PersistentVolumeClaims
## 12.5. Container Storage Interface (CSI)

## Chapter 13. ConfigMaps and Secrets<a name="chapter13"></a>
## 13. ConfigMaps and Secrets
## 13.1. ConfigMaps
## 13.2. Create a ConfigMap from Literal Values and Display Its Details
## 13.3. Create a ConfigMap from a Configuration File
## 13.4. Create a ConfigMap from a File
## 13.5. Use ConfigMaps Inside Pods
## 13.6. Secrets
## 13.7. Create a Secret from Literal and Display Its Details
## 13.8. Create a Secret Manually
## 13.9. Create a Secret from a File and Display Its Details
## 13.10. Use Secrets Inside Pods


## Chapter 14. Ingress<a name="chapter14"></a>
## 14. Ingress
## 14.1. Ingress I
## 14.2. Ingress II
## 14.3. Ingress Controller
## 14.4. Deploy an Ingress Resource
## 14.5. Access Services Using Ingress


## Chapter 15. Advanced Topics<a name="chapter15"></a>
## 15. Advanced Topics
## 15.1. Annotations
## 15.2. Jobs and CronJobs
## 15.3. Quota Management
## 15.4. Autoscaling
## 15.5. DaemonSets
## 15.6. StatefulSets
## 15.7. Kubernetes Federation
## 15.8. Custom Resources
## 15.9. Helm
## 15.10. Security Contexts and Pod Security Policies
## 15.11. Network Policies
## 15.12. Monitoring and Logging

## Chapter 16. Kubernetes Community<a name="chapter16"></a>
## 16. Kubernetes Community
## 16.1. Kubernetes Community
## 16.2. Weekly Meetings and Meetup Groups
## 16.3. Slack Channels and Mailing Lists
## 16.4. SIGs and Stack Overflow
## 16.5. CNCF Events
## 16.6. What's Next on Your Kubernetes Journey?