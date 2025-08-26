# Despliegue del sistema utilizando Docker

Para gestionar el despliegue de los diferentes servicios que conforman el servicio de laboratorios virtuales y remotos se utilizan imágenes de docker para cada uno de los servicios. Esto flexibiliza el despliegue de los servicios y los independiza de la máquina de desarrollo, permitiendo realojar todas el servicio web en diferentes máquinas en un tiempo reducido.

El frontend se desarrolla de forma independiente del backend utilizando el servidor de desarrollo de vite. En producción el frontend tiene que servirse desde el backend. En esta página se explica también cómo es esta integración. 


## Integración del frontend en el backend

Se genera el código de producción correspondiente al frontend:

```shell
# npm ci (Clean Installation) Realiza una instalaciónn limpia de los paquetes para versión de producción
npm ci 
npm run bukd
```
Esto crea en la carpeta ```\dist``` un archivo ```index.html```  estático y una carteta ```\assets``` con los scripts, estilos y el resto de fuentes necesarias para ejecutar el frontend de forma estática. 


Una vez creados, los archivos estáticos que cuelgan de ```\assets``` del frontend se sirven en el backend:


```python

# backend/app/main.py
from fastapi import FastAPI
from fastapi.staticfiles import StaticFiles
from fastapi.responses import FileResponse

app = FastAPI()


#####################################################################################
# Archivos estáticos del frontend
#####################################################################################
FRONTEND_PATH = "../virtyremlab-web-react-fronted" # Cambiar por una ruta propia en 
#despliegue de docker

app.mount("/assets", StaticFiles(directory=f"{FRONTEND_PATH}/assets"), name="assets")



```

Se prepara el backend para que responda con ```index.html``` cuando le llegue una petición a la raíz

```python

@app.get("/", response_class=HTMLResponse, include_in_schema=False)
def root():
    return FileResponse(f"{DIST_DIR}/index.html")

```

Se crea un fallback para que cualquier ruta que no coincida con los endpoints soportados por el backend se redireccionen a directorio raíz:

```python
# Fallback 
@app.exception_handler(404)
async def redirect_404_to_root(request, exc):
    return RedirectResponse(url="/")
```


