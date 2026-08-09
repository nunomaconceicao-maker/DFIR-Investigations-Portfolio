# 🛡️ Portefólio de Investigação Forense e Resposta a Incidentes (DFIR)

Bem-vindo ao meu repositório central de Cibersegurança. Este espaço é dedicado à documentação de simulações práticas, exercícios de laboratório e estudos de caso focados em **Digital Forensics**, **Incident Response** e **Análise SOC**.

## 📂 Casos Investigados e Metodologias

Abaixo encontram-se os laboratórios práticos concluídos. Cada estudo de caso contém a metodologia detalhada, os fluxogramas de decisão técnica e os resultados da investigação.

* 🔍 **[Estudo de Caso 1: Ameaça Interna, Ocultação de Dados e Triagem de Malware](./Caso-01-Ameaca-Interna/README.md)**
  * *Técnicas:* Recuperação de blocos não alocados, validação de integridade (Hashes MD5/HMAC) e OSINT.
  * *Cenário:* Fraude financeira e destruição intencional de provas (Shift+Del).

* 🔍 **[Estudo de Caso 2: Processamento Forense e Cadeia de Custódia com Autopsy](./Caso-02-Forense-com-Autopsy/README.md)**
  * *Técnicas:* Ingestão de imagens `.dd`, Data Carving, Análise Hexadecimal (Magic Numbers) e Tagging.
  * *Cenário:* Análise de imagem Raw em ambiente estanque para extração legal de provas.

* 🔍 **[Estudo de Caso 3: Aquisição Forense e Preservação de Evidências (Bit-by-Bit)](./Caso-03-Aquisicao-Imagem-Bit-a-Bit/README.md)**
  * *Técnicas:* Cópia Bit-a-Bit, Captura de Slack Space e Unallocated Space.
  * *Cenário:* Isolamento e duplicação da evidência antes da intervenção técnica para preservar a cadeia de custódia.
 
## 📂 Compreensão e Análise de Sistemas de Ficheiros

* 🔍 **[Estudo de Caso 1: Análise de Sistema de Ficheiros Linux com Autopsy](./Sistemas-Ficheiros/Caso-01-Analise-Linux-Autopsy/README.md)**
  * *Técnicas:* Estrutura Linux, Análise `/etc/passwd`, Extração de Metadados e Cálculo de Hash MD5.
  * *Cenário:* Investigação a um colaborador suspeito de exfiltração de dados num ambiente Linux.
 
* 🔍 **[Estudo de Caso 2: Análise Forense de Imagens Windows (NTFS) via CLI](./Sistemas-Ficheiros/Caso-02-Analise-Windows-TSK/README.md)**
  * *Técnicas:* Linha de Comandos (The Sleuth Kit), Interrogação da MFT (`istat`), Deteção de Evasão e Data Carving (`fls`, `tsk_recover`).
  * *Cenário:* Triagem e recuperação de ficheiros intencionalmente eliminados num sistema Windows através de interrogação direta de baixo nível.

### 🔒 Aquisição e Duplicação de Dados

* 💾 **[Estudo de Caso 1: Aquisição Física e Duplicação Forense Bit-a-Bit (dd CLI)](./Aquisicao-Duplicacao/Caso-01-Aquisicao-Fisica-DD/README.md)**
  * *Técnicas:* PowerShell, Interrogação de Hardware (WMI/CIM), Prevenção de Contaminação e Extração Raw (`dd`).
  * *Cenário:* Captura física de um disco de sistema ativo para um disco forense isolado, mitigando conflitos de enumeração de hardware.

* 💾 **[Estudo de Caso 2: Interoperabilidade Forense em Linux - Conversão de E01 para DD](./Aquisicao-Duplicacao/Caso-02-Conversao-E01-DD/README.md)**
  * *Técnicas:* Linux CLI, Virtual File Systems (`xmount`), Gestão de Evidências Comprimidas (Expert Witness Format).
  * *Cenário:* Emulação em tempo real de uma imagem proprietária E01 para formato Raw/dd sem duplicação física, preparando o ambiente para análise open-source.
 
* 💾 **[Estudo de Caso 3: Montagem Forense de Sistemas de Ficheiros (NTFS e HFS+) em Linux](./Aquisicao-Duplicacao/Caso-03-Montagem-Imagens-Linux/README.md)**
  * *Técnicas:* Linux CLI, Prevenção de Contaminação (`-o ro`), Dispositivos de Bloco Virtuais (`losetup`), Análise de Artefactos Ocultos.
  * *Cenário:* Montagem de evidências *raw* para extração lógica e inspeção de ficheiros sensíveis do macOS (HFS+) e Windows (NTFS), assegurando a integridade pericial.
---
*Construído com foco na adoção de boas práticas da indústria e standards de investigação digital.*
