### Puede mostrar el nombre de la cuenta activa con este comando:

```
gcloud auth list
```

### Puede mostrar el ID del proyecto con este comando:

```
gcloud config list project
```

### Configurando el Entorno

```
gcloud config set compute/region us-west1
```

### Para ver la configuración de la región del proyecto, ejecute el siguiente comando:

```
gcloud config get-value compute/region
```

### Estableza la zona en:

```
gcloud config set compute/zone us-west1-a
```

### Para ver la configuración de la zona del proyecto, ejecute el siguiente comando:

```
gcloud config get-value compute/zone
```

### En Cloud Shell, ejecute el siguiente comando de gcloud para ver el ID de su proyecto:

```
gcloud config get-value project
```

### En Cloud Shell, ejecute el siguiente comando de gcloud para ver los detalles del proyecto:

```
gcloud compute project-info describe --project $(gcloud config get-value project)
```

## Configure variables de entorno

### Cree una variable de entorno para almacenar su ID de proyecto:

```
export PROJECT_ID=$(gcloud config get-value project)
```

### Cree una variable de entorno para almacenar su Zona:

```
export ZONE=$(gcloud config get-value compute/zone)
```

### Para verificar si las variables se establecieron correctamente, ejecute los siguientes comandos:

```
echo -e "PROJECT ID: $PROJECT_ID\nZONE: $ZONE"
```

### Cree una máquina virtual con la herramienta gcloud

```
gcloud compute instances create gcelab2 --machine-type e2-medium --zone $ZONE
```

### Para abrir la ayuda del comando create, ejecute lo siguiente:

```
gcloud compute instances create --help
```

### Visualice la lista de parámetros de configuración en su entorno:

```
gcloud config list
gcloud config list --all
gcloud components list
```

### Enumere las instancias de procesamiento disponibles en el proyecto.

```
gcloud compute instances list
```

### Enumere la máquina virtual de gcelab2.

```
gcloud compute instances list --filter="name=('gcelab2')"
```

### Enumere las reglas de firewall en el proyecto

```
gcloud compute firewall-rules list
```

### Enumere las reglas de firewall de la red predeterminada.

```
gcloud compute firewall-rules list --filter="network='default'"
```

### Enumere las reglas del firewall de la red predeterminada donde la regla de permiso coincide con una regla ICMP.

```
gcloud compute firewall-rules list --filter="NETWORK:'default' AND ALLOW:'icmp'"
```

### Para conectarse a su VM con SSH, ejecute el siguiente comando:

```
gcloud compute ssh gcelab2 --zone $ZONE
```

### Instale el servidor web nginx en la máquina virtual.

```
sudo apt install -y nginx
exit
```

### Enumere las reglas de firewall del proyecto.

```
gcloud compute firewall-rules list
```

### La comunicación con la máquina virtual fallará si esta no tiene una regla de firewall adecuada. Nuestro servidor web nginx espera comunicarse en tcp:80. Para que la comunicación funcione, necesitamos realizar estas acciones:

- Agregar una etiqueta a la máquina virtual gcelab2
- Agregar una regla de firewall para el tráfico http

```
gcloud compute instances add-tags gcelab2 --tags http-server,https-server
```

### Actualice la regla de firewall para permitir la conexión

```
gcloud compute firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server
```

### Enumere las reglas de firewall del proyecto.

```
gcloud compute firewall-rules list --filter=ALLOW:'80'
```

### Verifique que la comunicación de http a la máquina virtual sea posible.

```
curl http://$(gcloud compute instances list --filter=name:gcelab2 --format='value(EXTERNAL_IP)')
```

### Visualice los registros disponibles en el sistema:

```
gcloud logging logs list
```

### Consulte los registros de procesamiento.

```
gcloud logging logs list --filter="compute"
```

### Lea los registros relacionados con el tipo de recurso de gce_instance.

```
gcloud logging read "resource.type=gce_instance" --limit 5
```

### Lea los registros de una máquina virtual específica.

```
gcloud logging read "resource.type=gce_instance AND labels.instance_name='gcelab2'" --limit 5
```
