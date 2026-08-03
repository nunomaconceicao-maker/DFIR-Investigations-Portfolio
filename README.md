# 🔍 DFIR: Estudo de Caso de Investigação Forense e Ameaça Interna

## 📌 Sobre o Projeto
Este repositório documenta uma simulação prática de Digital Forensics and Incident Response (DFIR). O objetivo deste projeto é demonstrar as metodologias de aquisição, recuperação de dados eliminados, verificação de integridade e deteção de técnicas de evasão (anti-forense) num cenário de *Insider Threat* (Ameaça Interna).

## 🎯 Objetivos Técnicos
* **Recuperação de Dados:** Restaurar artefactos e ficheiros de registo financeiro intencionalmente eliminados para ocultar provas.
* **Integridade Criptográfica:** Garantir a Cadeia de Custódia através do cálculo de assinaturas Hash (MD5/SHA-256) e validação HMAC.
* **Triagem de Malware:** Analisar artefactos suspeitos através de OSINT sem comprometer o ambiente de análise.
* **Deteção de Anti-Forense:** Identificar ofuscação de dados e adulteração de *File Signatures* (Magic Numbers).

## 🛠️ Ferramentas Utilizadas
* **Recuperação de Ficheiros:** Software de Data Recovery Comercial / TestDisk
* **Análise de Integridade:** Geradores de Hash (MD5 Calculator, HashCalc)
* **Inteligência de Ameaças:** VirusTotal API / OSINT
* **Análise Hexadecimal/Estrutural:** File Viewer e analisadores de cabeçalhos de ficheiros
* **Ambiente Segregado:** Máquina Virtual Windows estanque (Sem comunicação com a rede host)

---

## 📂 Fases da Investigação


### Fase 1: Recuperação de Provas (Cenário de Fraude Financeira)
**Contexto:** Foi detetada uma anomalia nos registos financeiros da organização. O principal suspeito eliminou permanentemente (via `Shift + Del`) os ficheiros de auditoria locais numa tentativa de destruir provas.
* **Metodologia:** Realizou-se uma varredura profunda (Deep Scan) aos blocos não alocados do disco (Volume `D:`).
* **Descobertas:** Como o sistema operativo apenas eliminou o apontador na Master File Table (MFT) e os setores não foram reescritos, foi possível extrair e pré-visualizar os ficheiros de texto cruciais (`audit_log_Q3.txt` e `transfer_records.txt`), isolando-os num diretório seguro.

### Fase 2: Cadeia de Custódia e Triagem de Malware
**Contexto:** Para garantir a validade legal das provas e investigar um anexo suspeito encontrado durante a recuperação de dados.
* **Metodologia:** 
  1. Todos os ficheiros recuperados foram submetidos a cálculos de hash (MD5). Adicionalmente, testou-se a aplicação de HMAC com uma chave simétrica de controlo para selar a integridade dos dados recuperados.
  2. Um ficheiro PDF suspeito (`invoice_urgente.pdf`) teve o seu hash extraído localmente e verificado contra bases de dados de inteligência de ameaças (VirusTotal).
* **Descobertas:** A pesquisa não intrusiva pelo hash do PDF revelou que o ficheiro continha um *payload* malicioso, identificado por dezenas de motores de segurança, sem nunca ter sido necessário executar o ficheiro no ambiente local.

### Fase 3: Deteção de Técnicas Anti-Forense (Ofuscação de Ficheiros)
**Contexto:** Durante a análise ao tráfego de rede capturado, encontrou-se um ficheiro de vídeo (`treino_seguranca.mp4`) e vários anexos de imagem. Suspeita-se de exfiltração de dados ou movimento lateral.
* **Metodologia:** Cruzamento da extensão dos ficheiros com os seus respetivos *Magic Numbers* (cabeçalhos estruturais).
* **Descobertas:** 
  * O cálculo de hash a um ficheiro de imagem aparentemente inofensivo (`anexo_01.png`) não coincidiu com a sua versão de *backup* original, levantando fortes indícios de esteganografia.
  * O suposto vídeo `.mp4` falhou a execução, despoletando erros de compilação. A análise estrutural confirmou que o cabeçalho não correspondia a um ficheiro de media. A extensão foi forjada pelo atacante para passar despercebida pelos filtros de segurança básicos.

 ## 💡 Conclusões e Lições Retiradas
1. **Volatilidade e Tempo de Resposta:** O sucesso da recuperação de dados em discos NTFS/FAT depende da rapidez do isolamento do disco. Uma intervenção atempada evitou o *data overwriting*.
2. **Abordagem "Zero-Trust" aos Ficheiros:** Nunca confiar nas extensões dos ficheiros. A análise forense deve basear-se sempre nas assinaturas hexadecimais (Magic Numbers) para identificar a verdadeira natureza de um artefacto.
3. **Segurança do Ambiente de Análise:** O cálculo local de hashes aliado a plataformas OSINT permite realizar uma triagem eficaz de malware sem expor a infraestrutura a potenciais infeções.

---
*Este projeto foi desenvolvido como um exercício prático de aprofundamento de competências em Cibersegurança e Digital Forensics.*

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
    class C decision;```
