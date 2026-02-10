---
date: Enero 2023
title: Plantilla ![arc42](images/arc42-logo.png)
---

# 

**Acerca de arc42**

arc42, La plantilla de documentación para arquitectura de sistemas y de
software.

Por Dr. Gernot Starke, Dr. Peter Hruschka y otros contribuyentes.

Revisión de la plantilla: 7.0 ES (basada en asciidoc), Enero 2017

© Reconocemos que este documento utiliza material de la plantilla de
arquitectura arc42, <https://www.arc42.org>. Creada por Dr. Peter
Hruschka y Dr. Gernot Starke.

# Introducción y Metas {#section-introduction-and-goals}

## Vista de Requerimientos {#_vista_de_requerimientos}
Este documento describe la arquitectura de un sistema ERP para una empresa fabricante de zapatos que produce calzado a gran escala y lo vende a empresas comercializadoras (por ejemplo: Nike u otros clientes mayoristas).

El objetivo principal del ERP es integrar y automatizar procesos internos críticos como:

Compras de materias primas e insumos.

Gestión de inventarios (materia prima, producto en proceso y producto terminado).

Control de costos.

Facturación a clientes comerciales.

Gestión de empleados.

Gestión de activos fijos (maquinaria de planta).

Reportes ejecutivos (EIS).

El alcance principal de esta versión se enfoca en el Módulo de Compras, ya que es la base para garantizar disponibilidad de insumos para la fabricación.

Requisitos de negocio principales del Módulo de Compras

Registrar proveedores de materias primas e insumos.

Registrar insumos comprables (cuero, suelas, telas, cordones, pegantes, cajas, etiquetas).

Crear órdenes de compra asociadas a un proveedor.

Permitir aprobación/rechazo de órdenes por un rol responsable.

Registrar recepción total o parcial de una orden de compra.

Actualizar inventario al registrar recepción.

Consultar historial de compras por proveedor, insumo y fechas.

Generar reportes básicos de compras para control de gastos.

## Metas de Calidad {#_metas_de_calidad}
Las metas de calidad más importantes para este sistema son:

Seguridad

Control de acceso por roles (Compras, Bodega, Administrador, Gerencia).

Protección de información financiera y de proveedores.

Disponibilidad

El sistema debe estar disponible durante el horario laboral para operaciones de compras y bodega.

Usabilidad

Interfaz simple para usuarios operativos (compras y bodega).

Formularios claros para reducir errores.

Mantenibilidad

Arquitectura modular que permita agregar módulos futuros (producción, planificación, etc.).

Trazabilidad

Registro histórico de compras y recepción de insumos para auditoría y control.

## Partes interesadas (Stakeholders) {#_partes_interesadas_stakeholders}

|Rol/Nombre|******	                | Contacto|	                   |Expectativas|
|Jefe de Compras| ******            |<Contact-1>|******	        | Control de proveedores, aprobación de órdenes, seguimiento de compras  |
|Analista de Compras|******        | <Contact-2>|******	      |  Registro de proveedores, insumos y creación de órdenes de compra    |
|Auxiliar de Bodega|******	        |<Contact-3>|******	       |  Registrar recepciones de insumos y actualizar stock     |
|Gerente General|******	           | <Contact-4>|******	       |  Reportes de compras, costos y trazabilidad      |
|Administrador del Sistema|******	|<Contact-5>|******	       |  Gestión de usuarios, roles y soporte del sistema    |
|Proveedores|******	                   | N/A	|******             |Órdenes claras, trazabilidad de pedidos y recepción     |
|Clientes Comercializadores| ******   |  N/A|******	          |   Cumplimiento de entregas, calidad y continuidad de producción      |

# Restricciones de la Arquitectura {#section-architecture-constraints}
Restricciones tecnológicas

Backend: Java + Spring Boot (API REST).

Base de datos: PostgreSQL.

Frontend: React (Single Page Application).

Autenticación: JWT.

Documentación de API: Swagger/OpenAPI.

Contenedores: Docker (opcional para despliegue).

Restricciones del proyecto

El sistema debe ser accesible desde navegador.

Debe permitir múltiples usuarios concurrentes.

Debe soportar roles y permisos.

Debe mantener consistencia de inventario al registrar recepciones.

Se debe evitar recepción de cantidades superiores a las ordenadas.
# Alcance y Contexto del Sistema {#section-context-and-scope}
## Contexto de Negocio {#_contexto_de_negocio}
El ERP se utiliza dentro de la fábrica de calzado para controlar procesos internos y conectar el flujo de insumos hacia la producción.

Diagrama o Tabla

Explicación del contexto de negocio

El área de compras registra proveedores, insumos y órdenes de compra.

La bodega registra la recepción de insumos.

Gerencia consulta reportes para toma de decisiones.

Los proveedores son actores externos que suministran materiales.

Los clientes comercializadores reciben productos terminados y generan demanda indirecta de insumos.

**\<Diagrama o Tabla\>**

**\<optionally: Explanation of external domain interfaces\>**

## Contexto Técnico {#_contexto_técnico}
Diagrama o Tabla
Explicación de interfaces técnicas

El frontend web se comunica con el backend mediante HTTP/HTTPS.

El backend se comunica con PostgreSQL mediante conexión JDBC.

Los reportes son generados desde el backend (consultas SQL + lógica de agregación).
**\<Diagrama o Tabla\>**

**\<Opcional: Explicación de las interfases técnicas\>**

**\<Mapeo de Entrada/Salida a canales\>**

# Estrategia de solución {#section-solution-strategy}
La estrategia se basa en una arquitectura moderna de tres capas:

Frontend SPA (React): interfaz para usuarios operativos y administrativos.

Backend API (Spring Boot): lógica del negocio, reglas de validación y seguridad.

Base de datos relacional (PostgreSQL): persistencia consistente y trazable.

Decisiones principales:

Se usa API REST para desacoplar frontend y backend.

Se usa JWT para autenticación y control de acceso.

Se maneja el Módulo de Compras como componente independiente, pero integrado con Inventario.


# Vista de Bloques {#section-building-block-view}

## Sistema General de Caja Blanca {#_sistema_general_de_caja_blanca}

***\<Diagrama general\>***

Motivación

La división en contenedores permite separar responsabilidades, mejorar la mantenibilidad y permitir escalabilidad futura.

Bloques de construcción contenidos

Frontend Web (React SPA)

Backend API (Spring Boot)

Base de datos PostgreSQL

Módulo de Reportes (interno en backend)

Interfases importantes

Frontend ↔ Backend: API REST (JSON).

Backend ↔ Base de datos: SQL/JDBC.

Backend ↔ Reportes: consultas agregadas y exportación.

### \<Caja Negra 1\> {#_caja_negra_1}

Propósito/Responsabilidad
Permite al usuario operar el sistema: registrar proveedores, insumos, crear órdenes, registrar recepción y consultar reportes.

Interfase(s)
Consume endpoints REST del backend.

Requerimientos satisfechos

Registro de proveedores e insumos.

Creación y seguimiento de órdenes.

Recepción de compras.

### \<Caja Negra 2\> {#_caja_negra_2}

Propósito/Responsabilidad
Implementa la lógica del negocio, reglas de validación, control de roles y acceso a datos.

Interfase(s)

REST API hacia frontend.

JDBC hacia PostgreSQL.

Requerimientos satisfechos

Validación de órdenes.

Control de estados.

Seguridad y roles.

### \<Caja Negra N\> {#_caja_negra_n}

Propósito/Responsabilidad
Almacena entidades: usuarios, roles, proveedores, insumos, órdenes de compra, recepciones y stock.

## Nivel 2 {#_nivel_2}

### Caja Blanca *\<bloque de construcción 1\>* {#_caja_blanca_bloque_de_construcción_1}

Componentes internos del Módulo de Compras:

Gestión de proveedores.

Gestión de insumos.

Gestión de órdenes de compra.

Aprobación y estados.

Registro de recepción.

# Vista de Ejecución {#section-runtime-view}

## \<Escenario de ejecución 1\> {#_escenario_de_ejecución_1}

-   *\<Inserte un diagrama de ejecución o la descripción del
    escenario\>*

-  Flujo del escenario

El usuario de compras ingresa al formulario de insumos.

Completa los campos requeridos (nombre, categoría, unidad de medida).

El frontend envía la solicitud al backend.

El backend valida datos obligatorios.

Si es válido, guarda el insumo en PostgreSQL.

El backend responde éxito.

El frontend actualiza la lista del catálogo.

## \<Escenario de ejecución 2\> {#_escenario_de_ejecución_2}

El auxiliar de bodega selecciona una orden aprobada.

Registra cantidades recibidas por cada insumo.

El backend valida que no excedan lo ordenado.

El sistema actualiza stock.

La orden cambia de estado a “Parcial” o “Recibida”.​


# Vista de Despliegue {#section-deployment-view}

## Nivel de infraestructura 1 {#_nivel_de_infraestructura_1}

***\<Diagrama General\>***
El sistema puede desplegarse en un servidor local (on-premise) o en la nube usando contenedores Docker:

Contenedor frontend (Nginx + React build)

Contenedor backend (Spring Boot)

Contenedor PostgreSQL
Motivación

El despliegue con Docker facilita replicar ambientes (desarrollo, pruebas y producción) y simplifica la instalación.

Características de Calidad/Rendimiento

Respuesta rápida en operaciones CRUD (proveedores, insumos, órdenes).

Consistencia fuerte para inventario.

Separación de contenedores mejora mantenibilidad.

    Mapeo de los Bloques de Construcción a Infraestructura

    Frontend React → Servidor Web (Nginx)

Backend Spring Boot → Servidor de Aplicaciones

PostgreSQL → Servidor de Base de Datos


# Conceptos Transversales (Cross-cutting) {#section-concepts}

## *\<Concepto 1\>* {#_concepto_1}

El sistema maneja autenticación con JWT y autorización por roles.

Roles principales:

Compras

Bodega

Administrador

Gerencia

## *\<Concepto 2\>* {#_concepto_2}

Todas las órdenes y recepciones guardan:

Usuario creador

Fecha y hora

Estado

Historial de cambios​

## *\<Concepto n\>* {#_concepto_n}

Las órdenes de compra manejan estados:

Pendiente

Aprobada

Rechazada

Parcial

Recibida

Cancelada

# Decisiones de Diseño {#section-design-decisions}

Se usa arquitectura en contenedores (C4 model).

Se usa API REST para desacoplar frontend y backend.

PostgreSQL como base de datos por consistencia y consultas relacionales.

JWT para autenticación.

Docker para despliegue.
# Requerimientos de Calidad {#section-quality-scenarios}

## Árbol de Calidad {#_árbol_de_calidad}

Seguridad

Disponibilidad

Usabilidad

Mantenibilidad

Trazabilidad
## Escenarios de calidad {#_escenarios_de_calidad}
Seguridad:
Dado un usuario sin rol de compras, cuando intente crear una orden, entonces el sistema debe denegar el acceso.

Usabilidad:
Dado un usuario de bodega, cuando registre recepción, entonces el sistema debe mostrar el saldo pendiente por recibir.

Disponibilidad:
Dado que el sistema está en horario laboral, cuando múltiples usuarios consulten órdenes, entonces el sistema debe responder sin caídas.

Trazabilidad:
Dado un insumo, cuando se consulte historial, entonces debe mostrar en qué órdenes fue comprado y recibido.
# Riesgos y deuda técnica {#section-technical-risks}
Falta de integración futura con módulos de producción y planificación.

Posibles inconsistencias si no se implementan transacciones correctamente al registrar recepciones.

Si el sistema crece, se requerirá optimización de consultas para reportes.

Dependencia de conectividad estable si se despliega en la nube.
# Glosario {#section-glossary}




|Término|	                                            |Definición|
|ERP|                               |Sistema de planificación de recursos empresariales que integra procesos de una empresa en una plataforma.|
|Proveedor|                      |Empresa que suministra materias primas e insumos para fabricar zapatos (cuero, suelas, pegantes, etc.).|
|Insumo|	                           |Material comprado para fabricación, usado en la producción de calzado.|
|Orden de Compra|	                 |  Documento interno que registra una compra a un proveedor con items, cantidades y precios.|
|Recepción|	                      | Registro de la llegada física de insumos comprados al almacén.|
|Stock|	                          | Cantidad disponible en inventario de un insumo o producto.|
|Catálogo de Insumos|                |Lista centralizada de insumos que se pueden comprar.|
|Cliente Comercializador|          |  Empresa que compra zapatos terminados para venderlos al consumidor final (Nike, distribuidores, etc.).|
|Trazabilidad|	                  | Capacidad de rastrear el flujo de insumos desde compra hasta su uso en producción.|
|Unidad de Medida|	              | Forma de cuantificar insumos (metros, unidades, kilos, pares).|
|Estado de Orden|	                  | Situación de una orden (pendiente, aprobada, parcial, recibida, cancelada).|
