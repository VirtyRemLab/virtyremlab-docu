# Exportar archivo requirements desde un entorno de anaconda 


Lo primero es necesario exportar las dependencias del entorno de anaconda:

```shell
conda env export -f conda-requirements.yml
```


Después se extraen las dependencias de pip:

```shell
 pip freeze --exclude-editable > pip-freeze.txt
```

Ambos archivos se combinan en ```requirements.yml```

Hay que tener en cuenta:



- Evitar a dependencias directas.
- Eliminar información del sistema local: prefix, build, etc.



Un ejemplo:

```yml
name: remlab-env
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.11
  - pip

  # Core backend (FastAPI + websockets + SIO)
  - fastapi=0.115
  - starlette=0.46
  - uvicorn=0.34
  - websockets=15
  - python-socketio=5.13
  - python-engineio=4.12

  # Red y utilidades
  - httpx=0.28
  - pyyaml
  - jinja2

  # Paquetes con C via conda (mejor que pip)
  - cffi
  - cryptography
  - lxml
  - uvloop

  # SOAP/ONVIF base (zeep existe en conda; el wrapper onvif_zeep, no)
  - zeep

  - pip:
      # Solo lo que no esté en conda-forge
      - onvif_zeep==0.2.12

```