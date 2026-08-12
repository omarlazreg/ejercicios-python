# DOCKER - Guía completa

## 1. ¿Qué es Docker?

Docker es una plataforma que permite empaquetar una aplicación junto con TODO lo que necesita para funcionar (código, librerías, dependencias, configuración, sistema operativo base) en una unidad llamada **contenedor**.

```
┌─────────────────────────────────────┐
│           TU APLICACIÓN              │
│  ┌─────────────────────────────┐    │
│  │  Código                      │    │
│  │  Librerías (pandas, numpy)   │    │
│  │  Dependencias del sistema    │    │
│  │  Variables de entorno        │    │
│  │  Configuración               │    │
│  └─────────────────────────────┘    │
│         = CONTENEDOR DOCKER          │
└─────────────────────────────────────┘
```

El objetivo es una sola frase: **"funciona en mi máquina" deja de ser un problema, porque el contenedor funciona igual en cualquier máquina.**

---

## 2. El problema que resuelve

Sin Docker:

```
DESARROLLADOR                    SERVIDOR PRODUCCIÓN
─────────────                    ────────────────────
Python 3.11                      Python 3.9
pandas 2.1                       pandas 1.5
Windows                          Linux
        │                               		 │
        └──────► ¿FUNCIONA IGUAL? ───────┘
                    ❌ A VECES NO
```

Con Docker:

```
DESARROLLADOR                    SERVIDOR PRODUCCIÓN
─────────────                    ────────────────────
┌──────────────┐                ┌──────────────┐
│  CONTENEDOR  │   idéntico     │  CONTENEDOR  │
│  Python 3.11 │ ═════════════► │  Python 3.11 │
│  pandas 2.1  │                │  pandas 2.1  │
└──────────────┘                └──────────────┘
        ✅ FUNCIONA IGUAL SIEMPRE
```

---

## 3. Conceptos clave

```
IMAGEN  ──────►  Plantilla / receta (solo lectura)
                  Ej: "Python 3.11 + librerías X"

CONTENEDOR ────►  Instancia en ejecución de una imagen
                  (la imagen "cobra vida")

DOCKERFILE ────►  Archivo de texto con instrucciones
                  para construir una imagen paso a paso

DOCKER HUB ────►  Repositorio online de imágenes
                  (como GitHub pero de imágenes)

VOLUMEN ───────►  Almacenamiento persistente fuera
                  del contenedor (los datos no se
                  pierden si el contenedor se borra)

REGISTRY ──────►  Lugar donde se guardan y comparten
                  imágenes (Docker Hub, ECR, ACR...)
```

Relación entre ellos:

```
Dockerfile  ──build──►  Imagen  ──run──►  Contenedor
(receta)                (plantilla)        (ejecutándose)
```

---

## 4. Ejemplo de Dockerfile (estructura típica)

```
FROM python:3.11          → parte de una imagen base
WORKDIR /app               → carpeta de trabajo dentro del contenedor
COPY requirements.txt .    → copia archivo de dependencias
RUN pip install -r requirements.txt   → instala dependencias
COPY . .                   → copia el resto del código
CMD ["python", "app.py"]   → comando que se ejecuta al arrancar
```

---

## 5. Flujo de trabajo completo

```
 1. Escribes el Dockerfile
          │
          ▼
 2. docker build → construyes la IMAGEN
          │
          ▼
 3. docker run → arrancas el CONTENEDOR
          │
          ▼
 4. docker push → subes la imagen a un REGISTRY
          │
          ▼
 5. Otro equipo/servidor hace docker pull
          │
          ▼
 6. docker run → mismo contenedor, mismo resultado
```

---

## 6. Arquitectura: Docker vs Máquinas Virtuales

```
MÁQUINA VIRTUAL                    DOCKER (CONTENEDORES)
────────────────                   ──────────────────────
┌─────┐ ┌─────┐ ┌─────┐            ┌─────┐ ┌─────┐ ┌─────┐
│ App │ │ App │ │ App │            │ App │ │ App │ │ App │
├─────┤ ├─────┤ ├─────┤            ├─────┤ ├─────┤ ├─────┤
│ SO  │ │ SO  │ │ SO  │            │     │ │     │ │     │
│compl│ │compl│ │compl│            └─────┴─┴─────┴─┴─────┘
├─────┴─┴─────┴─┴─────┤            │   Docker Engine      │
│    Hipervisor        │            ├──────────────────────┤
├──────────────────────┤            │  SO del servidor     │
│  SO servidor físico   │            │  (solo uno)          │
└──────────────────────┘            └──────────────────────┘

Pesado, lento de arrancar         Ligero, arranca en segundos
(minutos, GB de tamaño)           (milisegundos, MB de tamaño)
```

Cada máquina virtual lleva su propio sistema operativo completo. Los contenedores comparten el sistema operativo del servidor, por eso son mucho más ligeros y rápidos.

---

## 7. Docker Compose (varios contenedores a la vez)

Cuando una aplicación necesita varias piezas (por ejemplo: base de datos + backend + frontend), se define todo en un solo archivo:

```
docker-compose.yml
        │
        ├── servicio: base_de_datos (imagen postgres)
        ├── servicio: backend       (imagen python)
        └── servicio: frontend      (imagen node)
                │
                ▼
    docker-compose up
                │
                ▼
   ┌──────────┐ ┌──────────┐ ┌──────────┐
   │   BBDD   │ │ BACKEND  │ │ FRONTEND │
   │contenedor│ │contenedor│ │contenedor│
   └──────────┘ └──────────┘ └──────────┘
        Se levantan todos a la vez, conectados entre sí
```

---

## 8. Uso empresarial (por qué importa a nivel negocio)

```
PROBLEMA DE NEGOCIO              SOLUCIÓN CON DOCKER
────────────────────              ────────────────────
Migrar de proveedor cloud   ───►  Los contenedores son
sin reescribir todo               portables entre AWS,
                                   Azure, GCP, on-premise

Escalar en picos de demanda ───►  Se pueden arrancar 50
(ej. Black Friday)                copias del mismo
                                   contenedor en segundos

Onboarding de nuevos        ───►  El nuevo empleado hace
desarrolladores lento             "docker run" y ya tiene
                                   el entorno completo

Entornos de dev/test/prod   ───►  Mismo contenedor en los
distintos = bugs                  3 entornos = menos
                                   sorpresas en producción

Costes de infraestructura   ───►  Más aplicaciones por
                                   servidor físico (mejor
                                   aprovechamiento)
```

---

## 9. Casos de uso típicos en un entorno de datos (relevante para tu perfil)

```
CASO 1: Pipeline de datos reproducible
────────────────────────────────────────
   Contenedor con Python + librerías fijas
   → Airflow ejecuta ese contenedor
   → Mismo resultado siempre, sin importar
     dónde se ejecute el pipeline

CASO 2: Entorno de desarrollo aislado
────────────────────────────────────────
   Cada proyecto tiene su propio contenedor
   → No hay conflictos de versiones de
     librerías entre proyectos distintos

CASO 3: Despliegue de modelos ML
────────────────────────────────────────
   Modelo + librerías empaquetados
   → Se despliega igual en local, testing
     y producción

CASO 4: Microservicios
────────────────────────────────────────
   Cada servicio de la empresa (facturación,
   stock, pedidos) en su propio contenedor
   → Se actualizan y escalan de forma
     independiente
```

---

## 10. Resumen ejecutivo (para directores)

```
┌─────────────────────────────────────────────────┐
│  DOCKER EN UNA FRASE:                            │
│                                                   │
│  "Empaqueta una aplicación una vez,              │
│   ejecútala igual en cualquier lugar."           │
│                                                   │
│  BENEFICIOS CLAVE:                               │
│  ✓ Menos errores entre entornos                  │
│  ✓ Despliegues más rápidos                       │
│  ✓ Mejor uso de recursos/infraestructura         │
│  ✓ Escalabilidad bajo demanda                    │
│  ✓ Portabilidad entre proveedores cloud          │
└─────────────────────────────────────────────────┘
```

DOCKERFILE                          COMANDOS DOCKER (terminal)
─────────────                       ────────────────────────────
Sintaxis propia, muy simple,        Se ejecutan en la terminal
tipo "instrucciones":               normal (cmd, PowerShell,
                                     bash...), como cualquier
FROM, RUN, COPY, CMD,               otro comando (ls, cd, git...)
WORKDIR, ENV, EXPOSE...

Se parece más a un archivo de
configuración que a un lenguaje
de programación real. 

- Documentación oficial: docs.docker.com/get-started
- Docker Curriculum (gratuito, muy práctico): docker-curriculum.com
- Cursos en YouTube: "Docker tutorial for beginners"
- Play with Docker (playground online, sin instalar nada):
  labs.play-with-docker.com