# ❄️ FrigoAgent: Agente Autónomo de Diagnóstico y Mantenimiento en Refrigeración Industrial

Proyecto desarrollado para la asignatura **Ingeniería de Soluciones con IA (ISYD101)** - Evaluación Parcial N°2.

## 📋 Descripción del Flujo Organizacional
En plantas frigoríficas y centros de distribución agroindustrial, las desviaciones térmicas o fallas en compresores ponen en riesgo la cadena de frío en cuestión de minutos. **FrigoAgent** automatiza la supervisión de telemetría IoT, el diagnóstico técnico basado en manuales corporativos (RAG) y la emisión de Órdenes de Trabajo (OT) priorizadas según SLA.

## 🏗️ Arquitectura y Diagrama de Orquestación de Componentes (IE8)

```mermaid
graph TD
    User([Operador / Supervisor de Planta]) -->|Consulta o Alerta| Agent[Orquestador: LangChain Tool-Calling Agent]
    
    subgraph Memoria y Contexto
        MemShort[(Memoria Corto Plazo: ChatMessageHistory)]
        MemLong[(Memoria Semántica RAG: FAISS VectorStore)]
        Docs[Manuales Técnicos y SLA] --> Embeddings[Google GenAI Embeddings] --> MemLong
    end
    
    subgraph Herramientas del Agente - IE1
        T1[🔍 Consulta: consultar_telemetria_camara]
        T2[🧠 Razonamiento: calcular_criticidad_y_riesgo]
        T3[📚 Recuperación RAG: buscar_protocolo_tecnico]
        T4[✍️ Escritura: registrar_orden_trabajo]
    end

    Agent <-->|Continuidad de Flujo IE3| MemShort
    Agent <-->|Razonamiento LLM| LLM[Google Gemini 1.5 Flash]
    
    Agent -->|Paso 1: Lee Sensores| T1
    T1 --> DB_IoT[(Simulador Sensores IoT)]
    
    Agent -->|Paso 2: Evalúa Desviación| T2
    
    Agent -->|Paso 3: Busca Solución IE4| T3
    T3 <--> MemLong
    
    Agent -->|Paso 4: Decide según SLA IE5| Decision{¿Requiere OT?}
    Decision -->|Prioridad Alta/Crítica| T4
    Decision -->|Estado Normal| Report[Solo informa normalidad]
    T4 --> JSON[(ordenes_trabajo.json)]
