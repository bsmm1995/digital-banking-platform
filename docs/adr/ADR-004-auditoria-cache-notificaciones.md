# ADR-004: Auditoría inmutable, caché y notificaciones desde el core

Estado: Propuesto. Fecha: 2026-11-15.

## Contexto

El reto pide auditar las acciones de los clientes, usar caché para los clientes frecuentes y notificar los movimientos por al menos dos canales.

## Decisión

Auditoría: los eventos llegan a un servicio en Quarkus nativo que los guarda en una base de solo inserción con hash encadenado y luego se archivan en S3 Object Lock. Caché: Cache-Aside con ElastiCache Valkey (LFU), invalidada con los eventos del core. Notificaciones: push y correo, con SMS de respaldo, generadas a partir de los cambios del core.

## Alternativas descartadas

- Amazon QLDB: sin soporte desde 2025.
- SMS como canal principal: más caro y más expuesto a fraude.

## Consecuencias

A favor: cualquier alteración de la auditoría se detecta y se notifican también los movimientos de cajero y ventanilla.

En contra: la caché puede estar desactualizada unos segundos. Por eso el saldo nunca se cachea.
