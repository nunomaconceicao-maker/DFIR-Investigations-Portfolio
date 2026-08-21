# Generative AI for Security Automation and Compliance

## Cenário
Como profissional de cibersegurança, uma parte significativa das operações diárias envolve a realização de auditorias de segurança de rotina e a geração de documentação detalhada de conformidade. Embora críticas, estas tarefas podem ser morosas e repetitivas. Este projeto explora como potenciar Modelos de Linguagem de Grande Escala (LLMs) para maximizar a produtividade, automatizando a criação de *scripts* operacionais de auditoria e a redação de relatórios formais de conformidade, libertando assim tempo valioso para trabalho analítico e estratégico mais complexo.

## Objetivo
Aprender a aplicar técnicas de *prompting* (simples e avançadas) para gerar *scripts* de auditoria de segurança funcionais e compilar relatórios de conformidade de forma automatizada com recurso a Inteligência Artificial.

## Visão Geral
Este módulo foca-se na utilização prática de IA Generativa (ChatGPT) para automação de segurança. No primeiro exercício, a IA será instruída a gerar um *script* em PowerShell para a execução de uma auditoria de segurança básica num sistema. No segundo exercício, será adotada uma *framework* de *prompting* avançada e estruturada para guiar o modelo através de um processo interativo, resultando na criação de um relatório de conformidade de segurança abrangente, rigoroso e pronto para auditoria.

## Tarefas do Laboratório

1. [Exercise 01: Generating a PowerShell Security Audit Script with AI](#exercise-01-generating-a-powershell-security-audit-script-with-ai)
2. [Exercise 02: Creating a Comprehensive Compliance Report Using an Advanced AI Prompting Framework](#exercise-02-creating-a-comprehensive-compliance-report-using-an-advanced-ai-prompting-framework)

---

## Exercise 01: Generating a PowerShell Security Audit Script with AI

### Cenário
No papel de um engenheiro de *DevSecOps*, surge frequentemente a necessidade de criar *scripts* rápidos e eficientes para auditar configurações de segurança — como o estado da *Firewall* do Windows — ao longo de múltiplos servidores. Em vez de desenvolver o código manualmente a partir do zero, este cenário explora a utilização de um assistente de Inteligência Artificial (LLM) para gerar um *script* em PowerShell que seja não só funcional, mas também robusto e devidamente documentado, otimizando o tempo de desenvolvimento.

### Objetivo
Utilizar engenharia de *prompts* direcionada para gerar um *script* de PowerShell operacional através do ChatGPT, com o intuito de auditar, validar e registar (*log*) o estado dos perfis da *Firewall* do Windows.

### Metodologia e Execução

O laboratório foi executado num ambiente Windows 11, utilizando a interface web do ChatGPT para a geração do código de automação.

1. **Engenharia de Prompt (Definição da Persona):**
   * Foi submetido um *prompt* estruturado para forçar a IA a adotar o rigor de um engenheiro de segurança, exigindo código limpo e moderno:
     > *"Act as a DevSecOps engineer writing security audit scripts. Generate a PowerShell script to check if Windows Firewall is enabled and log the result. Use comments, avoid deprecated commands, and include a test output line. Below is an example: # Check Windows Firewall Status Get-NetFirewallProfile | Select-Object Name, Enabled"*

2. **Análise do Script Gerado pela IA:**
   A IA gerou um *script* completo dividido em blocos lógicos essenciais para operações de TI:
   * **Configuração e Recolha (Setup):** O *script* definiu automaticamente variáveis ambientais, como o caminho para o ficheiro de *log* (ex: no *Desktop* do utilizador) e a captura de *timestamps*. Utilizou o *cmdlet* moderno `Get-NetFirewallProfile` para extrair os nomes dos perfis de rede (Domain, Private, Public) e os seus respetivos estados (Enabled/Disabled).
   * **Lógica de Validação (Looping):** Foi implementada uma rotina de verificação iterativa. O código validou cada perfil da *firewall*; se detetasse pelo menos um perfil desativado, o estado geral da auditoria passava a "Falha" (gerando uma mensagem de alerta). Caso contrário, a auditoria registava "Sucesso".
   * **Registo e Output (Logging):** O *script* foi desenhado para anexar (*append*) a mensagem final ao ficheiro de *log* de texto. Adicionalmente, apresentou na consola uma tabela formatada com os detalhes técnicos e imprimiu uma "linha de teste", garantindo que o engenheiro pudesse validar a execução antes de enviar o *script* para produção.

### Análise e Conclusão
O exercício demonstrou a eficácia da IA Generativa na aceleração de tarefas de *DevSecOps*. A instrução explícita para "evitar comandos obsoletos" garantiu que o modelo utilizasse o módulo `NetSecurity` (suportado no PowerShell 5+ e PowerShell Core), em vez de recorrer a comandos antigos do *netsh*.

Ao gerar código modular, comentado e com tratamento de *logs* incorporado, a IA produziu um artefacto de auditoria pronto a ser utilizado (*production-ready*). Esta abordagem de automação não só reduz o erro humano na escrita de código, como estabelece uma base sólida para a verificação contínua de conformidade (*Continuous Compliance*). A partir desta base estrutural, um analista de segurança pode facilmente expandir o *script* para, por exemplo, enviar os resultados por e-mail ou reencaminhar os *logs* diretamente para um SIEM.

---

## Exercise 02: Creating a Comprehensive Compliance Report Using an Advanced AI Prompting Framework

### Cenário
Após a automação bem-sucedida de um *script* de auditoria operacional, o desafio seguinte enquadra-se na vertente de GRC (Governance, Risk, and Compliance): a elaboração de um relatório formal de conformidade de segurança. Sendo a documentação técnica um processo complexo que exige um alinhamento estrito com *frameworks* de mercado, a adoção de um *prompt* simples resultaria numa análise genérica e superficial. Para garantir a precisão, profundidade e rigor exigidos num ambiente corporativo, foi aplicada uma *framework* de *prompting* avançada e multifásica, forçando a IA (ChatGPT) a conduzir um processo interativo de perguntas e respostas (Q&A) antes de compilar o documento final.

### Objetivo
Utilizar uma *framework* de *prompting* estruturada em múltiplas fases para guiar um LLM através de um processo interativo e avaliativo, resultando na geração automatizada de um relatório detalhado de conformidade de segurança.

### Metodologia e Execução

O exercício foi realizado no ChatGPT, utilizando uma abordagem de geração assistida em várias etapas:

1. **Injeção do Prompt Avançado (Fase de Alinhamento):**
   * Foi submetido um *template* de *prompt* avançado. Em vez de ordenar a geração imediata de texto, a instrução forçou a IA a analisar os critérios pretendidos e a fazer perguntas de clarificação ao analista sobre o incidente específico, garantindo a recolha de todo o contexto técnico antes da redação.

2. **Processo Interativo de Q&A:**
   * A IA gerou uma lista de questões direcionadas.
   * O analista forneceu as métricas do incidente através da caixa de conversação, fornecendo à IA as balizas factuais para a análise (evitando "alucinações" no modelo).

3. **Geração e Estruturação do Relatório:**
   * A IA validou as informações e propôs um esboço (*outline*) lógico. Após aprovação ("yes, provide everything at one go"), o relatório completo foi gerado, englobando as seguintes secções críticas:
     * **Sumário da Vulnerabilidade:** Foco na CVE-2023-36884 (Falha de Execução Remota de Código no Microsoft Office), detalhando os sistemas afetados, vetores de ataque e passos de mitigação.
     * **Indicators of Compromise (IOCs):** Listagem técnica dos artefactos maliciosos (endereços IP, domínios, *hashes* e nomes de ficheiros).
     * **Mapeamento MITRE ATT&CK:** Análise da *Kill Chain* do incidente, mapeando táticas e técnicas desde o Acesso Inicial (via *phishing*) até à Exfiltração de Dados.
     * **Sumário Executivo e Metodologia:** Confirmação do comprometimento de uma instância Windows Server 2022 via *phishing*. A metodologia da auditoria baseou-se na aplicação de uma escala RAG (*Red-Amber-Green*) para classificar o nível de conformidade.
     * **Descobertas Críticas (Findings):** Identificação de falhas de alto risco, nomeadamente a ausência de aplicação de regras de prevenção de *malware* (ex: *Attack Surface Reduction - ASR*) e desvios nas políticas de contas de administrador local.
     * **Mapeamento de Conformidade (Compliance Mapping):** O relatório classificou com o status **Vermelho** as falhas críticas no controlo de acessos, indicando uma violação direta das normas rigorosas da **CIS** e **PCI DSS**.
     * **Plano de Remediação:** Geração de tarefas corretivas acionáveis atribuídas a equipas específicas, com prazos rigorosos (ex: imposição das regras ASR num prazo máximo de 7 dias).

4. **Autoavaliação e Melhoria Contínua:**
   * O *prompt* forçou a IA a autoavaliar a qualidade do seu próprio relatório (*Evaluation Rubric Score*), analisando a sua clareza, acionabilidade e alinhamento com as melhores práticas da indústria.
   * Por fim, a IA gerou "Opções de Refinamento" para relatórios futuros, recomendando a inclusão de diagramas visuais, o mapeamento para a *framework* do NIST e a possibilidade de exportar uma versão exclusivamente focada na equipa técnica.

### Análise e Conclusão
Este laboratório ilustra o enorme potencial da Inteligência Artificial Generativa para transformar as operações de GRC. A utilização de *prompting* iterativo superou largamente as limitações da geração de texto "num único passo". Ao forçar a IA a fazer perguntas prévias, garantiu-se que o relatório final estava perfeitamente ancorado nos dados reais do incidente (IOCs, CVEs) e em normas rigorosas como PCI DSS e CIS. 

A inclusão automatizada de Planos de Remediação com atribuição de responsáveis e prazos (*SLAs*) demonstra que a IA não serve apenas para relatar problemas, mas também para estruturar a resposta a incidentes. Com esta técnica, um analista sénior consegue reduzir horas de trabalho administrativo e documental para uma fração do tempo, garantindo que o resultado técnico não perde rigor normativo ou profundidade tática.
