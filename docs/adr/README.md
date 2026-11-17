# Decisiones de arquitectura (ADR)

Las decisiones más importantes de la propuesta, en formato [MADR](https://adr.github.io/madr/) simplificado. Todas están en estado "Propuesto" porque dependen de validarse con el banco.

| ADR | Decisión |
|---|---|
| [ADR-001](ADR-001-nube-aws-y-dr.md) | AWS (us-east-1 y us-east-2) con DR por niveles de criticidad |
| [ADR-002](ADR-002-integracion-bff-saga-eventos.md) | API Gateway + BFF, saga para transferencias y eventos con EventBridge y SQS |
| [ADR-003](ADR-003-identidad-fapi2-biometria.md) | OAuth 2.0 con FAPI 2.0, Keycloak para clientes, Entra ID para empleados y biometría |
| [ADR-004](ADR-004-auditoria-cache-notificaciones.md) | Auditoría inmutable, caché para clientes frecuentes y notificaciones desde el core |
| [ADR-005](ADR-005-frontend-angular-flutter.md) | Angular para web y Flutter para móvil |
