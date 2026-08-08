# 🪟 DFIR: Análise Forense de Imagens Windows (NTFS) via CLI

## 📌 Sobre a Investigação
Este repositório documenta a resposta técnica a um incidente de cibersegurança focado na triagem e recuperação de dados num sistema Windows comprometido. A intervenção utilizou exclusivamente ferramentas de linha de comandos (CLI) para interrogar diretamente a estrutura do disco a um nível subjacente ao Sistema Operativo, garantindo uma extração de artefactos não intrusiva e altamente granular.

## 🎯 Objetivos Técnicos da Intervenção
* **Análise de Estruturas de Volume:** Mapeamento de tabelas de partição e identificação de setores críticos numa imagem Raw (`.dd`).
* **Interrogação de Metadados (NTFS):** Análise direta aos registos da *Master File Table* (MFT), extraindo propriedades do volume, listas de *clusters* (Bitmap) e descritores de segurança.
* **Data Carving (Extração de Dados):** Identificação de indicadores de evasão (ficheiros desvinculados/apagados) e execução de processos de recuperação em massa (*Extraction/Carving*) para um ambiente estanque.

## 🛠️ Stack Tecnológico e Ambiente de Triagem
* **Motor Forense:** The Sleuth Kit (TSK - *Command Line Tools*)
* **Tipo de Evidência:** Imagem Forense Raw / Duplicação Bit-a-Bit (`.dd`)
* **Sistema de Ficheiros Alvo:** NTFS 

---

## 📂 Metodologia de Triagem e Resposta

### Fase 1: Mapeamento de Partições e Identificação do Sistema
O processo iniciou-se com a leitura não montada da imagem forense para prevenir a contaminação de metadados:
* Execução do utilitário `mmls` para mapear o *layout* das partições, identificando os limites exatos (setores de início e fim) das áreas alocadas e do espaço livre (*Unallocated*).
* Utilização do `fsstat` para validar a assinatura do sistema de ficheiros (NTFS) e extrair metadados críticos relativos à origem do sistema (ex: arquitetura e versão do SO).

### Fase 2: Interrogação Direta à Master File Table (MFT)
Para auditar a integridade do sistema de ficheiros, utilizou-se o módulo `istat` com o objetivo de analisar entradas vitais da MFT, nomeadamente:
* **MFT Entry 0 e 1 (`$MFT` / `$MFTMirr`):** Validação da tabela principal de ficheiros e da sua respetiva cópia de segurança.
* **MFT Entry 6 (`$Bitmap`):** Interrogação do ficheiro que rastreia o estado de alocação de todos os *clusters* no volume, vital para compreender o volume de dados residuais.
* **MFT Entry 8 e 9 (`$BadClus` / `$Secure`):** Auditoria aos *clusters* marcados como danificados (uma técnica comum de ocultação de dados) e revisão das políticas de controlo de acessos.

### Fase 3: Deteção de Evasão e Recuperação de Artefactos (Data Carving)
O foco final da intervenção foi a identificação e recuperação de informação intencionalmente apagada pelo perpetrador:
* A ferramenta `fls` foi executada com parâmetros de recursividade e filtragem de eliminação (`-r -d`) para expor unicamente as entradas marcadas como desvinculadas (assinaladas com `*`) na totalidade da árvore de diretórios.
* Recurso ao utilitário `tsk_recover` para efetuar o *carving* integral da imagem. Todos os ficheiros recuperáveis (alocados e não-alocados) foram exportados com sucesso para uma diretoria de custódia sanitizada, permitindo o prosseguimento da análise documental.

---

## 💡 Parecer Técnico e Lições Retiradas
1. **Poder da Abordagem CLI:** A utilização de ferramentas em modo texto demonstrou ser significativamente mais rápida e granular do que interfaces gráficas tradicionais, permitindo uma resposta ágil no isolamento de ficheiros críticos.
2. **Dependência da MFT no NTFS:** A investigação validou que a eliminação de um ficheiro no sistema NTFS atua primariamente ao nível do registo da MFT e do `$Bitmap`. Sem a sobreposição magnética dos *clusters* (Secure Wipe), a recuperação integral dos dados é tecnicamente viável em tempo útil.
3. **Visibilidade Total (Unallocated Space):** A capacidade de ignorar as *flags* do sistema operativo e forçar a extração de espaço não alocado através do comando `tsk_recover` foi decisiva para o resgate efetivo das evidências eliminadas.
