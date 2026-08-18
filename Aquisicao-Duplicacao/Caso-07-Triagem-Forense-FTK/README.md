# 🔍 DFIR: Triagem Rápida e Interrogação de Imagens Forenses (FTK Imager)

## 📌 Scenario
Em investigações de fuga de informação (*Data Theft / Exfiltration*), o volume de dados recolhido pode ascender a dezenas de Terabytes. Antes de submeter uma imagem pericial a um motor de processamento intensivo (que pode consumir dias de computação), a equipa de Primeira Resposta (*First Responders*) necessita de efetuar uma Triagem Forense (*Evidence Triage*). Este caso documenta a montagem lógica e a inspeção a frio de uma imagem Raw num ambiente estanque. O objetivo foi validar rapidamente a presença de artefactos críticos, ficheiros recentemente eliminados e metadados estruturais, determinando a viabilidade de prosseguir para uma investigação profunda.

## 🎯 Objectives
* Efetuar a montagem lógica de uma imagem forense estática em modo de leitura rigorosa (*Read-Only*), sem comprometer as assinaturas criptográficas originais.
* Conduzir uma triagem rápida à árvore do sistema de ficheiros para identificar a presença de ficheiros eliminados ou órfãos antes do processamento intensivo.
* Contornar a abstração do sistema operativo inspecionando os valores Hexadecimais cruciais para validar o conteúdo *raw* de ficheiros ofuscados.
* Extrair metadados físicos de artefactos (Localização de *Clusters*, *MAC Times* e *Signed Integers*) através de interpretação hexadecimal direta.

## 🧾 Evidence
* **Origem:** Imagem física bit-a-bit de uma estação de trabalho financeira.
* **Identificador:** `WS-FIN-04_PrimaryDrive.dd`
* **Estado:** Preservada; Triagem a frio (Dead Analysis).

## 🛠️ Environment & Tools
* **Motor de Triagem Forense:** AccessData FTK Imager.
* **Sistema Operativo:** *Forensic Workstation* (Windows Sandbox Isolada).

---

## 🔬 Methodology
1. **Ingestão Passiva:** Adição do contentor `.dd` como fonte de evidência (*Image File*) no FTK Imager, garantindo que o motor o interpreta com proteção contra escrita em memória.
2. **Navegação Lógica (Evidence Tree):** Exploração sequencial da árvore do sistema de ficheiros (NTFS), com foco na expansão das partições raiz para localização de marcadores de eliminação na *File List*.
3. **Inspeção de Baixo Nível (Hex View):** Transição da pré-visualização de ficheiros de texto/imagem para o modo Hexadecimal, permitindo a auditoria direta ao código binário e aos cabeçalhos (Magic Numbers) de ficheiros suspeitos.
4. **Interpretação de Metadados:** Utilização simultânea dos módulos *Properties* e *Hex Value Interpreter* para extrair a datação DOS (DOS Date), o *cluster* de início lógico e atributos operacionais de artefactos isolados.

---

## 🔎 Analysis

### Evidence 01 (Visibilidade de Ficheiros Eliminados)
A dissecação automática da árvore de partições (*Evidence Tree*) expôs de imediato a estrutura residente. Na *File List*, múltiplos ficheiros documentais e folhas de cálculo foram listados com o ícone de marcação de "eliminado" (um 'X' vermelho na interface do FTK), indicando que os seus registos foram removidos da interface de utilizador, mas os apontadores MFT persistem.

### Evidence 02 (Auditoria Hexadecimal)
A seleção de ficheiros reportados como danificados ou sobrescritos não impediu o FTK Imager de renderizar os *bytes* remanescentes no painel de visualização Hex. Esta vista confirmou que a estrutura interna (*File Signature*) de certos artefactos divergia da sua extensão lógica.

### Evidence 03 (Metadados Intrinsecos)
O painel de propriedades consolidou dados técnicos que a API do Windows oculta por defeito, nomeadamente o *Start Cluster* exato onde o ficheiro foi inicialmente gravado, os atributos lógicos de segurança e os identificadores temporais convertidos diretamente a partir do carimbo Hexadecimal (*Hex Value Interpreter*).

## 🧩 Findings
* **Finding 1 (Eficiência de Triage):** A intervenção validou o FTK Imager como uma ferramenta de excelência para a fase Triage. A identificação instantânea de dezenas de folhas de cálculo eliminadas no perfil do suspeito justificou tecnicamente a necessidade de avançar com o caso para um processamento forense profundo (utilizando ferramentas de correlação como o Autopsy ou EnCase).
* **Finding 2 (Deteção Precoce de Evasão):** A visualização hexadecimal integrada permitiu detetar num olhar relance que o suspeito tentou corromper cabeçalhos de ficheiros, provando uma tentativa ativa de ofuscação de provas (*Anti-Forensics*) logo nos primeiros 10 minutos de investigação.

---

## ⚠️ Forensic Considerations
* **Preservação de Integridade:** O AccessData FTK Imager opera de forma intrinsecamente passiva quando monta imagens `.dd` ou `.E01`. Ao contrário do processo de montar uma imagem lógica via Windows Explorer, o FTK ignora as rotinas de I/O do sistema anfitrião, assegurando que nenhum *timestamp* de "Último Acesso" é adulterado durante a triagem pericial.
* **Limites da Ferramenta de Triagem:** É imperativo registar legalmente que, embora o FTK Imager permita a visualização de *Hex Data* e a exportação manual de ficheiros eliminados, este não processa *Super Timelines* automáticas, não efetua indexação massiva, nem processa *Data Carving* cego de forma automatizada. Trata-se de uma ferramenta cirúrgica de validação, não de um motor de correlação total.

## 📝 Conclusion
A triagem pericial primária cumpriu o seu propósito estratégico. Num intervalo de tempo extremamente reduzido, a interrogação da imagem através do FTK Imager confirmou o comprometimento da estação de trabalho, atestou a presença de dados exfiltrados (entretanto eliminados) e expôs técnicas de evasão baseadas em adulteração hexadecimal. O caso segue agora para a fase de Processamento e *Data Carving* massivo, suportado por factos tangíveis isolados nesta triagem.
