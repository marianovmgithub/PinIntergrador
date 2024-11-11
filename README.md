# Pin Intergrador DevOps 2402

**Instructivo Paso a Paso para la Ejecución**

**Paso 1: Preparar la Máquina Virtual con Ubuntu**
Asegúrate de que tu máquina virtual tenga Docker instalado.

Instala Jenkins en Docker:

Copiar código
docker run -d -p 8080:8080 -p 50000:50000 --name jenkins --group-add $(getent group docker | cut -d: -f3) -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts

**Nota:** Verifica que el contenedor de Jenkins tenga acceso al socket de Docker y que esté en el mismo grupo de usuarios (docker) para ejecutar comandos de Docker y kubectl.

**Paso 2: Configurar Jenkins**
- Accede a Jenkins en http://localhost:8080 y sigue las instrucciones para configurar el servidor.

- Instala los plugins necesarios en Jenkins:
    **Pipeline** (para crear y ejecutar pipelines).
    **Kubernetes CLI** (para ejecutar comandos kubectl).
    **Docker Pipeline** (para usar Docker en pipelines).

**Paso 3: Cargar los Jenkinsfiles en Jenkins**
- Crea dos proyectos de tipo Pipeline en Jenkins.
- Copia y pega los Jenkinsfiles en la sección de configuración de cada proyecto.

**Paso 4: Configurar Credenciales**
- Agrega las credenciales de AWS (Access Key y Secret Key) en la sección de Manage Jenkins > Credentials.
- Carga tu clave SSH privada (pin.pem) en Jenkins para el acceso SSH al clúster.

**Paso 5: Ejecutar los Pipelines**
- Ejecuta el proyecto de infraestructura productiva primero. Verifica los logs de la consola de Jenkins para asegurarte de que cada etapa se complete correctamente.

- Luego, ejecuta el proyecto de telemetría para desplegar Prometheus, Grafana y Telegraf.

**Paso 6: Verificar el Despliegue en AWS**
- Accede a la consola de AWS y revisa los clústeres EKS y los pods en ejecución.
- Usa kubectl get pods -n prometheus y kubectl get pods -n grafana para comprobar que Prometheus y Grafana están funcionando.

**Paso 7: Acceder a Grafana**
- Obtén la URL del servicio LoadBalancer de Grafana con kubectl get svc -n grafana.
- Accede a la URL desde tu navegador y entra con las credenciales configuradas (admin y EKS!sAWSome).

**Paso 8: Configurar Telegraf**
- Asegúrate de que el ConfigMap de Telegraf esté configurado para recolectar métricas de CPU, memoria y disco y que exponga los datos a Prometheus.
- Agrega Prometheus como fuente de datos en Grafana y configura un dashboard para visualizar las métricas recolectadas.

**Consideraciones al Ejecutar Jenkins en Docker**
**- Acceso a la Red:** Asegúrate de que el contenedor de Jenkins tenga acceso a Internet y pueda comunicarse con AWS y los clústeres de Kubernetes.
**- Persistencia de Datos:** Utiliza volúmenes de Docker (-v) para mantener los datos de Jenkins entre reinicios.
**- Permisos:** Verifica que el contenedor de Jenkins tenga los permisos necesarios para ejecutar comandos de kubectl y eksctl.
