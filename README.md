# Sistema de Predicción de Abandono de Clientes de Telefónica utilizando contenedores de Docker

Este proyecto implementa un sistema de predicción que utiliza un modelo de randomforest para predecir si un cliente de Telefónica abandonará el servicio o no. El sistema ofrece múltiples interfaces de comunicación, incluyendo REST, Kafka y gRPC.

## Tercera Entrega: Integración con Docker Compose

En esta parte de proyecto, se define un archivo docker compose, que permite construir las imagenes, lanzar los contenedores, crear redes y detener contenedores, así como el manejo volumenes y variables de entorno de manera sencilla.

## Datos de entrada para el modelo

| Campo                        | Tipo    | Descripción                           |
|------------------------------|---------|---------------------------------------|
| internet_service             | int     | Tipo de servicio de internet          |
| number_dependents            | int     | Número de dependientes                |
| number_referrals             | int     | Número de referencias                 |
| satisfaction_score           | int     | Puntuación de satisfacción            |
| tenure_in_months             | int     | Tiempo como cliente (en meses)        |
| total_long_distance_charges  | float   | Cargos totales de larga distancia     |
| total_revenue                | float   | Ingresos totales generados            |
| contract                     | string  | Tipo de contrato                      |
| payment_method               | string  | Método de pago                        |


## Características principales

- Interfaces múltiples: REST, Kafka y gRPC.
- Procesamiento concurrente mediante hilos.

## Estructura del proyecto

El proyecto está organizado en los siguientes componentes, cada uno disponibilizado en su propio contenedor Docker:

1. **API REST** (`docker_rest/`):
   - `Dockerfile`: Define la imagen Docker para el servicio REST.
   - `app.py`: Implementa un servidor FastAPI para recibir solicitudes HTTP.
   - `requirements.txt`: Lista las dependencias necesarias para el servicio REST.

2. **API Kafka** (`docker_kafka_topics/`):
   - `Dockerfile`: Define la imagen Docker para los servicios de Kafka.
   - `consumer.py`: Consume mensajes de Kafka y realiza predicciones.
   - `producer.py`: Envía solicitudes de predicción a través de Kafka.
   - `requirements.txt`: Lista las dependencias necesarias para los servicios de Kafka.

3. **API gRPC** (`docker_gRPC/`):
   - `Dockerfile`: Define la imagen Docker para el servicio gRPC.
   - `server.py`: Implementa el servidor gRPC.
   - `client.py`: Implementa el cliente gRPC para pruebas.
   - `requirements.txt`: Lista las dependencias necesarias para el servicio gRPC.

4. **Servidor Kafka** (`docker_kafka_server/`):
   - `Dockerfile`: Define la imagen Docker para el servidor Kafka.
   - Incluye configuraciones para Kafka y Zookeeper.

5. **Modelo de predicción** (`models/`):
   - Contiene los archivos del modelo pre-entrenado (`.joblib` o `.pkl`).
   - Este directorio se monta en los contenedores que necesitan acceso al modelo.

6. **Scripts de utilidad**:
   - `docker-compose.yaml`: Construye, lanza y detiene los contenedores
    
   - `tests.py`: Script para realizar pruebas en las diferentes interfaces (se ejecuta fuera de los contenedores).

## Funcionamiento de las interfaces

### REST API

- Endpoint: `GET /predict`
- Acepta parámetros de consulta para los datos del cliente.
- Devuelve la predicción en formato JSON.

### Kafka

- Utiliza dos temas: uno para solicitudes y otro para respuestas.
- El consumidor procesa las solicitudes y envía las predicciones de vuelta.
- El productor envía solicitudes y recibe las respuestas.

### gRPC

- Define un servicio con un método `Predict`.
- El cliente envía los datos del cliente como mensaje protobuf.
- El servidor responde con la predicción.

## Procesamiento concurrente

El sistema utiliza hilos para manejar múltiples solicitudes simultáneamente, especialmente en las implementaciones de Kafka y gRPC.

## Cómo usar

1. **Levantar todos los contenedores**:


```bash
   docker compose up -d
```

2. **Realizar pruebas**:

```bash
   python -m venv env
   source env/bin/activate
   pip install -r requirements.txt
   python tests.py
```

Este script realiza pruebas en todas las interfaces (REST, Kafka, gRPC).