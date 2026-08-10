# 🔍 DFIR: Virtualização de Imagem Forense para Análise Dinâmica

## 📌 Scenario
Em investigações de resposta a incidentes, a análise estática pode revelar-se insuficiente quando a equipa se depara com dados ofuscados, artefactos voláteis ou aplicações cuja decifragem depende do arranque nativo do sistema operativo. Este laboratório documenta a transição de uma investigação estática para uma abordagem de *Live Forensics* (Análise Dinâmica). O processo envolveu a virtualização de uma **cópia de trabalho** (proveniente de uma imagem física original) num disco virtual arrancável, replicando o ambiente exato do suspeito de forma isolada e contida.

## 🎯 Objectives
* Empregar utilitários de virtualização *open-source* (QEMU) para efetuar a transição da matriz de dados de *Raw* para Virtual.
* Desenhar e implementar um ambiente de *Sandboxing* para contenção absoluta de ameaças adormecidas.
* Inicializar (*boot*) a máquina do suspeito em segurança, extraindo artefactos interativos e contornando a ofuscação da evidência em repouso.

## 🧾 Evidence
* **Origem:** Cópia de Trabalho (*Working Copy*) proveniente de imagem física `.dd`.
* **Formato Intermédio:** `.vmdk` (Virtual Machine Disk)

## 🛠️ Environment & Tools
* **Motor de Transição:** Ubuntu Linux (`qemu-img`)
* **Plataforma de Sandboxing:** VMware Workstation
* **Sistema Alvo Emulado:** Microsoft Windows 10

---

## 🔬 Methodology
1. **Tradução da Arquitetura (QEMU):** Execução do utilitário `qemu-img` sobre o *kernel* Linux (`qemu-img convert -f raw Windows_Evidence_002.dd -O vmdk Windows.vmdk`) para reconstruir os cabeçalhos do contentor hipervisor sem adulterar o *payload* da evidência (partições, MFT).
2. **Design de Contenção (VMware):**
   * **Network Isolation:** O adaptador de rede (*Network Adapter*) foi categoricamente configurado para `Do not use a network connection`. 
   * **Parâmetros de Hardware:** Configuração do *Firmware Type* definida estritamente para **BIOS** (em detrimento de UEFI), garantindo a compatibilidade de arranque (*legacy boot*) associada a discos clonados bit-a-bit.
   * **Limitação de Recursos:** A memória RAM foi limitada a ~1.5GB (1536 MB) estáticos para prevenir o esgotamento do *host* durante a emulação.
3. **Interação de Primeiro Plano:** O disco `.vmdk` foi anexado como disco virtual primário ("*Use an existing virtual disk*"), e a máquina virtual foi inicializada.

---

## 🔎 Analysis

### Evidence 01
O sistema operativo emulado (Windows 10) concluiu o processo de inicialização (*boot sequence*) e atingiu com sucesso o *Lock Screen* interativo, desencadeando o carregamento dinâmico de chaves de registo e processos de sistema em *runtime*.

## 🧩 Findings
* **Finding 1 (Desbloqueio de Extração Dinâmica):** A conversão íntegra dos cabeçalhos estruturais via QEMU permitiu ao *hypervisor* assimilar o dump físico como hardware legítimo. Este passo operacionaliza a extração de artefactos baseados em *interface* de utilizador e permite efetuar *memory dumping* local de processos que requerem a API do Windows para se revelarem.

---

## ⚠️ Forensic Considerations
* **Alteração Inevitável de Artefactos:** O arranque (*boot*) de um sistema operativo causa alterações massivas em metadados, ficheiros temporários, *prefetch* e *event logs*. É imperativo que esta técnica seja conduzida **exclusivamente sobre uma cópia de trabalho**. A imagem `.dd` primária tem de permanecer segregada e inviolada.
* **Isolamento de Rede rigoroso:** Garantir que a placa de rede virtual está completamente desligada fisicamente no *hypervisor* constitui uma medida essencial de controlo em cadeia de custódia. O *Network Isolation* previne a lateralização inadvertida de *malware*, inibe chamadas *Command and Control (C2)* ao exterior e bloqueia a alteração cronológica baseada em Network Time Protocol (NTP).

## 📝 Conclusion
A virtualização da cópia estática para um modelo de emulação dinâmica foi bem-sucedida e operada sob rigorosos parâmetros de contenção. O ambiente *Sandboxed* viabilizou a condução de comportamentos dinâmicos, permitindo o avanço da investigação forense para táticas avançadas de recuperação que estariam bloqueadas num cenário de análise exclusivamente estática.
