# 🔍 DFIR: Aquisição Forense e Preservação de Evidências (Bit-a-Bit)

## 📌 Scenario
Este laboratório documenta o processo crítico de Aquisição Física (*Physical Acquisition*) de um sistema ativo (Windows). A intervenção focou-se na preservação do estado integral de um disco de sistema através da criação de uma imagem forense *raw* não adulterada, garantindo a recolha exaustiva de dados alocados, espaço não alocado (*Unallocated Space*) e espaço de folga (*Slack Space*) para futura análise em ambiente estanque.

## 🎯 Objectives
* Mapear o hardware subjacente através da interrogação de controladores físicos para identificação precisa da topologia dos discos.
* Isolar logicamente o destino de cópia para prevenir ciclos de sobrescrita e adulteração da evidência original.
* Executar uma clonagem a baixo nível (setor a setor) contornando a API e os bloqueios de segurança do Sistema Operativo.

## 🧾 Evidence
* **Tipo de Evidência:** Disco Físico de Sistema (Windows)
* **Formato de Saída:** Imagem Física Raw / Duplicação Bit-a-Bit (`.dd`)
* **Estado:** Sistema Ativo (*Live Acquisition*)

## 🛠️ Environment & Tools
* **Sistema Operativo (Host):** Windows
* **Ferramentas:** Windows PowerShell (WMI/CIM), Utilitário `dd` (Data Duplicator CLI)

---

## 🔬 Methodology
1. **Mapeamento de Hardware:** Execução do *cmdlet* `Get-CimInstance Win32_DiskDrive` no PowerShell para interrogar diretamente a *motherboard* e mapear os identificadores de hardware (`DeviceID`), mitigando os riscos de enumeração lógica do sistema operativo.
2. **Escalação de Privilégios (Bypass de ACLs):** Execução da consola de extração com privilégios máximos de administração (*Run as Administrator*), permitindo que o utilitário `dd` opere num nível inferior à estrutura de controlo de acessos nativa do Windows (evitando erros de *Access is Denied*).
3. **Parametrização da Extração:** Configuração do comando de clonagem apontando estritamente para o identificador físico de origem (`if=\\.\PHYSICALDRIVE1`) e descarregando para a unidade forense isolada (`of=E:\Windows_Evidence_002.dd`).
4. **Otimização de Transferência:** Definição do tamanho de bloco para 512 Kilobytes (`bs=512k`) de forma a maximizar a taxa de I/O, acompanhado das *flags* `--size` e `--progress` para monitorização ativa de perda de blocos.

---

## 🔎 Analysis

### Evidence 01
A execução do comando `Get-CimInstance` revelou uma discrepância na ordem de enumeração SCSI/NVMe: o disco de sistema alvo correspondia ao identificador `PHYSICALDRIVE1`, enquanto o disco secundário forense assumia a posição `PHYSICALDRIVE0`.

### Evidence 02
O ficheiro gerado (`.dd`) copiou de forma abstrata os blocos binários do dispositivo, contornando o processamento da API de sistema de ficheiros do Windows.

## 🧩 Findings
* **Finding 1 (O Perigo da Enumeração Lógica):** A assumida linearidade de que a "Drive C:" corresponde sempre ao `PHYSICALDRIVE0` constitui uma falácia perigosa em infraestruturas modernas. A interrogação de hardware foi imperativa, pois evitou um ataque de ciclo infinito (*storage loop*), no qual o disco escreveria a imagem sobre si próprio, destruindo a evidência.
* **Finding 2 (Imutabilidade pela Base):** A utilização do `dd` a baixo nível garantiu o "congelamento" exato do sistema comprometido. Ignorando a abstração do sistema de ficheiros, assegurou-se que os *timestamps* e metadados vitais permaneceram inalterados.

---

## ⚠️ Forensic Considerations
* **Risco de Corrupção Irreversível:** Um mero erro tipográfico na definição dos parâmetros de *Input File* (`if=`) e *Output File* (`of=`) durante a execução do comando `dd` resulta na formatação irrecuperável da unidade investigada.
* **Validação de Integridade:** Embora o foco principal da intervenção fosse o procedimento de duplicação, em ambiente real, esta operação obriga à recolha da assinatura criptográfica (Hash MD5/SHA-256) da drive original e da imagem gerada de imediato, assegurando o seu valor legal.

## 📝 Conclusion
A extração forense bit-a-bit foi executada em conformidade técnica, resultando numa duplicação *raw* intacta. O método impediu a contaminação cruzada e estabilizou a evidência volátil num contentor isolado, preparando o terreno para técnicas de *Data Carving* e escrutínio profundo num laboratório segregado.
