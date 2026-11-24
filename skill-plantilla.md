---
name: nombre-de-la-skill
description: Una o dos frases que digan QUÉ hace y CUÁNDO usarla (con palabras que yo diría de verdad). Esta descripción decide si Claude la carga, así que sé concreto.
---

# Nombre de la skill

## Cuándo usarla
- Situaciones en las que debe activarse (ej.: "tengo una trayectoria .xtc y quiero estadísticas de X").
- Cuándo NO usarla (ej.: "no sirve para trayectorias de ADN con otro formato").

## Entradas esperadas
- Archivos o datos que necesita (formato, columnas, unidades).
- Preguntas que debe hacerme si falta algo (máximo una).

## Pasos
1. Paso uno (comprobar la entrada).
2. Paso dos (procesar; indicar scripts o comandos concretos).
3. Paso tres (validar el resultado).

## Salida
- Qué entrega y en qué formato (archivo, figura, tabla, resumen corto).
- Cómo nombra los archivos que genera.

## Reglas y errores comunes
- Supuestos que nunca debe hacer.
- Errores frecuentes y cómo detectarlos.

## Casos de prueba
Prefijo de archivos: `nombre-de-la-skill__`

| Caso | Entrada | Resultado esperado | Estado |
|------|---------|--------------------|--------|
| 01 | `nombre-de-la-skill__caso01_entrada.ext` | ... | pendiente |
| 02 | ... | ... | pendiente |

## Notas de desarrollo
- Versión: 0.1
- Decisiones tomadas y pendientes.
