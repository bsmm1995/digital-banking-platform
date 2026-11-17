# ADR-001: AWS con DR por niveles de criticidad

Estado: Propuesto. Fecha: 2026-11-15.

## Contexto

AWS no tiene región en Ecuador. El reto pide baja latencia, alta disponibilidad y una estrategia de DR, y no todos los servicios necesitan recuperarse en el mismo tiempo.

## Decisión

Región principal us-east-1 y DR en us-east-2, con CloudFront en el borde y Direct Connect hacia el core. El DR se organiza en cuatro niveles: T0 (identidad, transferencias y auditoría) con RPO casi cero y RTO de 15 minutos o menos, y niveles T1 a T3 con estrategias más económicas. Además, respaldos inmutables en Oracle Cloud Bogotá, con las llaves custodiadas fuera de AWS, para el caso de perder el proveedor completo.

## Alternativas descartadas

- São Paulo como región principal o de DR: más cara, con más retraso de replicación, y al seguir siendo AWS no reduce el riesgo de proveedor.
- Chile: todavía no tiene disponibilidad general. Se revisará más adelante.
- Multicloud activo: costo y complejidad desproporcionados para el tamaño del banco.
- Activo-activo entre regiones: costo alto y riesgo de inconsistencias en saldos.

## Consecuencias

A favor: catálogo completo, menor costo y un DR cuyo costo va de acuerdo con la criticidad. Los datos sobreviven incluso a una caída total de AWS.

En contra: los datos quedan fuera del país, lo que exige contrato de encargo y cifrado con llaves del banco. La copia en Oracle protege los datos, no el servicio. La latencia real hay que medirla antes de producción.
