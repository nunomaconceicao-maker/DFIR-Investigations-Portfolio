# 🔍 DFIR: Aquisição Forense e Preservação de Evidências (Bit-by-Bit Copy)

## 📌 Scenario
Esta intervenção documenta a primeira e mais crítica fase de qualquer processo de *Digital Forensics and Incident Response* (DFIR): a **Aquisição de Dados**. O cenário foca-se na necessidade de intervir num suporte físico de armazenamento suspeito. O objetivo primário é executar a imobilização e duplicação da prova através de uma imagem forense exata (cópia *bit-a-bit*), garantindo que a evidência original é isolada, preservada de modificações acidentais ou intencionais, e preparada para análise subsequente em ambiente estanque.

## 🎯 Objectives
* **Aquisição Rigorosa:** Criar uma réplica física/lógica exata (*bit-stream image*) da partição de disco, contornando a abstração do sistema operativo.
* **Captura de Dados Latentes:** Assegurar que a duplicação recolhe a totalidade do espaço não alocado (*Unallocated Space*) e os fragmentos residuais (*Slack Space*), imperativos para a fase de *Data Recovery*.
* **Preservação da Cadeia de Custódia:** Aplicar medidas de controlo para garantir que todo o escrutínio e manipulação técnica futura ocorre exclusivamente sobre a cópia forense gerada (respeitando o Princípio de Troca de Locard).

## 🧾 Evidence
* **Origem:** Suporte físico de armazenamento (Partição/Disco alvo).
* **Formato Resultante:** Imagem Forense Proprietária (`.rdr`).
* **Estado:** Cópia de trabalho preservada em ambiente isolado.

## 🛠️ Environment & Tools
* **Ferramenta de Imaging:** R-Drive Image (Software comercial de *Imaging* / *Disaster Recovery*).
* **Sistema Operativo:** Microsoft Windows (Máquina Virtual estanque).

---

## 🔬 Methodology
1. **Isolamento e Preparação:** Isolamento lógico da evidência original e acoplamento do disco de destino forense (previamente higienizado).
2. **Configuração do Motor de Imagem:** Inicialização da ferramenta R-Drive Image com privilégios administrativos para interagir diretamente a nível de bloco.
3. **Seleção de Parâmetros (Bypass Lógico):** Rejeição explícita da cópia lógica baseada em ficheiros (inadequada para DFIR) em prol da configuração de cópia setor-a-setor (*bit-by-bit*).
4. **Captura Integral:** Execução da leitura de toda a superfície magnética/chips NAND, abrangendo a *Master File Table* (MFT), os dados em utilização, o *Slack Space* e o *Unallocated Space*.
5. **Geração do Contentor:** Despejo dos blocos binários copiados para o ficheiro de imagem de destino.

---

## 🔎 Analysis

> **Nota:** Tratando-se de uma fase estrita de aquisição, a análise incide sobre o contentor pericial gerado e o processo de isolamento, não sobre a extração de artefactos do sistema alvo.

### Evidence 01
A parametrização técnica do R-Drive Image para cópia setor-a-setor gerou um ficheiro de destino (`.rdr`) cujo tamanho reflete a dimensão volumétrica total da partição geométrica, ignorando o "espaço livre" reportado pela API do Windows.

### Evidence 02
A imagem resultante encapsulou blocos físicos que não contêm ficheiros ativos registados na MFT atual.

## 🧩 Findings
* **Finding 1 (O Valor do Bit-a-Bit):** A rejeição da cópia lógica foi vital. O encapsulamento integral dos setores físicos garante que ficheiros previamente eliminados (residentes no *Unallocated Space*) e dados residuais do fecho de blocos (*Slack Space*) transitam com sucesso para o disco forense do laboratório, habilitando as futuras fases de *Data Carving*.
* **Finding 2 (Mitigação do Princípio de Locard):** Ao executar o bloqueio do disco primário e transferir a intervenção para a imagem `.rdr`, a equipa anula o Princípio de Troca de Locard (onde cada interação com o sistema deixa e leva um vestígio), garantindo que a prova matricial nunca sofre *mount timestamps* ou atualizações de metadados indesejadas.

---

## ⚠️ Forensic Considerations
* **Formatos Proprietários vs. Open-Source:** A ferramenta R-Drive Image gera contentores com a extensão proprietária `.rdr`. Apesar de eficiente para *Disaster Recovery*, em DFIR isto introduz um desafio de interoperabilidade. A posterior análise em ferramentas *open-source* (como o Autopsy ou The Sleuth Kit) exigirá procedimentos adicionais de conversão ou montagem em Virtual File Systems (VFS) para expor a matriz *Raw/dd*.
* **Assinatura e Integridade:** O padrão da indústria dita que, no momento imediato à conclusão da barra de progresso da ferramenta de cópia, devem ser gerados os hashes MD5 e SHA-256 do disco físico de origem e da imagem gerada, sendo as suas assinaturas documentadas no relatório de apreensão.

## 📝 Conclusion
A fase de aquisição forense foi conduzida em rigorosa conformidade. A cópia exata do suporte garantiu a preservação da totalidade da superfície de dados, imobilizando o estado da evidência no momento da recolha e viabilizando a condução segura e destrutiva de investigações analíticas no ambiente *Sandboxed* da equipa.

---

## 📊 Fluxo da Metodologia de Aquisição

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
