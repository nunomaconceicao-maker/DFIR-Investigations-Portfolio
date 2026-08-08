# 🐧 DFIR: Investigação de Ameaça Interna e Extração Forense em Infraestrutura Linux

## 📌 Sobre a Investigação
Este repositório documenta a resposta técnica a um incidente de segurança (*Insider Threat*), focado na aquisição e análise forense de um servidor Linux (`.img`). O objetivo central da intervenção consistiu em dissecar a estrutura de diretórios do sistema operativo para auditar o acesso a contas privilegiadas e garantir a integridade da prova legal através do cálculo de assinaturas criptográficas estritas (Hashes).

## 🎯 Objetivos Técnicos da Intervenção
* **File System Parsing:** Ingestão de imagem forense Linux (cópia física) e reconstrução integral da árvore de diretórios nativa.
* **Auditoria de Acessos:** Localização e extração de configurações críticas de sistema, mapeando as contas de utilizador locais envolvidas no incidente.
* **Recuperação de Espaço Não Alocado:** Identificação de tentativas de ofuscação de provas, focando na deteção de ficheiros eliminados (*Unallocated Space*) com tamanho de zero bytes e supressão de metadados temporais.
* **Garantia da Cadeia de Custódia:** Extração de metadados temporais (MAC times) e cálculo de *hashes* MD5 de artefactos suspeitos de conterem propriedade intelectual exfiltrada.

## 🛠️ Stack Tecnológico e Ambiente de Triagem
* **Plataforma Forense:** Autopsy (The Sleuth Kit)
* **Tipo de Evidência:** Imagem Forense de Sistema Linux (`.img`)
* **Infraestrutura de Análise:** Estação de Trabalho Forense (Windows Server *Air-Gapped* / Isolado da Rede)

---

## 📂 Metodologia de Triagem e Resposta

### Fase 1: Ingestão e Reconstrução de Dados
O processo de resposta a incidentes iniciou-se com a abertura do contentor forense no Autopsy e a ingestão segura da imagem `.img` (formato Raw). Os módulos de ingestão foram parametrizados para realizar o *parsing* automático do sistema de ficheiros nativo do Linux (ext), permitindo a navegação hierárquica desde a diretoria raiz `/root` até às áreas restritas dos utilizadores.

### Fase 2: Auditoria de Identidades e Acessos (`/etc/passwd`)
Para mapear o raio de ação do perpetrador e identificar a potencial escalada de privilégios:
* Executou-se a navegação até à diretoria de configuração `/etc`.
* Isolou-se e analisou-se o ficheiro `passwd`.
* Durante a inspeção de baixo nível, detetaram-se indícios de atividade de ofuscação (sinalizada pelo *flag* de estado `Unallocated` e supressão para zero bytes em instâncias específicas). Através do separador *Text/Strings* da ferramenta (Data Carving), foi possível extrair dados textuais residuais cruciais sobre as contas de sistema (nomeadamente, a identificação da conta de administração `root` e do utilizador sob investigação, `j.silva`).

### Fase 3: Isolamento de Artefactos e Validação Criptográfica
A triagem focou-se nos documentos com maior probabilidade de manipulação ou exfiltração não autorizada por parte da conta comprometida:
* Foi localizado e isolado um artefacto de interesse (`Relatorio_Auditoria_Q3.xls`, suspeito de conter informação organizacional sensível) no percurso `/home/j.silva/Documents/`.
* Procedeu-se à extração imediata da sua assinatura digital (*Hash* MD5) e à documentação das datas de Criação, Modificação e Acesso (MAC times) através da análise de *File Metadata*. Este rigor processual garante a imutabilidade matemática da prova recolhida, essencial para suportar qualquer futura ação disciplinar ou processo judicial sem risco de repúdio.

### Fase 4: Data Carving Avançado com Editor Hexadecimal
Para garantir a exaustão da recolha de provas, o processo de resposta foi complementado com análise de baixo nível utilizando o **WinHex**:
* Foi executada uma operação de *Data Carving* isolada ao ficheiro de imagem, varrendo os setores do disco em busca de assinaturas de cabeçalhos de ficheiros (*Magic Numbers*).
* A técnica *File Recovery by Type* permitiu ignorar a tabela de alocação corrompida e resgatar imagens (capturas de ecrã exfiltradas) diretamente do espaço não alocado (*Unallocated Space*), exportando-as de forma segura para uma diretoria de custódia.
---

## 💡 Parecer Técnico e Lições Retiradas
1. **Agnosticismo da Infraestrutura de Análise:** A investigação comprovou a eficácia operacional de utilizar plataformas baseadas em ambiente Windows para dissecar, indexar e auditar partições e sistemas de ficheiros complexos de matriz Linux.
2. **Indicadores de Comprometimento (IoCs) Baseados em Tempo:** A presença de ficheiros com metadados temporais manipulados ou zerados (`0000-00-00 00:00:00`) no espaço não alocado atua como um alerta imediato de técnicas de *Anti-Forensics*, indicando a eliminação intencional de vestígios.
3. **Cadeia de Custódia Baseada em Hashes:** O registo rigoroso do MD5 validou a integridade completa da extração; a assinatura criptográfica é inegociável para atestar a validade técnica e legal da intervenção aos olhos das entidades competentes.
