# ADR-002: API Gateway + BFF, saga para transferencias y eventos con EventBridge y SQS

Estado: Propuesto. Fecha: 2026-11-15.

## Contexto

El reto pide un API Gateway que orqueste clientes, movimientos y transferencias. Una transferencia interbancaria cruza el core y el BCE sin una transacción común, y los servicios tienen que comunicarse sin acoplarse.

## Decisión

Amazon API Gateway para lo transversal y un BFF por canal para orquestar. Transferencias con saga orquestada, idempotencia y outbox. Eventos con EventBridge y SQS, y CDC del core con AWS DMS. El backend es Java 25: Spring Boot 4.1 WebFlux en los BFF y el dominio, y Quarkus 3.40 nativo en Notificaciones y Auditoría.

## Alternativas descartadas

- Orquestar en el gateway: mezcla la lógica de negocio con la infraestructura.
- Apache Kafka (MSK): más de lo que necesita un banco mediano, y exige operar un clúster.

## Consecuencias

A favor: un gateway simple, canales independientes, ningún doble débito por reintentos y eventos que se pagan por uso.

En contra: EventBridge entrega al menos una vez, así que todos los consumidores deben ser idempotentes.
