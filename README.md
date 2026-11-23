# Registro de Ventas en Solana · Entrega final Advanced

**Autor:** Fernando Benítez Plata
**Correo de registro:** fernandobenpla@gmail.com
**Ruta elegida:** Proyecto guiado con una mejora funcional
**Versión de partida:** [Registro-Ventas-Solana-PDA](https://github.com/WayLearnLatam/Registro-Ventas-Solana-PDA) (programa) y [Registro-Ventas-Solana-App](https://github.com/WayLearnLatam/Registro-Ventas-Solana-App) (frontend), de WayLearn, con las cinco operaciones del registro (crear registro, crear cliente, registrar venta, corregir importe y devolución).

## Propósito

Aplicación para gestionar un registro de negocio, sus clientes y sus ventas en Solana Devnet. Cada entidad vive en su propia cuenta PDA: **Registro → Cliente → Venta**. El importe de una venta es un dato del ejercicio: no transfiere SOL ni tokens.

## Despliegue en Devnet

- **Program ID:** `GrsZycN4DeohPL5RosA8wQ5s8ZCEeusJUDKkxPUAo4WF`
- **Explorer:** https://explorer.solana.com/address/GrsZycN4DeohPL5RosA8wQ5s8ZCEeusJUDKkxPUAo4WF?cluster=devnet

## Base y aporte propio

**Base (material de WayLearn):** las cinco operaciones del registro (crear registro, crear cliente, registrar venta, corregir importe y devolución) y la **baja de cliente** del día 5, que el programa de ejemplo no incluía y completé siguiendo la consigna (`dar_baja_cliente`: cierra la cuenta del cliente y lo quita del índice del registro).

**Mi aporte funcional:** una **validación de negocio en Rust** sobre el importe de las ventas (`MONTO_MAXIMO = 1_000_000`). `registrar_venta` y `cambiar_monto` rechazan con `MontoInvalido` (6002) los importes iguales a 0 o mayores al máximo. La regla vive en el programa; el frontend no la bloquea, para que el rechazo lo emita la cadena. Además, la baja de cliente rechaza con `ClienteConVentas` (6003) si el cliente aún tiene ventas activas.

**Dónde está:**

| Cambio | Archivo |
| --- | --- |
| Constante `MONTO_MAXIMO` y validación en `registrar_venta` y `cambiar_monto` | `programa/src/lib.rs` |
| Errores 6002 y 6003, instrucción `dar_baja_cliente` y contexto `EliminarCliente` | `programa/src/lib.rs` |
| Instrucción y errores en el IDL | `idl.json` |
| Función `darBajaCliente` | `src/solana/acciones.ts` |
| Pestaña «Baja de cliente» y texto de ayuda del importe | `src/components/Registro.tsx` |
| Pruebas automatizadas | `tests/integracion-cliente.test.ts` |

## Cómo se comprueba

| Caso | Pasos | Resultado esperado |
| --- | --- | --- |
| Venta válida | Registrar venta con importe 50 | Se confirma la transacción |
| **Rechazo por importe** | Registrar o corregir una venta con importe 2000000 | Error `MontoInvalido: el importe debe ser mayor a 0 y no superar 1,000,000` |
| **Rechazo de baja** | Dar de baja un cliente que tiene una venta activa | Error `ClienteConVentas: el cliente aún tiene ventas activas` |
| Baja válida | Hacer la devolución de sus ventas y luego dar de baja al cliente | Se confirma; el cliente desaparece de la lista y su cuenta se cierra |

## Estructura del repositorio

| Ruta | Contenido |
| --- | --- |
| `programa/src/lib.rs` | Programa Rust + Anchor (con mi aporte) |
| `src/` | Frontend React + Vite + TypeScript |
| `idl.json` | IDL del programa |
| `clients/js/` | Cliente generado con Codama (no se edita a mano) |
| `src/config/programa.json` | Program ID y configuración |
| `evidencias/` | Capturas y enlaces de transacciones |

## Configuración y ejecución

Requisitos: Node.js 22.12+ (rama 22), wallet de navegador compatible con Solana y SOL de prueba en Devnet ([faucet](https://faucet.solana.com/)).

```bash
git clone https://github.com/fernandobenpla-lang/registro-ventas-solana
cd registro-ventas-solana
npm install
npm run cliente
npm run dev
```

Abre `http://localhost:5173` en un navegador con tu wallet instalada (también funciona en GitHub Codespaces, puerto 5173).

Verificación automática (TypeScript, pruebas y build; no envía transacciones):

```bash
npm run verificar
```

## Recorrido manual en Devnet

1. Conecta tu wallet en Devnet.
2. Crea un registro (negocio).
3. Crea un cliente.
4. Registra una venta.
5. Corrige el importe.
6. Prueba el rechazo por importe (2000000).
7. Prueba el rechazo de la baja (cliente con venta activa).
8. Haz la devolución de la venta.
9. Da de baja al cliente.

## Evidencias

| Operación | Evidencia |
| --- | --- |
| Registro, cliente y venta creados | captura en `evidencias/` |
| Devolución | [Ver transacción en Explorer](https://explorer.solana.com/tx/5ATS3YBRp4BK2Hntw15xsSuX3kC1Vc5FPwXGZxPRJ5mg3n4Kx4XoGPLEqBqVkVPBacLNZM65pcb8D8J2YdiUkYr5?cluster=devnet) |
| Baja de cliente (válida) | [Ver transacción en Explorer](https://explorer.solana.com/tx/4595n6nHYzoM5YqtzVbmJBgJJnB8gA23jFvYEugpt594hMbffuFsnHsPVw54FK1DUxmArtVuSN22E24BegQHgAEN?cluster=devnet) |
| Rechazo por importe | captura en `evidencias/` |
| Rechazo de baja | captura en `evidencias/` |

## Uso de IA

Usé IA (Claude) como apoyo para escribir el cambio en Rust, la integración en el frontend y las pruebas. Revisé el código, ejecuté `npm run verificar` y comprobé cada caso en Devnet.

## Alcance

Proyecto educativo para Devnet. Las validaciones del formulario no sustituyen las del programa; antes de producción se requiere una revisión de seguridad.
