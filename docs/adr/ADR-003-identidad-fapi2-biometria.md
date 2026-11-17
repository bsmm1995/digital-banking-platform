# ADR-003: OAuth 2.0 con FAPI 2.0, Keycloak, Entra ID y biometría

Estado: Propuesto. Fecha: 2026-11-15.

## Contexto

Los clientes entran por web y móvil, con un riesgo creciente de robo de tokens, automatización con IA y deepfakes. Los empleados necesitan acceso corporativo con SSO.

## Decisión

Authorization Code + PKCE con el perfil FAPI 2.0 (PAR, DPoP y step-up). Keycloak 26.8 para clientes y Microsoft Entra ID para empleados, por ser el IdP corporativo más difundido en la banca del país. Cualquiera de los dos se puede reemplazar por el IdP que el banco ya tenga, después de evaluar estándares, costo y continuidad del proveedor. En el onboarding, KYC con prueba de vida certificada (ISO 30107-3 y CEN/TS 18099); en el ingreso diario, passkeys con la biometría del teléfono.

## Alternativas descartadas

- Amazon Cognito: soporte limitado de FAPI 2.0 y poca adopción en banca.
- Contraseña + SMS OTP: no resiste phishing.

## Consecuencias

A favor: un token robado no sirve fuera del dispositivo, la biometría no sale del teléfono y la arquitectura depende de estándares, no de un producto.

En contra: Keycloak hay que operarlo en alta disponibilidad y mantenerlo actualizado.
