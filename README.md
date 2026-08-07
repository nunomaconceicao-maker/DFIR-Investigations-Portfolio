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

---
*Construído com foco na adoção de boas práticas da indústria e standards de investigação digital.*
