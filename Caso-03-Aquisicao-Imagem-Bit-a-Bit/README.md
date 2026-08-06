# 💾 DFIR: Aquisição Forense e Preservação de Evidências (Bit-by-Bit Copy)

## 📌 Sobre o Projeto
Este repositório documenta a primeira e mais crítica fase de qualquer investigação de *Digital Forensics and Incident Response* (DFIR): a **Aquisição de Dados**. 

O objetivo deste estudo de caso é demonstrar o procedimento correto para criar uma imagem forense exata (cópia bit-a-bit) de um suporte físico de armazenamento, garantindo que a evidência original é isolada e preservada de modificações acidentais ou intencionais durante a análise.

## 🎯 Objetivos Técnicos
* **Aquisição Forense:** Criar uma réplica exata (*bit-stream image*) de uma partição de disco.
* **Preservação da Cadeia de Custódia:** Garantir que o trabalho de análise subsequente é realizado exclusivamente sobre a cópia forense, protegendo a prova original (Princípio de Locard).
* **Captura de Dados Ocultos:** Assegurar que a imagem recolhe o espaço não alocado (*unallocated space*) e os fragmentos residuais (*slack space*), essenciais para a recuperação de dados eliminados.

## 🛠️ Stack Tecnológico e Ambiente
* **Ferramenta de Imagem:** R-Drive Image (Software comercial de imaging / *Disaster Recovery*)
* **Tipo de Aquisição:** Cópia Física/Lógica Bit-a-Bit (`.rdr`)
* **Ambiente de Laboratório:** Máquina Virtual Windows estanque.

---

## 📂 Metodologia de Aquisição

```mermaid
graph TD
    A[1. Isolamento da Evidência Original] --> B[2. Preparação do Disco de Destino Forense];
    B --> C[3. Configuração da Ferramenta de Imaging];
    C --> D{Seleção do Tipo de Cópia};
    D -->|Cópia Lógica Simples| E[Ignorado - Inadequado para Forense];
    D -->|Cópia Bit-a-Bit| F[4. Captura de Todos os Setores];
    F --> G[Unallocated Space];
    F --> H[Slack Space];
    F --> I[MFT e Ficheiros Ativos];
    G --> J((5. Geração do Ficheiro de Imagem));
    H --> J;
    I --> J;
    
    classDef default fill:#f9f9f9,stroke:#333,stroke-width:2px;
    classDef highlight fill:#e1f5fe,stroke:#0288d1,stroke-width:2px;
    class F highlight;
    class J highlight;
