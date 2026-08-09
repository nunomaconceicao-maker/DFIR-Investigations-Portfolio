# 🚀 DFIR: Análise Dinâmica e Emulação de Evidências (Raw para VMDK)

## 📌 Sobre a Intervenção
Em investigações forenses, a análise estática pode ser insuficiente quando nos deparamos com dados ofuscados, artefactos em memória ou aplicações cuja decifragem depende do arranque do sistema operativo. Este laboratório documenta o processo de conversão de uma imagem física estática (`.dd`) num disco virtual arrancável (`.vmdk`), permitindo a condução de uma **Análise Dinâmica** em ambiente isolado (*Sandbox*).

## 🎯 Objetivos Técnicos
* Empregar utilitários de virtualização *open-source* (QEMU) para conversão de matrizes de dados.
* Desenhar e implementar um ambiente de *Sandboxing* (VMware Workstation) para contenção da ameaça.
* Executar a inicialização (*boot*) da máquina do suspeito preservando o estado das evidências e garantindo isolamento de rede (*Air-Gapping*).

## 🛠️ Stack Tecnológico e Ambiente
* **Motor de Conversão:** Ubuntu Linux com `qemu-utils` (`qemu-img`)
* **Plataforma de Sandboxing:** VMware Workstation
* **Transição de Formatos:** `RAW / .dd` -> `VMware / .vmdk`

---

## 📂 Metodologia e Execução

### Fase 1: Tradução do Sistema de Ficheiros (QEMU)
Para tornar o dump físico suportável por um *hypervisor* comercial, procedeu-se à manipulação da imagem através do *kernel* Linux:
* Instalação das dependências de virtualização (`apt-get install qemu-utils`).
* Execução do utilitário de conversão indicando o formato original estrito e o formato de saída do hipervisor: 
  `qemu-img convert -f raw Windows_Evidence_002.dd -O vmdk Windows.vmdk`.
* Este processo reconstruiu os cabeçalhos do disco para criar um contentor VMDK funcional, sem adulterar o *payload* de dados (partições, MFT, etc.).

### Fase 2: Configuração da Arquitetura de Contenção (VMware Workstation)
A evidência traduzida foi migrada para uma *workstation* Windows para emulação, cumprindo protocolos estritos de segurança:
* **Isolamento Lógico (Air-Gapping):** O adaptador de rede (*Network Adapter*) da máquina foi categoricamente configurado para `Do not use a network connection`. Este passo é obrigatório em protocolos de *Incident Response* para prevenir lateralização de *malware* adormecido, chamadas de C2 (*Command and Control*) ou alteração de *timestamps* via NTP (*Network Time Protocol*).
* **Parâmetros de Hardware:** Configuração do *Firmware Type* estritamente definida para **BIOS** (em detrimento de UEFI), garantindo a compatibilidade de arranque (*legacy boot*) associada a discos clonados fisicamente.
* A alocação de memória RAM foi limitada a ~1.5GB (1536 MB) estáticos para prevenir o esgotamento de recursos do *host* durante a análise.

### Fase 3: Inicialização e Interação de Primeiro Plano
* O disco `.vmdk` foi anexado como disco virtual primário à máquina ("*Use an existing virtual disk*"), mantendo o seu formato original.
* O sistema operativo alvo (Windows 10) arrancou com sucesso no ambiente emulado. A partir deste estado dinâmico, tornou-se possível extrair artefactos baseados em *interface* e efetuar *memory dumping* local de processos previamente encriptados na evidência em repouso.

---

## 💡 Parecer Técnico e Lições Retiradas
1. **O Valor da Virtualização em DFIR:** O utilitário `qemu-img` demonstra uma flexibilidade ímpar na tradução de arquiteturas de discos, permitindo que imagens estáticas capturadas no terreno sejam rapidamente transformadas em cenários vivos nos SOCs para análise comportamental de artefactos suspeitos.
2. **Rigor no Isolamento de Rede:** O arranque de uma máquina pericial acarreta um risco severo de contaminação. Garantir que a placa de rede virtual está completamente desligada fisicamente no *hypervisor* não é apenas uma boa prática, é um requisito legal em cadeia de custódia para garantir que a evidência não estabelece ligações externas que alterem o seu estado ou comprometam a rede do laboratório.
