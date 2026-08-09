# 🐧 DFIR: Interoperabilidade Forense (Conversão de Imagens E01 para Raw/dd)

## 📌 Sobre a Intervenção
Este laboratório demonstra a resolução de um desafio comum em *Digital Forensics*: a incompatibilidade de formatos. Imagens periciais capturadas no formato comprimido proprietário **E01** (Expert Witness Format) frequentemente requerem conversão para formato **Raw (.dd)** para serem analisadas nativamente por ferramentas *open-source* em infraestruturas Linux.

## 🎯 Objetivos Técnicos
* Instalação e operação de ferramentas de virtualização forense em ambiente Linux (Ubuntu).
* Conversão e montagem *on-the-fly* (em tempo real) de contentores E01.
* Preservação da prova pericial sem necessidade de duplicação física e alocação de espaço em disco adicional.

## 🛠️ Stack Tecnológico e Ambiente
* **Sistema Operativo:** Ubuntu Linux (Forensic Workstation)
* **Utilitário Forense:** `xmount` (Virtual File System)
* **Formatos Manipulados:** `.E01` (Origem) -> `.dd` (Destino Emulado)

---

## 📂 Metodologia e Execução

### Fase 1: Isolamento da Evidência
* O ficheiro comprimido `Windows_Evidence_001.E01` foi isolado localmente a partir da diretoria de rede para prevenir bloqueios de leitura durante a montagem do disco virtual.

### Fase 2: Instalação de Dependências (Root)
* Através do terminal Linux, procedeu-se à escalação de privilégios (`sudo su`) e gestão de pacotes via APT para instalação do utilitário vital de emulação: `apt-get install -y xmount`.

### Fase 3: Conversão e Montagem (Virtual File System)
* Utilizou-se o comando de emulação indicando a *flag* de formato de entrada (`--in ewf`).
* O `xmount` leu os cabeçalhos comprimidos do E01 e, sem alterar ou duplicar fisicamente os dados, projetou um ficheiro virtual `Windows_Evidence_001.dd` numa diretoria alvo.
* Este ficheiro virtual `.dd` permite agora que qualquer ferramenta de *Data Carving* baseada em Linux (como The Sleuth Kit ou Foremost) analise o disco como se estivesse perante uma cópia bit-a-bit nativa.
