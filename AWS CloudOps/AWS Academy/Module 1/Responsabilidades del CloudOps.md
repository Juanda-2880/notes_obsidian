#AWS #Cloud #Contexto 

![[Responsabilidades del CloudOps-1780958173719.webp|643]]
### ¿Qué implica ser CloudOps?

El rol de **CloudOps** incluye tareas como **construir, probar, desplegar, monitorear, mantener y asegurar** los sistemas de cómputo. En la nube, esto se logra mediante la **construcción de infraestructura reutilizable** usando **templates** o **scripts**.

---
### Reusabilidad con templates

- Los **templates reutilizables** permiten **automatizar el despliegue de la infraestructura**, evitando configuraciones manuales.
    
- Ejemplo en AWS: **CloudFormation Templates** o **Terraform scripts**.
    
---
### Despliegues automáticos

- Los despliegues automáticos varían según el uso de **scripts, programas o templates**.
    
- Se pueden definir detalles de la infraestructura de dos formas:
    
    - **Declarativa**: describes _qué_ quieres (ej. CloudFormation, Terraform).
        
    - **Programática**: defines _cómo_ construirlo (ej. SDKs, AWS CLI).
        
---
### Infraestructura repetible

- El objetivo es crear **infraestructura repetible y confiable**.
    
- Se puede desplegar la misma infraestructura en **múltiples regiones** o crear un **entorno de pruebas (test environment)** que coincida con el entorno de producción.
    
- Esto asegura consistencia y facilita la escalabilidad.
    
---
## Información valiosa para tu examen

- **Infraestructura como Código (IaC)**: concepto central en CloudOps. Permite versionar, auditar y replicar infraestructuras.
    
- **Herramientas clave en AWS CloudOps**:
    
    - **AWS CloudFormation** → IaC declarativa.
        
    - **Terraform** → IaC multi-cloud.
        
    - **AWS CLI** → despliegues programáticos.
        
    - **AWS CodePipeline** → CI/CD automatizado.
        
    - **Amazon CloudWatch** → monitoreo y alertas.
        
    - **AWS IAM** → seguridad y control de acceso.
        
- **Buenas prácticas CloudOps**:
    
    - Usar **versionado de templates** en repositorios (GitHub).
        
    - Implementar **automatización CI/CD** para despliegues consistentes.
        
    - Configurar **observabilidad** (logs, métricas, alarmas).
        
    - Aplicar **seguridad desde el diseño**