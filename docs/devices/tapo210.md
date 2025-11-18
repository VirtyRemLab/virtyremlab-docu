# CÁMARA TAPO C210

Cámara ip para la supervisión de los equipos de forma remota. Esta cámara se conectará dentro la misma red local que el equipamiento de prácticas. La plataforma de laboratorios remotos accede a la imagen de la cámara y sus controles por diversos conntroles de comunicación permitiendo un stream de video en tiempo real. En esta página se describe la configuración de la cámara y su integración en el backend del sistema.

# Configuración incial

Es necesario descargar la aplicación *Tapo app* para Android o Iphone. Dentro de la aplicación, en configuración avanzada, se configura una IP estática y se habilita una cuenta de RTSP. En las opciones de configuración RTSP, se debe crear un usuario y contraseña. Esto nos permite acceder a la cámara desde nuestra plataforma desde RST.

# Envio del stream a nuestro servidor

Dentro de la misma red de la cámara conectamos una RPI, la cual se conecta al servidor RTSP para extraer el stream de video y enviarlo a un servidor de medios alojado en la plataforma de laboratorios remotos. Esto se realiza ejecutando el siguiente script de bash:

```bash

CAM_URL="rtsp://virtyremlab:cam_aeropendulo@IP_camara:554/stream2"
SRV_URL="rtsp://relay:supersecreto@IP_servidor_labs:8554/tapo"

ffmpeg -hide_banner \
  -rtsp_transport tcp -stimeout 5000000 -i "$CAM_URL" \
  -fflags +genpts -use_wallclock_as_timestamps 1 -fflags nobuffer \
  -vf "scale=640:-2,fps=15" \
  -c:v h264_v4l2m2m -b:v 300k -maxrate 300k -bufsize 300k \
  -g 30 -keyint_min 30 -bf 0 -tune zerolatency \
  -an \
  -f rtsp -rtsp_transport udp "$SRV_URL"

```

Para que este script se ejecute desde la RPI cada vez que se arranque y que se reinicie cada vez que ocurra un error, se crea un servicio en raspbian siguiendo los siguientes pasos:

1. Mejoramos el script para que se vuelve a ejecutar si se produce un error.
```bash
#!/bin/bash
CAM_URL="rtsp://virtyremlab:cam_aeropendulo@IP_camara:554/stream2"
SRV_URL="rtsp://relay:supersecreto@IP_servidor_labs:8554/tapo"
while true; do
  echo "Iniciando ffmpeg..."
  ffmpeg -hide_banner \
    -rtsp_transport tcp -stimeout 5000000 -i "$CAM_URL" \
    -fflags +genpts -use_wallclock_as_timestamps 1 -fflags nobuffer \
    -vf "scale=640:-2,fps=15" \
    -c:v h264_v4l2m2m -b:v 300k -maxrate 300k -bufsize 300k \
    -g 30 -keyint_min 30 -bf 0 -tune zerolatency \
    -an \
    -f rtsp -rtsp_transport udp "$SRV_URL"

  echo "ffmpeg se ha parado con código $? — reintentando en 5s..."
  sleep 5
done
```
2. Damos permisos de ejecución al script:
```bash
chmod +x rtsp.sh
```
3. Se crea y configura el servicio:
```bash
sudo nano /etc/systemd/system/tapo-relay.service
```

```bashrc
[Unit]
Description=CAM_TRANS_RPI
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=diego                     # cambiar si el usuario NO es 'pi'
ExecStart=/home/diego/tapo-rtsp.sh
Restart=always
RestartSec=5
# Opcional: limita uso de CPU/memoria si quieres
# Nice=5

[Install]
WantedBy=multi-user.target

```

3. Se habilita el servicio:
```bash
sudo systemctl daemon-reload
sudo systemctl enable tapo-relay.service
sudo systemctl start tapo-relay.service
```
4. Podemos ver los logs del servicio así:
```bash
journalctl -u tapo-relay.service -f
```