# 🔍 DFIR: Processamento Forense e Cadeia de Custódia com Autopsy

## 📌 Sobre o Projeto
Este repositório documenta um cenário prático de investigação forense focado na **Cadeia de Custódia e Apresentação de Provas**. O objetivo consistiu na ingestão de uma imagem de disco bit-a-bit (formato `.dd`), recuperação de dados intencionalmente eliminados e validação técnica da evidência, culminando na geração de um relatório pericial.

Para contornar limitações de licenciamento de ferramentas comerciais em ambientes isolados, a investigação foi conduzida recorrendo ao **Autopsy** (plataforma open-source baseada no *The Sleuth Kit*), o padrão da indústria para análise forense digital.

## 🎯 Objetivos Técnicos
* **Processamento de Evidências:** Ingestão de imagem Raw (`.dd`) mantendo a integridade da prova.
* **Data Recovery:** Identificação e extração de artefactos eliminados e reconstrução da tabela do sistema de ficheiros (NTFS).
* **Análise de Baixo Nível:** Inspeção ao nível Hexadecimal para validação de cabeçalhos (Magic Numbers) e visualização de texto bruto (Data Carving).
* **Documentação Legal:** Etiquetagem estruturada de provas (*Tagging*) e geração automatizada de Relatório Pericial em HTML.

## 🛠️ Ferramentas Utilizadas
* **Plataforma Forense:** Autopsy (Open-Source)
* **Tipo de Evidência:** Imagem Forense (`Evidence_001.dd`)
* **Ambiente:** Máquina Virtual Windows (Isolada)

---

## 📂 Metodologia de Investigação

```mermaid
graph TD
    A[1. Criação do Caso Forense] --> B[2. Ingestão da Imagem .dd];
    B --> C[3. Parsing do Sistema de Ficheiros NTFS];
    C --> D{Triagem de Dados};
    D -->|Ficheiros Ativos| E[Análise Padrão];
    D -->|Ficheiros Eliminados| F[Inspeção de Deleted Files / $Recycle.Bin];
    F --> G[4. Validação Hexadecimal e Textual];
    E --> G;
    G --> H[5. Etiquetagem de Evidências / Tagging];
    H --> I((6. Geração de Relatório Pericial HTML));
    
    classDef default fill:#f9f9f9,stroke:#333,stroke-width:2px;
    classDef highlight fill:#e1f5fe,stroke:#0288d1,stroke-width:2px;
    class F highlight;
    class I highlight;```
