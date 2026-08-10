# 🔍 DFIR: Análise Forense de Imagens Windows (NTFS) via CLI

## 📌 Scenario
Em resposta a um incidente de cibersegurança num sistema Windows comprometido, foi necessário executar a triagem e recuperação de dados de forma altamente granular e ágil. Para evitar a indexação morosa de plataformas gráficas e garantir uma análise subjacente à abstração do Sistema Operativo, a intervenção foi conduzida recorrendo exclusivamente a ferramentas de linha de comandos (CLI). A abordagem visou interrogar diretamente a infraestrutura do disco (partições, MFT e *clusters*) a baixo nível, resgatando evidências que o perpetrador julgava ter destruído.

## 🎯 Objectives
* **Análise de Estruturas de Volume:** Mapear tabelas de partição e identificar os limites físicos de setores alocados e não alocados numa imagem Raw (`.dd`).
* **Interrogação de Metadados (NTFS):** Inspecionar diretamente os registos vitais da *Master File Table* (MFT), nomeadamente o estado dos *clusters* (`$Bitmap`) e as áreas marcadas como danificadas (`$BadClus`).
* **Deteção de Evasão e Data Carving:** Identificar ficheiros desvinculados (*deleted*) e executar processos de extração em massa (*Extraction/Carving*) para um diretório de custódia sanitizado.

## 🧾 Evidence
* **Tipo de Evidência:** Imagem Forense Raw / Duplicação Bit-a-Bit (`.dd`).
* **Sistema de Ficheiros Alvo:** NTFS.
* **Estado:** Imagem não montada; análise estática a frio (*Dead Analysis*).

## 🛠️ Environment & Tools
* **Motor Forense Lógico:** The Sleuth Kit (TSK - *Command Line Tools*).
* **Utilitários Core:** `mmls`, `fsstat`, `istat`, `fls`, `tsk_recover`.
* **Sistema Operativo (Host):** Máquina Virtual isolada.

---

## 🔬 Methodology
1. **Mapeamento Topológico (`mmls`):** Execução do utilitário `mmls` para dissecar o *layout* das partições, identificando os *offsets* exatos (setores de início e fim) das áreas de dados viáveis da imagem.
2. **Validação de Arquitetura (`fsstat`):** Utilização do `fsstat` focado no *offset* da partição principal para validar a assinatura NTFS e extrair a arquitetura original do volume.
3. **Auditoria à MFT (`istat`):** Interrogação das entradas vitais da *Master File Table*:
   * Entradas 0 e 1 (`$MFT` / `$MFTMirr`) para validação estrutural.
   * Entrada 6 (`$Bitmap`) para mapeamento de alocação de *clusters*.
   * Entradas 8 e 9 (`$BadClus` / `$Secure`) para procura de ocultação de *payloads* em setores falsamente danificados.
4. **Triagem de Artefactos Eliminados (`fls`):** Varredura da árvore de diretórios com parâmetros de recursividade e filtragem de eliminação (`fls -r -d [imagem]`), listando exclusivamente ficheiros desvinculados.
5. **Recuperação em Massa (`tsk_recover`):** Execução do *carving* integral da imagem direcionado ao espaço não alocado, exportando os ficheiros recuperáveis para uma diretoria de evidências isolada.

---

## 🔎 Analysis

### Evidence 01 (Layout do Volume)
Os comandos `mmls` e `fsstat` expuseram a grelha física do disco, confirmando o *offset* da partição NTFS principal e revelando os limites exatos do espaço não alocado (*Unallocated Space*) livre da gestão da API do Windows.

### Evidence 02 (Interrogação da MFT)
A análise às metadatas base (via `istat`) confirmou que os atributos `$Bitmap` rastreavam vastas áreas de *clusters* como "livres", mas cujos registos prévios na MFT atestavam a existência histórica de ficheiros do utilizador. O `$BadClus` não revelou a marcação anómala de setores.

### Evidence 03 (Artefactos Desvinculados)
A filtragem implementada pelo utilitário `fls` listou dezenas de entradas marcadas com o prefixo `*`, o indicador canónico do The Sleuth Kit para atributos e ficheiros cujo apontador de alocação foi removido, mas que persistem no disco.

## 🧩 Findings
* **Finding 1 (Ineficácia da Eliminação Lógica):** A investigação confirmou que as ações de eliminação perpetradas atuaram primariamente ao nível do registo da MFT e da atualização do `$Bitmap` (marcadores lógicos). Como o suspeito não empregou técnicas de *Secure Wipe* (sobrescrita magnética), a integridade binária dos ficheiros manteve-se intata (Evidência 03), permitindo o resgate total dos dados via `tsk_recover`.
* **Finding 2 (Mitigação de Anti-Forense):** A auditoria limpa ao `$BadClus` descarta a hipótese de o atacante ter manipulado a tabela de setores danificados para camuflar *malware* ou contrabando de dados — uma técnica avançada de evasão ao nível do sistema de ficheiros.
* **Finding 3 (Agilidade da Interrogação Direta):** A abordagem puramente baseada em CLI provou a sua eficácia tática. Ignorando a indexação visual e as *flags* do sistema operativo, a equipa extraiu evidências vitais do *Unallocated Space* de forma cirúrgica e significativamente mais rápida do que numa GUI tradicional.

---

## ⚠️ Forensic Considerations
* **Preservação de Estado Absoluta:** O The Sleuth Kit interroga a imagem forense a frio. Ao não montar o contentor num *Virtual File System* (VFS), a investigação elimina em 100% o risco de contaminação cruzada ou alteração acidental de *timestamps* por processos em *background* do sistema operativo do investigador.
* **Gestão de Custódia no Carving:** O utilitário `tsk_recover` atua em modo de leitura pura sobre a imagem primária. No entanto, o volume de ficheiros resgatados exige que o laboratório disponibilize uma unidade de armazenamento pericial previamente sanitizada de forma a albergar as provas extraídas sem risco de contaminação ambiental.

## 📝 Conclusion
A triagem via linha de comandos revelou-se um vetor de investigação de elevado rigor e eficiência. O domínio prático sobre as estruturas basilares do NTFS (MFT, `$Bitmap`, `$BadClus`) permitiu anular a tentativa de destruição de provas do perpetrador, demonstrando que o conhecimento profundo do funcionamento *raw* de um sistema de ficheiros suplanta largamente a abstração gráfica apresentada aos utilizadores e atacantes.
