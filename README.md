# Despliegue de una aplicación Node.js con Express en clúster

## 📖 Introducción

En esta práctica se ha trabajado el despliegue de una aplicación **Node.js con Express**, analizando las diferencias de rendimiento entre una aplicación **sin clusterizar** y otra **utilizando clústeres**.

---

## 1️⃣ Preparación del entorno

En primer lugar, se accede al servidor **Debian/Ubuntu** mediante **SSH**.

mkdir practica-cluster
cd practica-cluster
Se inicializa el proyecto Node.js:

npm init
Durante la inicialización se aceptan los valores por defecto pulsando ENTER en mi caso, he puesto -y para ahorrame ese trabajo.

<img width="362" height="24" alt="image" src="https://github.com/user-attachments/assets/076b1b4a-697f-4d02-a3bb-af92b2b645ea" />

Finalmente, se instala Express:

npm install express


<img width="423" height="33" alt="image" src="https://github.com/user-attachments/assets/48964dcc-b243-4281-af06-5e535597a861" />


Instalación correcta de Express mediante npm.

2️⃣ Aplicación sin clúster
Se crea un archivo JavaScript (por ejemplo app.js) con una aplicación Express sencilla que contiene dos rutas:


<img width="783" height="386" alt="2 codigo app_simple" src="https://github.com/user-attachments/assets/1907054a-108d-4dbe-944f-397d5f6ac110" />

La ejecutamos para ver sus salidas.

<img width="422" height="49" alt="3 ejecucion app_simple" src="https://github.com/user-attachments/assets/3115a080-b0f4-4783-b428-54d395f22576" />

Se comprueba el funcionamiento accediendo desde el navegador:

http://IP-maquina:3000


/ → Devuelve Hello World!

<img width="427" height="100" alt="5 Comprobacion app_simple2" src="https://github.com/user-attachments/assets/578c0538-cd08-4446-9d87-d0aa87bc9631" />



http://IP-maquina:3000/api/50

/api/n → Realiza una suma intensiva para simular una carga elevada


<img width="535" height="98" alt="4 Comprobacion en navegador de app_simple" src="https://github.com/user-attachments/assets/638ab696-62a9-4384-8b03-0abc48cc7bda" />




3️⃣ Comportamiento sin clúster
Se accede a la ruta con un valor muy alto:

/api/5000000000
Mientras esta petición está en ejecución, se realiza otra petición en una pestaña distinta.


<img width="1065" height="211" alt="6 1 Comprobación de red pequeña" src="https://github.com/user-attachments/assets/b0b725a8-85cd-4bbd-b40f-25af38228356" />
<img width="1059" height="247" alt="6 Comprobacion de red" src="https://github.com/user-attachments/assets/e5ab9abb-c83f-469c-97cd-2a41d00ee2ba" />



4️⃣ Aplicación con clúster usando Node.js
Se modifica la aplicación para usar el módulo cluster y crear tantos workers como núcleos de CPU tenga el sistema.
Código fuente de la aplicación usando el módulo cluster.

<img width="760" height="455" alt="8  app_cluster" src="https://github.com/user-attachments/assets/0f811d92-7bd7-449f-9d74-bee8d0cbb225" />


Aspectos clave:

El proceso maestro crea los workers.

Cada worker atiende peticiones en el mismo puerto.

Si un worker muere, se genera otro automáticamente.

Se ejecuta la aplicación:

node app_cluster.js



<img width="427" height="133" alt="image" src="https://github.com/user-attachments/assets/2b831ae5-143e-4289-afb6-a51bfafb85fd" />


5️⃣ Pruebas de rendimiento con loadtest
Se instala la herramienta de pruebas de carga:

npm install -g loadtest

Instalación global de loadtest.

🔬 Pruebas sin clúster
loadtest http://localhost:3000/api/500000 -n 1000 -c 100

<img width="603" height="351" alt="10 loadtest app_simple ligero" src="https://github.com/user-attachments/assets/9acd6bbc-d6b0-4949-bd18-f9975595f991" />

Resultados de loadtest sin clúster (latencia alta, RPS bajo).

Se repite la prueba con un valor más alto:

loadtest http://localhost:3000/api/500000000 -n 1000 -c 100


<img width="415" height="331" alt="10 1 loadtest app_simple pesado" src="https://github.com/user-attachments/assets/d577dd1c-010f-4e06-97b7-be1aee89045b" />


Métricas empeoradas sin clúster.

🔬 Pruebas con clúster
Se repiten exactamente las mismas pruebas con la aplicación clusterizada.


<img width="587" height="363" alt="11 loadtest app_cluster ligero" src="https://github.com/user-attachments/assets/7fc1d43d-dfd7-4a55-b412-9f54ac1186d5" />



Resultados con clúster (mejor latencia y más RPS).


<img width="438" height="341" alt="11 1 loadtest app_cluster pesado" src="https://github.com/user-attachments/assets/1c418731-8e55-4d04-9176-9d6c1156f438" />


Comparativa clara del aumento de rendimiento con clúster.

6️⃣ Uso de PM2 para administrar el clúster
Se instala PM2 globalmente:

npm install pm2 -g

Se ejecuta la aplicación sin modificar el código, pero en modo clúster:

pm2 start app_simple.js -i 0

<img width="1012" height="870" alt="12 pm2 start" src="https://github.com/user-attachments/assets/4db449a7-5895-45f1-be1e-6f63cab208e0" />


PM2:

Genera automáticamente los workers

Reinicia procesos caídos

Balancea la carga

7️⃣ Archivo Ecosystem de PM2
Se genera el archivo de configuración:

pm2 ecosystem


Archivo ecosystem.config.js generado.

Se configura para ejecutar la aplicación en modo clúster.

<img width="675" height="185" alt="14 edición archivo ecosystem config" src="https://github.com/user-attachments/assets/f745e3d8-6fb6-41e6-b23e-7fbce089bc3c" />


Contenido del archivo ecosystem configurado correctamente.

Se inicia la aplicación con:

pm2 start ecosystem.config.js

<img width="992" height="181" alt="15 pm2 ecosystem" src="https://github.com/user-attachments/assets/83806d51-6958-4970-9157-3d6f7f2cea2d" />


8️⃣ Comandos principales de PM2
🔹 pm2 ls
Muestra todas las aplicaciones gestionadas por PM2, su estado, consumo de CPU y memoria.

<img width="1013" height="126" alt="image" src="https://github.com/user-attachments/assets/3957348f-0d97-420e-8784-0b3bbcc058c5" />


🔹 pm2 logs
Muestra los logs en tiempo real de la aplicación.

<img width="1016" height="430" alt="image" src="https://github.com/user-attachments/assets/9bd1c692-73e4-4402-b189-c720d13ebf50" />



🔹 pm2 monit
Abre un monitor interactivo con métricas en tiempo real.


<img width="1072" height="864" alt="13 1 Tarea investigación" src="https://github.com/user-attachments/assets/36e4cccc-a0bf-4b7b-aa26-2899181932cc" />


9️⃣ Cuestiones finales
¿Por qué en algunos casos la aplicación sin clúster tiene mejores resultados?
En determinadas pruebas:

En casos con cargas de trabajo muy ligeras, la aplicación sin clúster puede superar a la clusterizada debido a la sobrecarga de gestión para operaciones extremadamente breves ya que el coste de gestionar el clúster 
(comunicación IPC y balanceo) es mayor que el tiempo de ejecución de la tarea misma, resultando en métricas de latencia y RPS ligeramente peores que en un despliegue monohilo
