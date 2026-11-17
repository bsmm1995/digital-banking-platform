# ADR-005: Angular para web y Flutter para móvil

Estado: Propuesto. Fecha: 2026-11-15.

## Contexto

El reto pide una SPA web, una app multiplataforma y dos opciones de stack justificadas.

## Decisión

Angular 22 para la web y Flutter 3.47 para móvil. La segunda opción evaluada fue React + React Native. La desventaja de manejar dos lenguajes se compensa con contract-first: los contratos OpenAPI generan los clientes de ambos frentes.

## Alternativas descartadas

- React + React Native: mejor ecosistema, pero menos estructura y más decisiones por equipo.
- Ionic / Capacitor: corre sobre WebView, lo que agrega superficie de ataque.

## Consecuencias

A favor: estructura y seguridad integradas en Angular y compilación nativa en Flutter.

En contra: dos lenguajes, una curva de aprendizaje alta en Angular y migraciones de versión que hay que planificar.
