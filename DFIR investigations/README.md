# 🛡️ DFIR Investigations

Bem-vindo ao meu portefólio de **Digital Forensics, Incident Response e Security Operations**.

Este repositório documenta a minha evolução prática e técnica na área de DFIR através de laboratórios, simulações de investigação e resolução de casos de estudo avançados. O objetivo central não é apenas demonstrar a proficiência na utilização de ferramentas, mas sim documentar uma **metodologia de investigação rigorosa** baseada na preservação de evidências, aquisição, exame, correlação de artefactos e elaboração de conclusões técnicas sólidas.

🟢 **Status:** Portefólio ativo e em desenvolvimento contínuo.

---

## 🧠 Áreas de Competência

### 🔎 Digital Forensics & Anti-Forensics
* **Aquisição e Preservação:** Duplicação forense, gestão rigorosa da cadeia de custódia e verificação de integridade (Hashes).
* **Defeating Anti-Forensics:** Identificação de técnicas de evasão, deteção de esteganografia, Alternate Data Streams (ADS) e análise de ficheiros ofuscados/empacotados.
* **File System Forensics:** Interoperabilidade Windows/Linux, análise de partições NTFS/HFS+, interrogação de artefactos de baixo nível.
* **Recuperação de Dados:** File Carving avançado, análise de espaço não alocado (Slack Space) e impacto da tecnologia TRIM (SSDs) na recuperação forense.
* **Análise de Imagens:** Tratamento de imagens RAW/DD e E01 (Expert Witness Format).

### 🚨 Incident Response & Security Operations
* **Evidence Triage:** Identificação rápida de indicadores de compromisso (IoCs) e investigação de atividade suspeita.
* **Timeline Analysis:** Reconstrução cronológica da atividade do sistema e correlação de eventos.
* **Análise de Malware (Básica):** Identificação de *File Extension Mismatch* e *unpacking* de binários suspeitos.

### 💻 Virtualização, Análise Dinâmica e Scripting
* **Ambientes Isolados:** Configuração de laboratórios seguros (Network Isolation) para análise dinâmica de sistemas.
* **Ferramentas de Virtualização:** VMware Workstation, Hyper-V, QEMU / qemu-img / xmount.
* **Automação & Scripting:** Python, Bash e PowerShell para automação de tarefas forenses repetitivas; domínio da Linux CLI.

---

## 🧰 Ferramentas Trabalhadas

O ecossistema de ferramentas deste repositório cresce à medida que novos casos são investigados. As ferramentas estão agrupadas pelas suas funções principais:

| Categoria | Ferramentas |
|---|---|
| **Plataformas de Análise** | Autopsy, The Sleuth Kit (TSK) |
| **Aquisição & Montagem** | dd / dcfldd, xmount, qemu-img, losetup |
| **Recuperação & Carving** | PhotoRec, Foremost, TestDisk, Active@ Partition Recovery |
| **Análise Hexadecimal & Evasão** | HxD, Stegsolve, OpenStego, Sysinternals (Streams), Detect It Easy (DIE) |
| **Password Cracking** | Hashcat, John the Ripper, Passware Kit |
| **Ambientes & Automação** | VMware Workstation, PowerShell, WMI, Bash, Python |

---

## 🔬 Metodologia de Investigação

As investigações documentadas neste repositório procuram seguir estritamente o fluxo estruturado e normalizado pela indústria forense:

```text
[ Identification ]
        ↓
[ Preservation ]
        ↓
[ Acquisition ]
        ↓
[ Integrity Verification ]
        ↓
[ Examination ]
        ↓
[ Analysis ]
        ↓
[ Timeline Reconstruction ]
        ↓
[ Evidence Correlation ]
        ↓
[ Findings ]
        ↓
[ Reporting ]
