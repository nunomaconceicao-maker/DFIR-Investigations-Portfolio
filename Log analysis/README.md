# AI-Powered Security Log Analysis

## Cenário
Como analista de SOC, estás a avaliar a melhor forma de integrar Modelos de Linguagem de Grande Escala (LLMs) no teu fluxo de trabalho diário. O teu objetivo é determinar a estratégia de *prompting* (instruções dadas à IA) mais eficaz para a análise de *logs* de segurança: será melhor fornecer à IA todos os *logs* de uma só vez utilizando um *prompt* genérico, ou utilizar *prompts* especializados e baseados em funções para cada tipologia de *log*?

## Objetivo
O objetivo deste módulo é avaliar e comparar a eficácia das técnicas de *prompting* geral versus especializado ao utilizar Inteligência Artificial Generativa para a análise de *logs* de cibersegurança.

## Visão Geral
Este módulo compara dois métodos distintos de utilização de IA Generativa na análise de segurança. No primeiro exercício, será utilizado um único *prompt* abrangente para solicitar ao ChatGPT a análise de um conjunto misto de *logs* de segurança provenientes de sistemas IDS, SIEM e *firewalls*. No segundo exercício, será adotada uma abordagem mais refinada e direcionada, elaborando *prompts* especializados para cada fonte de *logs*, com o intuito de comparar a profundidade e a qualidade da análise contextual fornecida pela IA.

## Tarefas do Laboratório

1. [Exercise 01: Interpreting AI-Driven Analysis from a General Security Prompt](#exercise-01-interpreting-ai-driven-analysis-from-a-general-security-prompt)
2. [Exercise 02: Executing Targeted Log Analysis with Specialized AI Prompts](#exercise-02-executing-targeted-log-analysis-with-specialized-ai-prompts)

---

## Exercise 01: Interpreting AI-Driven Analysis from a General Security Prompt

### Cenário
Na integração de Modelos de Linguagem de Grande Escala (LLMs) nas operações de um Centro de Operações de Segurança (SOC), o primeiro passo é estabelecer uma linha de base (*baseline*). Este laboratório foca-se na atribuição de uma "persona" abrangente à IA (ChatGPT) e no fornecimento simultâneo de um conjunto misto de *logs* provenientes de diferentes plataformas (IDS, SIEM e Firewall). O objetivo é avaliar a capacidade do modelo para processar, diferenciar e estruturar uma análise significativa de dados heterogéneos utilizando apenas uma instrução genérica e abrangente.

### Objetivo
Utilizar um *prompt* único e estruturado para analisar um conjunto misto de *logs* de segurança com o ChatGPT, avaliando a qualidade, precisão e utilidade da sua análise generalizada.

### Metodologia e Execução

1. **Preparação e Injeção do Prompt (Persona):**
   * Acedeu-se à interface do ChatGPT e definiu-se o comportamento do modelo através do seguinte *prompt* de engenharia estruturada:
     > *"Act as a cybersecurity analyst specializing in intrusion detection and log analysis. I will provide IDS, SIEM, and firewall logs; for each event, output a concise alert block in the following format: header with log type/vendor, then bullets for Timestamp, Source, Destination, Type, Severity, Rule, followed by short sections for Analysis, Risks, False Positive Potential, and Remediation (all clear, actionable, 1-3 sentences or short bullets). Number blocks sequentially, keep language concise, no extra summaries or timelines unless asked, and after listing all events, end with up to three clarifying questions."*
   * Submeteu-se o ficheiro de dados `Sample Logs For AI Analysis` em anexo à instrução.

2. **Avaliação dos Resultados - Logs IDS (Palo Alto Threat Prevention):**
   * **SQL Injection Attempt:** A IA analisou a tentativa bloqueada e contextualizou-a corretamente como um potencial compromisso interno, dado que tanto a origem como o destino eram endereços IP internos.
   * **RDP Brute Force:** Identificado como um ataque externo direcionado ao interior, classificado como Crítico e com baixo potencial de Falso Positivo (FP).
   * **Suspicious Network Activity (Allowed):** A IA alertou para o risco de movimento lateral, uma vez que o tráfego incomum entre *hosts* internos foi permitido pelas políticas da rede.

3. **Avaliação dos Resultados - Logs SIEM:**
   * **Login Failure:** A IA ponderou o risco de escalada de privilégios contra um Falso Positivo médio (potencial erro de digitação de palavra-passe pelo utilizador). 
   * **Unauthorized Access Attempt:** Contextualizado como potencial erro inadvertido do utilizador ao tentar aceder a ficheiros restritos.
   * **Log Cleared (Critical):** O modelo sinalizou assertivamente a limpeza de *logs* de segurança pelo utilizador "Unknown" como um indicador fortíssimo de evasão e de atividade maliciosa subjacente num *Domain Controller*.

4. **Avaliação dos Resultados - Logs Firewall (Fortinet FortiGate):**
   * **Suspicious HTTPS Traffic:** A IA correlacionou o tráfego interno para um IP público com a possibilidade de comunicações de Comando e Controlo (C2).
   * **Unauthorized SMB Access:** O bloqueio deste tráfego foi analisado como uma tentativa de propagação interna de *malware* (lateralização), com baixo risco de ser um Falso Positivo.
   * **Possible DNS Tunneling:** Identificado corretamente como um vetor de alto risco para exfiltração de dados ou canais de comunicação ocultos.

### Análise e Conclusão
O *prompt* generalista provou ser altamente eficaz para operações de triagem rápida (*Triage* Nível 1). Ao forçar o modelo a seguir um formato rígido (*Header, Analysis, Risks, FP Potential, Remediation*), eliminou-se a "alucinação" e o excesso de texto (verbosidade) típico das ferramentas de IA. 

A IA demonstrou capacidade analítica dedutiva — por exemplo, ao inferir que um ataque SQLi entre IPs internos representa um *host* já comprometido, ou que a limpeza de *logs* exige uma resposta a incidentes (IR) imediata. Contudo, por se tratar de um *prompt* único para várias plataformas, a análise avalia cada evento de forma isolada, não efetuando correlação temporal ou sequencial entre os eventos do IDS, SIEM e Firewall.

---

## Exercise 02: Executing Targeted Log Analysis with Specialized AI Prompts

### Cenário
Após a avaliação da abordagem generalista, é fundamental testar uma estratégia mais refinada (*Targeted Prompting*). Neste exercício, utilizam-se três *prompts* especializados e independentes, instruindo a IA a adotar "personas" técnicas específicas: Analista de IDS, Analista de SOC e Engenheiro de Segurança de Rede. O objetivo é determinar se a adoção de um perfil contextualizado produz análises mais detalhadas, rigorosas e com passos de remediação mais acionáveis para cada fonte de dados.

### Objetivo
Criar e executar *prompts* especializados e baseados em funções (Role-Based) para *logs* de IDS, SIEM e Firewall, visando gerar uma análise contextualizada e taticamente profunda através de IA generativa (ChatGPT).

### Metodologia e Execução

O processo foi dividido em três sessões de análise independentes, cada uma com o seu próprio *prompt* de definição de função:

1. **Análise de Logs IDS (Perfil: Analista de Deteção de Intrusões)**
   * **Prompt Base:** Foi solicitado à IA que atuasse como especialista em deteção de intrusões, focada em ataques de força bruta, SQLi e acessos não autorizados.
   * **SQL Injection Attempt:** O modelo classificou o evento corretamente e detalhou a remediação, sugerindo a investigação do *host* de origem e a revisão de vulnerabilidades na aplicação.
   * **RDP Brute Force Attempt:** Identificado como uma tentativa crítica de acesso inicial externo. A recomendação priorizou o bloqueio imediato do IP na *firewall* e a auditoria aos *logs* de autenticação do servidor destino.
   * **Suspicious Network Activity:** A IA interpretou o tráfego interno (LAN-to-LAN) como um indicador claro de movimento lateral, sugerindo a inspeção de pacotes e a validação do propósito de negócio daquela comunicação.

2. **Análise de Logs SIEM (Perfil: Analista de SOC)**
   * **Prompt Base:** A IA assumiu a função de rever eventos do SIEM, procurando anomalias de *login*, escalada de privilégios e exfiltração de dados.
   * **Failed Admin Log:** A análise sugeriu a implementação de políticas de bloqueio de conta (*Account Lockout*) e a criação de alertas baseados em limites para distinguir erros de digitação de ataques sustentados.
   * **Unauthorized File Access Attempt:** O foco recaiu sobre o Princípio do Menor Privilégio, sugerindo uma auditoria às permissões do utilizador (JohnDoe).
   * **Security Log Cleared:** O modelo sinalizou este evento como um indicador crítico de anti-forense. A remediação exigiu uma resposta a incidentes (IR) imediata, o isolamento do *Domain Controller* e uma investigação forense profunda.

3. **Análise de Logs de Firewall (Perfil: Engenheiro de Segurança de Rede)**
   * **Prompt Base:** Função focada na identificação de acessos remotos não autorizados, atividade de *botnets* e ligações de saída anómalas.
   * **Suspicious HTTPS Traffic (Allowed):** Identificado como potencial tráfego de Comando e Controlo (C2) oculto via encriptação HTTPS. Foi recomendada a realização de varrimentos de *malware* no *endpoint*.
   * **Unauthorized SMB Access (Blocked):** Considerado um forte indicador de propagação de *malware* (ex: *worms*). A sugestão passou pelo isolamento da origem e pelo reforço das regras de SMB na rede.
   * **Possible DNS Tunneling:** O modelo destacou o alto risco de evasão de controlos e exfiltração, sugerindo a investigação do *host* e a ativação de funcionalidades avançadas de filtragem DNS na *firewall*.

### Análise e Conclusão
A estratégia de *prompts* especializados superou largamente a abordagem generalista. Ao assumir funções técnicas restritas, o modelo de IA deixou de fornecer respostas genéricas e passou a alinhar-se com as metodologias reais de investigação de um SOC. 

As recomendações de remediação tornaram-se consideravelmente mais táticas e contextuais — por exemplo, sugerindo ações específicas na *firewall* para incidentes de rede, ou análises forenses profundas perante evidências de evasão no SIEM. Esta metodologia demonstra que a IA é uma ferramenta de triagem excecional, desde que o analista saiba restringir o seu escopo de análise através de uma engenharia de *prompts* rigorosa.