# prueba-de-sitio-web
Fragmento de código
graph TD
    subgraph EDGE ["Nivel Edge (Parcelas)"]
        ESP32["40 Nodos ESP32 + Kits"] -->|LoRaWAN| Gateway["Gateway LoRa Local"]
    end

    subgraph FOG ["Nivel Fog (On-Premise - Servidores HP UTC)"]
        Gateway --> ChirpStack["ChirpStack / Broker MQTT"]
        ChirpStack --> TimescaleDB[("TimescaleDB (Series de Tiempo)")]
        TimescaleDB --> SyncDaemon["Daemon Sincronizador"]
        TimescaleDB --> TinyML["Inferencia IA Local (XGBoost)"]
    end

    subgraph CLOUD ["Nivel Cloud (AWS)"]
        SyncDaemon -.->|Retry + Circuit Breaker| APIGW["Amazon API Gateway"]
        APIGW --> Cognito["Amazon Cognito (OAuth 2.1)"]
        APIGW --> RDS[("Amazon RDS PostgreSQL")]
        S3[("Amazon S3 (Datasets/Modelos)")] --> SageMaker["Entrenamiento IA"]
    end

    subgraph CLIENTS ["Consumidores"]
        AppMobile["App Móvil Flutter (Offline-First)"] <--> APIGW
        Dashboard["Panel Web Investigadores"] <--> APIGW
        Aliados["APIs INIAP / GAD"] <--> APIGW
    end
agrismart-latacunga-backend/
├── .github/
│   └── workflows/
│       ├── ci-cd.yml                 # Pipeline SaaS (GitHub Actions)
│       └── security-scan.yml         # Análisis estático SAST
├── config/
│   ├── openapi-spec.yaml             # Contrato de API Gateway
│   └── mosquitto.conf                # Configuración Broker MQTT
├── docker/
│   ├── docker-compose.fog.yml        # Despliegue en Servidores HP (Fog)
│   └── docker-compose.cloud.yml      # Despliegue en AWS EC2 (IaaS)
├── src/
│   ├── domain/                       # Lógica de Negocio (Hexagonal)
│   ├── infrastructure/               # Adaptadores (PostgreSQL, MQTT, S3)
│   └── sync/
│       └── sync_daemon.py            # Sincronizador Fog-Cloud (Store-and-Forward)
├── .env.example                      # Plantilla de variables de entorno (sin secretos)
├── .gitignore                        # Exclusión estrita de claves y tokens
└── README.md                         # Documentación principal
