# 🔍 DFIR: Processamento Forense e Cadeia de Custódia com Autopsy

## 📌 Scenario
Emular um processo formal de investigação digital focado na gestão estrita da cadeia de custódia e na preparação legal de provas. A intervenção requereu a ingestão de uma imagem física (*raw*) num ambiente estanque, a recuperação de dados intencionalmente eliminados e a validação técnica de artefactos a baixo nível. Para contornar limitações de licenciamento em laboratórios isolados, a investigação foi conduzida recorrendo à plataforma *open-source* **Autopsy** (baseada no *The Sleuth Kit*), garantindo a aplicação dos *standards* de análise da indústria.

## 🎯 Objectives
* Executar a ingestão da imagem *Raw* (`.dd`) assegurando a integridade pericial da prova original.
* Proceder ao *Data Recovery*, identificando e extraindo artefactos eliminados através da reconstrução estrutural do sistema de ficheiros (NTFS).
* Conduzir inspeções de baixo nível para validação de cabeçalhos hexadecimais (*Magic Numbers*) e abstração de texto puro (*Data Carving*).
* Gerar documentação forense auditável através de etiquetagem estruturada (*Tagging*) e compilação automatizada de um Relatório Pericial.

## 🧾 Evidence
* **Tipo:** Imagem Física Bit-a-Bit de Sistema
* **Origem/Identificador:** `Evidence_001.dd`
* **Estado:** Preservada, acoplada em software pericial (*Read-Only* lógico)

## 🛠️ Environment & Tools
* **Plataforma Forense:** Autopsy (*Open-Source*)
* **Sistema Operativo:** Microsoft Windows (Máquina Virtual isolada)

---

## 🔬 Methodology
1. **Configuração de Caso Pericial:** Criação de uma base de dados dedicada no Autopsy, estabelecendo os metadados do investigador e o isolamento do processo.
2. **Ingestão e Processamento:** Adição do ficheiro de evidência (`.dd`) como fonte de dados (*Data Source*), permitindo que o *ingest module* nativo processasse automaticamente o sistema de ficheiros NTFS.
3. **Triagem de Dados (Triage):** Segregação analítica da visualização. Foi dada prioridade de análise a blocos órfãos e ao diretório `$Recycle.Bin` em busca de ficheiros com o marcador de "eliminados".
4. **Validação Técnica (Data Examination):** Inspeção individual de artefactos críticos utilizando os painéis de vista Hexadecimal e Textual.
5. **Classificação e Reporte:** Etiquetagem (*Tagging*) das descobertas relevantes e geração automatizada de um relatório pericial final em formato HTML, agregando todos os *MAC times* e hashes dos dados selecionados.

---

## 🔎 Analysis

### Evidence 01 (Estrutura NTFS e Ficheiros Eliminados)
Após a fase de *parsing*, o motor do Autopsy reconstruiu e exibiu a árvore do sistema de ficheiros, destacando visualmente os artefactos cujos apontadores na *Master File Table* (MFT) se encontravam marcados como eliminados, mas cujos setores físicos ainda não haviam sido reescritos.

### Evidence 02 (Análise de Baixo Nível)
A interrogação direta (Hex View) aos cabeçalhos binários dos ficheiros suspeitos marcados durante a triagem evidenciou as *File Signatures* (Magic Numbers) gravadas diretamente no disco, independentemente das extensões atribuídas pelo sistema operativo.

### Evidence 03 (Marcadores Forenses)
A indexação interna do Autopsy associou com sucesso cada ficheiro ao seu correspondente *hash* original, atributos e caminhos absolutos, concentrando-os num *dataset* através das *Tags* aplicadas pelo analista.

## 🧩 Findings
* **Finding 1 (Rastreabilidade e Recuperação Eficaz):** A capacidade do The Sleuth Kit de processar a arquitetura NTFS provou que os dados reportados pelo sistema operativo como "eliminados" (removidos da visualização gráfica do utilizador) persistem no disco físico. A recuperação destes blocos órfãos permitiu ressuscitar e ler dados destruídos de forma intencional pelo suspeito.
* **Finding 2 (Validação vs. Evasão):** O cruzamento das extensões visíveis com a inspeção hexadecimal garante a correta tipificação do artefacto. Qualquer discrepância identificada no *Hex View* indicaria de imediato uma tentativa ativa de ofuscação de provas (anti-forense).
* **Finding 3 (Garantia de Admissibilidade):** O uso da arquitetura de *Tagging* converte dados estáticos em provas processáveis. O relatório HTML gerado comprova, de forma mecânica e sem erro humano de transcrição, o histórico de localização e a assinatura do artefacto recuperado, assegurando a sua viabilidade numa potencial barra de tribunal.

---

## ⚠️ Forensic Considerations
* **Preservação por Design:** A ingestão da imagem estática `.dd` no Autopsy garante que o motor forense atua como um bloqueador de escrita lógico (*write-blocker* virtual). Não existem modificações, atualizações de *timestamps* ou contaminação dos dados durante as fases de triagem e *Data Carving*.
* **Automação do Registo de Custódia:** O relatório forense extraído não é mero detalhe estético; atua como o garante legal da Cadeia de Custódia, associando irreversivelmente a prova extraída à imagem `.dd` primária que lhe deu origem.

## 📝 Conclusion
A simulação demonstrou a proficiência no processamento ponta a ponta (*end-to-end*) de uma imagem forense. A plataforma Autopsy permitiu a transição fluida desde a ingestão crua de bits até à recuperação de dados eliminados e visualização técnica avançada. A geração automatizada do relatório consolidou a investigação, garantindo que o ciclo rigoroso exigido por uma intervenção de *Digital Forensics* foi perfeitamente executado num ambiente *open-source*.

---

## 📊 Fluxo da Investigação

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
    class I highlight;
