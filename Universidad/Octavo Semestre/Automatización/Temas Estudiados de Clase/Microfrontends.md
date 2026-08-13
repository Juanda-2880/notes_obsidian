#Auto #PlatsII #Contexto #Universidad #Microfrontends

### ¿Qué es un Microfrontend?

En el desarrollo de software moderno, la arquitectura de **microservicios** revolucionó el _backend_ al dividir aplicaciones monolíticas en servicios independientes y desacoplados. Sin embargo, durante años el _frontend_ se mantuvo como un único bloque monolítico (_Frontend Monolith_), convirtiéndose en el cuello de botella del ciclo de desarrollo.

  

Un **Microfrontend** es la extensión del concepto de microservicios a la capa de presentación o interfaz de usuario (_UI_). Es un patrón arquitectónico donde una aplicación web se divide en módulos o funciones independientes desarrollados, probados y desplegados de manera autónoma por diferentes equipos de trabajo.

  

> **Objetivo principal:** Eliminar el acoplamiento entre equipos de desarrollo, reducir la complejidad de la base de código y permitir que cada módulo escale de forma autónoma con su propio ritmo de entregas.
> 
>   

### Organización de la Arquitectura: De Monolito a Organización Vertical

En los enfoques tradicionales, la separación se hace por capas tecnológicas (Backend por un lado, Frontend por otro). Esto genera interdependencias y ralentiza los lanzamientos.

  

Con los microfrontends se adopta la **organización vertical (Vertical Slices)**:

  

- Cada equipo es dueño de una funcionalidad completa de extremo a extremo (_End-to-End_), desde la base de datos y la lógica de negocio en el backend hasta la interfaz gráfica en el frontend.
    
      
    
- La aplicación final vista por el usuario es la composición armónica de estos bloques independientes en tiempo de ejecución (_runtime_).
    
      
    

```
  [ Vista de Usuario: Aplicación Web ]
  ┌─────────────────────────────────┐
  │   Header / Nav (Equipo A)       │
  ├─────────────────┬───────────────┤
  │ Catálogo        │ Carrito       │
  │ (Equipo B)      │ (Equipo C)    │
  └─────────────────┴───────────────┘
```

### Estrategias de Repositorios

Para gestionar el código fuente de una arquitectura de microfrontends existen tres enfoques principales:

  

1. **Monorepository (Monorepo):**
    
      
    - Todo el código de los diferentes proyectos reside en un único repositorio.
        
          
        
    - **Ventajas:** Facilita el intercambio de código, simplifica la gestión de dependencias globales y unifica el tooling (usando herramientas como _Nx_ o _Turborepo_).
        
          
        
    - **Ejemplo:** La estructura por defecto de _workspaces_ en Angular.
        
          
        
2. **Multirepository (Polyrepo):**
    
      
    - Cada microfrontend tiene su propio repositorio independiente.
        
          
        
    - **Ventajas:** Aislamiento total de código, permisos de acceso restringidos y pipelines de CI/CD independientes para cada equipo.
        
          
        
3. **Metarepository (Metarepo):**
    
      
    - Estrategia híbrida que utiliza repositorios individuales para cada microfrontend, sumados a un repositorio central ("orquestador") que los integra y coordina en tiempo de ejecución.
        
          
        

### Principios e Ideas Centrales

- **Agnosticismo Tecnológico:** Cada equipo puede elegir el _stack_ tecnológico que mejor se adapte a su módulo (React, Angular, Vue, etc.) sin depender de las decisiones de otros equipos.
    
      
    
- **Aislamiento de Código y Estado:** No se comparten variables globales ni estados directamente en ejecución. La falla de un microfrontend no debe colapsar la aplicación completa (_Degradación Grácil_).
    
      
    
- **Despliegues Autónomos:** Un cambio en el microfrontend de "Carrito de compras" no requiere volver a compilar o desplegar el módulo de "Perfiles de usuarios".
    
      
    

### Integración Técnica y Frameworks

#### 1. Webpack 5 Module Federation

Es la tecnología clave para implementar microfrontends modernos. Permite a una aplicación JavaScript cargar código compilado de manera remota desde otra aplicación en tiempo de ejecución (_runtime_), compartiendo dependencias comunes (como React o Lodash) para evitar descargas duplicadas en el navegador.

  

#### 2. Microfrontends con React

- **Ventajas:** Su arquitectura basada en componentes, su DOM virtual y su ecosistema modular lo hacen ideal para la integración remota.
    
      
    
- **Desafío:** La carga de múltiples instancias o dependencias sin optimizar puede incrementar el peso del _bundle_ descargado por el usuario. Es indispensable usar _Module Federation_ y _Lazy Loading_ (carga bajo demanda).
    
      
    

#### 3. Microfrontends con Angular

- **Ventajas:** Proporciona un marco rígido y estandarizado ideal para enfoques de _Monorepo_ a través de _Workspaces_, _Projects_ y _Libraries_.
    
      
    
- **Integración:** Desde Angular 12+, _Module Federation_ se integra de manera nativa o mediante esquemas de herramientas como _Nx_.
    
      
    

#### 4. Microfrontends con Next.js

- **Ventajas:** Permite combinar las ventajas del renderizado del lado del servidor (_SSR_) con la arquitectura de microfrontends (v10.2+ mediante _Module Federation_).
    
      
    

### Ventajas y Desafíos

|**Ventajas**|**Desafíos**|
|---|---|
|**Despliegues independientes:** Menor riesgo al pasar cambios a producción.|**Complejidad operativa:** Requiere una infraestructura robusta de CI/CD y automatización.|
|**Menor tiempo de compilación:** Solo se construye el módulo modificado.|**Mayor consumo de red:** Si no se gestionan bien las dependencias, aumenta el peso de descarga.|
|**Escalabilidad de equipos:** Múltiples células pueden trabajar en paralelo.|**Consistencia visual:** Riesgo de perder la coherencia de diseño si no hay un _Design System_.|

### Buenas Prácticas

1. **Delimitar el tamaño correcto (_Bounded Contexts_):** Aplicar principios de _Domain-Driven Design_ (DDD). Un microfrontend debe responder a una función de negocio clara (ej. "Catálogo", "Checkout", "Soporte"). No atomizar en exceso.
    
      
    
2. **Comunicación desacoplada:** Los microfrontends deben comunicarse mediante un **Event Bus** (eventos personalizados del navegador o _CustomEvents_) o arquitectura Pub/Sub, evitando llamadas directas o acoplamiento por métodos globales.
    
      
    
3. **Uso de un Sistema de Diseño (_Design System_):** Crear una biblioteca central de componentes visuales (botones, tipografía, modales) importada por todos los equipos para mantener la identidad estética.
    
      
    
4. **Orquestación con Meta-Frameworks:** Utilizar orquestadores de tipo SPA como _Single-SPA_ cuando se combinan diferentes frameworks en la misma pantalla.