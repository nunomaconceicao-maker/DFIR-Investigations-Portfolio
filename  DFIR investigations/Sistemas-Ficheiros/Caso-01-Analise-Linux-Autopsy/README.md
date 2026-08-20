# 🔍 DFIR: Investigação de Ameaça Interna e Extração Forense em Infraestrutura Linux

## 📌 Scenario
Em resposta a um incidente de segurança classificado como *Insider Threat* (Ameaça Interna), a equipa de investigação foi encarregue de analisar a imagem forense de um servidor Linux (`.img`). O objetivo central da intervenção consistiu em dissecar a estrutura de diretórios do sistema operativo (ext) para auditar o acesso a contas privilegiadas, identificar potenciais tentativas de exfiltração de propriedade intelectual e expor técnicas de anti-forense (destruição de dados), garantindo simultaneamente a integridade criptográfica da prova pericial.

## 🎯 Objectives
* **File System Parsing:** Executar a ingestão da imagem forense e reconstruir integralmente a árvore de diretórios nativa do ecossistema Linux.
* **Auditoria de Identidades:** Localizar configurações críticas de sistema para mapear as contas de utilizador locais envolvidas na intrusão.
* **Recuperação e Evasão:** Identificar tentativas ativas de ofuscação de provas, nomeadamente através da recuperação de ficheiros do *Unallocated Space* e análise de supressão temporal.
* **Preservação Criptográfica:** Extrair metadados temporais (*MAC times*) e gerar assinaturas *Hash* (MD5) de artefactos críticos para atestar a validade técnica do processo.

## 🧾 Evidence
* **Tipo de Evidência:** Imagem Forense de Sistema Linux (Cópia Física).
* **Formato:** `.img` (Raw).
* **Estado:** Preservada, acoplada em ambiente *Read-Only*.

## 🛠️ Environment & Tools
* **Plataforma Forense Lógica:** Autopsy (The Sleuth Kit).
* **Editor Hexadecimal / Raw Recovery:** WinHex.
* **Sistema Operativo do Host:** Windows Server (*Forensic Sandbox* com estrito *Network Isolation*).

---

## 🔬 Methodology
1. **Ingestão Lógica (Autopsy):** Abertura do contentor forense e parametrização dos módulos de ingestão para efetuar o *parsing* automático da arquitetura do sistema de ficheiros nativo do Linux (ext), permitindo navegação desde a `/root` até às diretórias de utilizador.
2. **Auditoria de Sistema:** Interrogação direta ao diretório de configuração `/etc/`, com foco específico no ficheiro `passwd` para mapeamento de privilégios.
3. **Isolamento de Artefactos Sensíveis:** Navegação até ao diretório `/home/j.silva/Documents/` para localizar, extrair e catalogar ficheiros com suspeita de exfiltração.
4. **Data Carving de Baixo Nível (WinHex):** Execução de uma varredura independente (agnóstica ao sistema de ficheiros) sobre os blocos não alocados, utilizando a técnica de *File Recovery by Type* para interrogar cabeçalhos hexadecimais (*Magic Numbers*).

---

## 🔎 Analysis

### Evidence 01 (Ofuscação no `/etc/passwd`)
A inspeção ao ficheiro `passwd` revelou que partes críticas da sua estrutura se encontravam marcadas com a *flag* de estado `Unallocated` e suprimidas para 0 (zero) bytes de tamanho. Contudo, a execução de extração de *Strings* (separador *Text*) revelou dados textuais residuais legíveis, contendo especificamente a identificação da conta de administração `root` e do utilizador `j.silva`.

### Evidence 02 (Artefacto Alvo)
Foi isolado o ficheiro `Relatorio_Auditoria_Q3.xls` no diretório do utilizador suspeito. A plataforma calculou com sucesso a sua assinatura MD5 e extraiu as datas de Criação, Modificação e Acesso (MAC times) a partir dos metadados do ficheiro.

### Evidence 03 (Artefactos Residuais)
O procedimento de *Data Carving* no WinHex identificou assinaturas binárias correspondentes a ficheiros de imagem (capturas de ecrã) dispersas pelo espaço não alocado (*Unallocated Space*), as quais haviam sido previamente omitidas pela tabela de alocação corrompida do sistema.

## 🧩 Findings
* **Finding 1 (Confirmação de Ameaça Interna e Anti-Forense):** A manipulação intencional do ficheiro `passwd` (Evidência 01) e a presença de dados zerados com *timestamps* suprimidos (`0000-00-00 00:00:00`) constituem Indicadores de Comprometimento (IoCs) claros de técnicas *Anti-Forense*. O utilizador `j.silva` tentou destruir ativamente os registos da sua escalada de privilégios e atividade no sistema.
* **Finding 2 (Exfiltração e Ocultação):** A recuperação de capturas de ecrã diretamente do espaço não alocado (Evidência 03) corrobora a tese de exfiltração não autorizada. O perpetrador tentou eliminar estas imagens para ocultar provas visuais das suas ações, provando a necessidade de conjugar análises lógicas (Autopsy) com análises *Raw/Hex* (WinHex).
* **Finding 3 (Interoperabilidade de Análise):** A extração bem-sucedida atesta o agnosticismo da infraestrutura pericial: é plenamente exequível auditar de forma rigorosa partições complexas de matriz Linux recorrendo a plataformas *open-source* baseadas num ambiente isolado Windows.

---

## ⚠️ Forensic Considerations
* **Integridade Inegociável:** O registo documentado do MD5 (Evidência 02) não é um detalhe acessório, mas sim uma medida essencial de controlo. Esta assinatura valida matematicamente a imutabilidade do relatório extraído, precavendo o risco de repúdio técnico num eventual processo disciplinar ou judicial contra o *insider*.
* **O Risco da Tabela de Alocação:** Depender exclusivamente das ferramentas que leem a tabela de alocação do sistema de ficheiros (MFT/ext) deixaria de fora os artefactos cruciais da Evidência 03. O *File Recovery by Type* contorna a corrupção lógica, interagindo puramente com a superfície magnética simulada do disco.

## 📝 Conclusion
A intervenção técnica expôs com sucesso o vetor da Ameaça Interna. A combinação de *parsing* lógico (Autopsy) com *Data Carving* a baixo nível (WinHex) anulou as técnicas de evasão do suspeito, permitindo a reconstrução de identidades, a recuperação de dados sensíveis ocultos e a preservação hermética da cadeia de custódia através da validação criptográfica.
