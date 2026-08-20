# 🔍 DFIR: Estudo de Caso de Investigação Forense e Ameaça Interna

## 📌 Scenario
Foi detetada uma anomalia grave nos registos financeiros da organização. Suspeita-se que um colaborador interno (*Insider Threat*) tenha executado fraude financeira e tentado destruir provas eliminando permanentemente ficheiros locais de auditoria. Adicionalmente, existem suspeitas de tentativa de exfiltração de dados e movimento lateral através de artefactos camuflados. A intervenção requer a recuperação de dados perdidos, deteção de técnicas anti-forenses (ofuscação) e triagem de *malware*, garantindo em todas as etapas a cadeia de custódia e o isolamento do laboratório.

## 🎯 Objectives
* Restaurar artefactos e ficheiros de registo financeiro intencionalmente eliminados a partir de blocos não alocados.
* Garantir a Cadeia de Custódia através do cálculo rigoroso de assinaturas criptográficas (Hashes MD5/SHA-256 e validação HMAC).
* Executar a triagem de artefactos suspeitos (*malware*) via inteligência OSINT, sem comprometer o ambiente de análise.
* Identificar técnicas de anti-forense, ofuscação de dados e falsificação de *File Signatures* (Magic Numbers).

## 🧾 Evidence
* **Tipo:** Espaço não alocado do Disco (Volume `D:`) e ficheiros suspeitos extraídos.
* **Estado:** Imagem/Artefactos isolados em ambiente estanque.

## 🛠️ Environment & Tools
* **Sistema Operativo:** Windows (*Forensic Sandbox* segregada - sem rede host).
* **Ferramentas:** TestDisk / Software de Data Recovery, MD5 Calculator / HashCalc, File Viewer / Analisadores Hexadecimais, VirusTotal API (OSINT).

---

## 🔬 Methodology
1. **Recuperação de Dados (Data Carving):** Realização de uma varredura profunda (*Deep Scan*) aos blocos não alocados do volume lógico suspeito.
2. **Preservação e Cadeia de Custódia:** Extração dos ficheiros órfãos para um diretório seguro, seguida da submissão imediata a cálculos de hash (MD5). Adicionalmente, aplicou-se HMAC com uma chave simétrica de controlo para selar a integridade da evidência recuperada.
3. **Análise Hexadecimal (Anti-Forense):** Interrogação dos cabeçalhos hexadecimais (Magic Numbers) de ficheiros de media suspeitos para validar a sua verdadeira assinatura em oposição à extensão reportada pelo Sistema Operativo.
4. **Triagem de Ameaças Estática:** Extração do hash de um ficheiro PDF suspeito e submissão à API do VirusTotal, aferindo o grau de ameaça sem recorrer à detonação do binário no ambiente local.

---

## 🔎 Analysis

### Evidence 01 (Espaço Não Alocado)
Os ficheiros de texto `audit_log_Q3.txt` e `transfer_records.txt` foram localizados intactos nos blocos não alocados do disco. Os seus apontadores originais na Master File Table (MFT) haviam sido eliminados (via instrução `Shift + Del`).

### Evidence 02 (Artefacto PDF)
O cálculo da assinatura MD5 do ficheiro `invoice_urgente.pdf` gerou uma *string* que, quando cruzada com bases de dados de inteligência de ameaças (VirusTotal), devolveu correspondência positiva (*match*) com um *payload* previamente classificado como malicioso por dezenas de motores de segurança.

### Evidence 03 (Falsificação de Extensão)
O ficheiro `treino_seguranca.mp4` reportou erros de compilação durante a tentativa de execução. A análise estrutural revelou que o seu *Magic Number* (cabeçalho hexadecimal) não corresponde à norma ISO Base Media File Format requerida para a tipologia MP4.

### Evidence 04 (Discrepância de Hashes)
O ficheiro visualmente inofensivo `anexo_01.png` revelou um *Hash* divergente da sua respetiva versão de *backup* original, apesar de não existirem alterações gráficas percetíveis à vista desarmada.

## 🧩 Findings
* **Finding 1 (Recuperação de Ocultação):** A eliminação `Shift + Del` suprime as vias normais de acesso do SO, mas não reescreve os setores físicos de imediato. A presença integral dos ficheiros financeiros na evidência 01 comprova a destruição intencional de provas documentais ligadas à fraude, agora recuperadas com sucesso.
* **Finding 2 (Ameaça Confirmada via OSINT):** A correspondência criptográfica da evidência 02 confirma que o ficheiro PDF é um vetor de intrusão (*malware*). O recurso a OSINT permitiu confirmar a ameaça com 100% de grau de confiança, sem nunca expor a *Forensic Sandbox* a uma infeção acidental.
* **Finding 3 (Práticas Evasivas e Anti-Forense):** O perpetrador empregou metodologias ativas de ofuscação para camuflar exfiltração de dados ou movimento lateral:
  * O vídeo MP4 (Evidência 03) teve a sua extensão forjada para iludir filtros de segurança básicos, ocultando a verdadeira natureza do executável/script.
  * A alteração não visual do código binário do ficheiro PNG (Evidência 04) sugere fortes indícios da utilização de esteganografia para contrabando de dados ofuscados.

---

## ⚠️ Forensic Considerations
* **Volatilidade e Reescrita (Overwriting):** A recuperação bem-sucedida descrita no Finding 1 dependeu criticamente da rapidez de resposta e do isolamento do disco (prevenção de escrita). Num disco NTFS ativo, o sistema operativo acabaria por reutilizar o espaço não alocado, destruindo a prova de forma irreversível.
* **Filosofia Zero-Trust perante Artefactos:** A investigação sublinha o princípio basilar do DFIR: as extensões de ficheiro são meras convenções visuais do Sistema Operativo. A validação estrutural a nível hexadecimal (Magic Numbers) é a única forma técnica de garantir a verdadeira tipologia de um artefacto.

## 📝 Conclusion
A investigação técnica concluiu positivamente o envolvimento do *insider* em manobras de fraude financeira, corroborado pelos registos de auditoria recuperados do disco. A identificação paralela de técnicas de evasão anti-forense (extensões forjadas e provável esteganografia) revela um nível de sofisticação técnico que visava a ocultação e exfiltração de dados. A metodologia implementada assegurou que as provas mantivessem validade de cadeia de custódia e que as ferramentas maliciosas não pusessem em causa a infraestrutura pericial.

---

## 📊 Fluxo da Investigação

```mermaid
graph TD
    A[1. Isolamento do Disco e Cópia Forense] --> B[2. Varredura de Setores Não Alocados];
    B --> C{Dados Ocultos ou Eliminados?};
    C -- Sim --> D[3. Extração para Diretório Seguro];
    C -- Não --> E[Fim da Análise de Disco];
    D --> F[4. Cálculo de Hashes MD5 / SHA-256];
    F --> G[5. Triagem e Análise];
    G --> H[Consulta OSINT / API VirusTotal];
    G --> I[Análise Hexadecimal / Magic Numbers];
    H --> J((Conclusões e Relatório));
    I --> J;
    
    classDef default fill:#f9f9f9,stroke:#333,stroke-width:2px;
    classDef decision fill:#e1f5fe,stroke:#0288d1,stroke-width:2px;
    class C decision;
