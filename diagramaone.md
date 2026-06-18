graph TD
    %% Entradas del Sistema
    subgraph Entradas ["Fase 1: Disparadores (Dos Entradas)"]
        A1([Merge a Producción]) -->|Webhook| B1{Es un código nuevo?}
        A2([Ejecución Manual en GitHub]) -->|workflow_dispatch| B2[Ingresar: Ruta y Tipo de Plantilla]
    end

    %% Regla de Decisión (Día a Día)
    B1 -->|Sí| C1{¿Tiene etiqueta SIG-Política?}
    C1 -->|Sí| D1[Seleccionar: politica.md]
    C1 -->|No| D2[Seleccionar: procedimiento.md]

    %% Motor Central
    subgraph Motor ["Fase 2: Motor Central (GitHub Actions)"]
        D1 --> E[Descargar Código Modificado]
        D2 --> E
        B2 --> F[Descargar Código Legacy de la Ruta]
        
        E --> G[Script: generador_sig.py]
        F --> G
        
        G -->|Lee código y plantilla| H(Autenticación GCP)
        H --> I[Enviar Prompt a Vertex AI]
        I -->|Retorna| J[Archivo Markdown generado]
        
        J --> K[Ejecutar Pandoc]
        K -->|Convierte formato| L[Archivo .docx generado]
        
        L --> M[Script: upload_drive.py]
    end

    %% Salida
    subgraph Salida ["Fase 3: Entrega SIG"]
        M --> N(Autenticación Google Drive)
        N --> O[(Carpeta Compartida SIG en Drive)]
    end

    %% Estilos
    style A1 fill:#2ea44f,stroke:#fff,color:#fff
    style A2 fill:#0366d6,stroke:#fff,color:#fff
    style O fill:#f9ab00,stroke:#fff,color:#fff
