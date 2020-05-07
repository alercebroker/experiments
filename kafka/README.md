# Kafka
Este set de experimentos, consistio principalmente en probar, modificar y optimizar configuraciones propias de KAFKA para obtener mejores resultados tanto en el throughput como en los tiempos de procesamiento de los distintos pasos de la Pipeline. 
## 1 - File Descriptor
## 2 - Ram Cache
## 3 - Aumento de discos
### Descripción: 
En el inicio de las pruebas, la instancia de EC2 solo tenia un disco (root), para escalar, creamos 3 discos, las cuales fueron montadas en 3 carpetas correspondientes. Con esto logramos segmentar la W/R de los topicos logrando mejores velocidades de R/W.
### Pasos para la implementación
1 - Agregar 3 nuevos volumenes en AWS <br/>
2 - Asociarlos a la instancia donde esta kafka <br/>
3 - Los discos se deben montar de la siguiente forma: 
- lsblk - ver particiones <br/>
- sudo mkfs -t xfs /dev/xxx - formatiar la partición <br/>
- sudo mkdir /data - crear la carpeta donde montaremos la partición <br/>
- sudo mount /dev/xxx /data - montar el volumen <br/>
4 - Cambiar arhivo de configuración de Kafka <br/>
- Debemos buscar y editar el archivo de configuración de kafka (Server.properties) <br/>
- Editar la linea log-dirs agregando los nuevos path, separandolos por (,) <br/>
5 Dar permisos de escritura y lectura al usuario donde corre Kafka. <br/>
6

## 4 - Configuraciones de Server.Properties
