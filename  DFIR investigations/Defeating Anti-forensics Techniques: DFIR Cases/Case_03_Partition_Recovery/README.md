# 🔬 Case 3: Recuperação de Dados de Partições Apagadas / Perdidas

## 📝 Descrição e Cenário
Quando uma partição é apagada de um disco através das ferramentas convencionais do sistema operativo, os ficheiros não são imediatamente destruídos. O que o sistema faz é remover a entrada referente a essa partição na tabela de partições (MBR - *Master Boot Record* ou GPT - *GUID Partition Table*). Enquanto o espaço não for reescrito (sobrescrito), a estrutura da partição e os dados originais permanecem recuperáveis.

**Cenário de Investigação:** Um atacante guardou ficheiros maliciosos (*payloads*) numa das partições do sistema da vítima para exfiltrar dados sensíveis do negócio. Após o ataque, de forma a ocultar os seus rastos (técnica anti-forense), o atacante apagou a partição inteira. O objetivo desta investigação é detetar a partição oculta/apagada, recuperar a sua estrutura e extrair os artefactos maliciosos para posterior análise.

## 🧰 Ferramentas Utilizadas
* **EaseUS Data Recovery Wizard** (Utilizado neste laboratório para simulação de recuperação com interface gráfica)
* *(Nota de DFIR: Em cenários de linha de comandos ou ambientes Linux, ferramentas open-source como o **TestDisk** cumprem exatamente o mesmo propósito com elevada eficácia).*

---

## ⚙️ Metodologia e Execução

### Fase 1: Identificação e Scanning da Partição Perdida
1. **Preparação do Ambiente:** Antes de iniciar qualquer extração, foi criada uma diretoria de destino isolada (`D:\Recovered Partition`) para garantir que os dados recuperados nunca seriam reescritos no disco de origem.
2. **Reconhecimento:** A ferramenta de recuperação foi executada no sistema comprometido. No painel de seleção de discos, o software identificou automaticamente uma anomalia na tabela de partições, listando uma entrada órfã denominada `Lost Partition-1`.
3. **Análise Profunda (Advanced Scan):** Iniciou-se o *scan* focado exclusivamente no espaço físico dessa partição apagada para reconstruir a árvore de diretórios original.

### Fase 2: Validação de Artefactos
1. **Inspeção de Diretórios:** Após a conclusão do *scan*, a ferramenta reconstruiu a estrutura lógica, apresentando pastas como `Lost Files`, `RAW` e a partição original `Lost Partition (NTFS)`.
2. **Validação:** Navegando pela estrutura reconstruída, foi possível visualizar os dados intactos que residiam na partição antes do ataque, confirmando que os dados não sofreram *wiping* (sobrescrita), mas apenas uma eliminação lógica da tabela de partições.

### Fase 3: Extração e Preservação
1. **Recuperação Segura:** A partição inteira (`Lost Partition-1`) foi selecionada para extração.
2. **Preservação da Evidência:** A extração foi direcionada para a diretoria previamente criada (`D:\Recovered Partition`), respeitando o princípio de não contaminação da drive de origem.
3. A ferramenta exportou com sucesso a árvore de diretórios, incluindo os potenciais ficheiros maliciosos utilizados pelo atacante, que ficaram agora disponíveis para isolamento e análise estática/dinâmica.

---

## 🧠 Conclusões de DFIR (Digital Forensics & Incident Response)

1. **Ilusão da Eliminação:** Apagar uma partição é uma técnica anti-forense extremamente fraca. Altera apenas o "mapa" do disco (MBR/GPT), deixando a "cidade" (os dados) intacta. Qualquer analista forense com ferramentas adequadas consegue reconstruir o índice e aceder ao conteúdo.
2. **A Regra de Ouro da Extração:** Neste caso prático, foi aplicada a regra fundamental de DFIR: **nunca recuperar dados para o disco original**. A recuperação exige a criação de novos ficheiros; se os guardássemos no disco original, arriscávamo-nos a sobrescrever o próprio espaço não alocado que estávamos a tentar recuperar.
3. **Próximos Passos na Investigação:** Com os dados recuperados, a fase seguinte (fora do âmbito deste laboratório) passaria por submeter os executáveis desconhecidos a uma *sandbox* e gerar os respetivos *hashes* para procurar Indicadores de Compromisso (IoCs) em bases de dados de *Threat Intelligence*.

---
*Retornar ao [Menu Principal](../README.md)*