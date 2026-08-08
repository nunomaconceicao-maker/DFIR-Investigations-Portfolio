# 💾 DFIR: Aquisição Forense e Preservação de Evidências (Bit-a-Bit)

## 📌 Sobre a Intervenção
Este laboratório documenta o processo crítico de **Aquisição Física (*Physical Acquisition*)** de um sistema ativo. A intervenção focou-se na preservação do estado integral de um disco de sistema Windows através da criação de uma imagem forense *raw* não adulterada, garantindo a recolha exaustiva de dados alocados, espaço não alocado (*Unallocated Space*) e *Slack Space* para futura análise em ambiente estanque.

## 🎯 Objetivos Técnicos da Intervenção
* **Mapeamento de Hardware Subjacente:** Interrogação dos controladores físicos do sistema para identificação precisa da topologia dos discos (Sistema vs. Disco Forense).
* **Prevenção de Contaminação:** Isolamento lógico do destino de cópia para prevenir ciclos de sobrescrita e adulteração da evidência original.
* **Duplicação a Baixo Nível:** Execução de uma clonagem setor a setor contornando a *API* e os bloqueios de segurança do Sistema Operativo.

## 🛠️ Stack Tecnológico e Ambiente de Extração
* **Interface de Interrogação:** Windows PowerShell (Administrador)
* **Motor Forense de Extração:** Utilitário `dd` (*Data Duplicator* CLI)
* **Formato da Evidência Gerada:** Imagem Física Raw / Duplicação Bit-a-Bit (`.dd`)

---

## 📂 Metodologia de Triagem e Resposta

### Fase 1: Mapeamento de Hardware e Prevenção de Corrupção
O processo iniciou-se com a interrogação direta à *motherboard* para mitigar riscos de enumeração lógica do sistema operativo:
* Execução do cmdlet `Get-CimInstance Win32_DiskDrive` no PowerShell para mapear os identificadores de *hardware* (`DeviceID`).
* Foi identificada uma discrepância crítica na ordem de enumeração SCSI/NVMe, confirmando-se que o disco de sistema alvo correspondia ao identificador `PHYSICALDRIVE1`, enquanto o disco secundário (Forensic Disk) assumia a posição `PHYSICALDRIVE0`. 
* Este passo garantiu a proteção contra um ataque de ciclo infinito (*storage loop*), onde o disco tentaria escrever a imagem sobre si mesmo, corrompendo a evidência e esgotando a capacidade do *drive*.

### Fase 2: Escalação de Privilégios e *Bypass* de Permissões
Para contornar o bloqueio nativo do sistema operativo no acesso direto aos setores do disco (Erro: *Access is Denied*):
* A consola de extração foi executada com privilégios máximos de administração (*Run as Administrator*), permitindo que o utilitário `dd` operasse num nível inferior à estrutura de controlo de acessos (ACLs) do Windows.

### Fase 3: Execução da Clonagem Forense
A extração foi processada com parâmetros otimizados para garantir integridade e velocidade:
* O comando de duplicação foi ajustado para apontar para o disco alvo exato (`if=\\.\PHYSICALDRIVE1`) e descarregar a imagem para o disco forense isolado (`of=E:\Windows_Evidence_002.dd`).
* Definiu-se um tamanho de bloco de 512 Kilobytes (`bs=512k`) para maximizar a taxa de transferência de I/O e implementaram-se as *flags* `--size` e `--progress` para monitorização ativa dos registos de entrada e saída, assegurando que não ocorriam perdas de blocos durante a cópia.

---

## 💡 Parecer Técnico e Lições Retiradas
1. **O Perigo da Enumeração Lógica:** A assumida linearidade de que o "Disco C:" é sempre o `PHYSICALDRIVE0` é uma falácia perigosa em ambientes virtualizados e infraestruturas complexas. O mapeamento prévio de *hardware* (WMI/CIM) é um passo mandatório antes de qualquer instrução de extração.
2. **Imutabilidade pela Base:** A utilização do utilitário `dd` permite congelar o estado exato de um sistema comprometido. Ao ignorar o sistema de ficheiros, garante-se que os *timestamps* e metadados vitais permanecem inalterados, preservando o valor em tribunal da prova adquirida.
