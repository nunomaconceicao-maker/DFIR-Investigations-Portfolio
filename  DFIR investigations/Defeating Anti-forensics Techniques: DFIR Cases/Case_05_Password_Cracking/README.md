# 🔬 Case 5: Quebra de Passwords em Ficheiros e Aplicações (Password Cracking)

## 📝 Descrição e Cenário
O bloqueio de documentos sensíveis através de passwords é uma técnica frequentemente utilizada para dificultar a análise forense e ocultar dados exfiltrados. Ao contrário da encriptação total de disco (FDE), a proteção ao nível do ficheiro permite que a evidência seja facilmente transportável, mas inacessível sem a chave correta.

**Cenário de Investigação:** Uma investigação de roubo de Propriedade Intelectual (IP) e segredos de negócio numa organização de banca de investimento levou à apreensão do computador do principal suspeito. Durante a fase de *Evidence Triage*, foram encontrados vários documentos altamente suspeitos, mas protegidos por password (Word, RAR e PDF). O objetivo desta fase da investigação é executar ataques de recuperação de passwords (*Offline Cracking*) para aceder ao conteúdo em texto limpo (*plaintext*) e recolher evidências do roubo de dados.

## 🧰 Ferramentas Utilizadas
* **Passware Kit Forensic** (Recuperação automatizada para ficheiros MS Office)
* **Advanced Archive Password Recovery - Elcomsoft** (Para ficheiros comprimidos RAR/ZIP)
* **Advanced PDF Password Recovery - Elcomsoft** (Para ficheiros Adobe PDF)
* *(Nota de DFIR: Em ambientes puramente de linha de comandos, o fluxo padrão seria extrair os hashes com ferramentas como `office2john`, `rar2john` ou `pdf2john`, e posteriormente submetê-los ao **Hashcat** ou **John The Ripper**).*

---

## ⚙️ Metodologia e Execução

### Fase 1: Documentos Microsoft Office (Word)
1. **Alvo:** `Sample_1.docx`
2. **Abordagem:** Utilizou-se o *Passware Kit Forensic* com a opção "Use Predefined Settings", que aplica automaticamente uma combinação de ataques de dicionário (*wordlists*), brute-force de baixa complexidade e análise de ficheiros temporários para recuperar a chave.
3. **Resultado:** A password foi recuperada com sucesso devido à sua baixa complexidade, permitindo a extração do conteúdo do documento.

### Fase 2: Ficheiros Comprimidos (RAR)
1. **Alvo:** `Compressed_files.rar`
2. **Abordagem:** Recorreu-se ao *Advanced Archive Password Recovery*. 
3. **Execução do Ataque:** Configurou-se um ataque de Força Bruta (*Brute-force*). Para fins de otimização de tempo nesta simulação, limitou-se o espaço de caracteres (*keyspace*) apenas a dígitos `All digits (0-9)`.
4. **Resultado:** A ferramenta calculou as permutações e quebrou o *hash* do ficheiro RAR, devolvendo a password em *plaintext* e permitindo a descompressão dos artefactos.

### Fase 3: Documentos Adobe PDF
1. **Alvo:** `Confidential.pdf`
2. **Abordagem:** Utilizou-se o *Advanced PDF Password Recovery*.
3. **Execução do Ataque:** Configurou-se um ataque de Força Bruta focado exclusivamente em letras minúsculas `All small latin (a-z)`.
4. **Resultado:** Ao limitar as variáveis (tamanho da password e *character set*), o processador conseguiu varrer todas as combinações rapidamente, revelando a chave de acesso.

---

## 🧠 Conclusões de DFIR (Digital Forensics & Incident Response)

1. **Entropia é a Chave da Defesa:** O sucesso de um ataque de *brute-force* depende inteiramente do tamanho da password e do espaço de caracteres utilizado (entropia). Em casos reais, onde não se conhece o *keyspace*, quebrar passwords longas e complexas exige um poder computacional massivo (frequentemente com *clusters* de GPUs).
2. **Ataques Offline:** Uma vez extraído o ficheiro do computador do suspeito, a proteção passa a ser testada offline. Isto significa que mecanismos como bloqueio de conta, *rate-limiting* (limite de tentativas) ou autenticação multifator (MFA) não se aplicam. A única barreira é a encriptação matemática do ficheiro.
3. **Metodologia Real:** Numa investigação de larga escala, um analista começa sempre com Ataques de Dicionário baseados em informações conhecidas sobre o alvo (OSINT, datas de nascimento, nomes de projetos do suspeito), seguidos de regras de mutação (ex: *Hashcat rules*), deixando os ataques puros de Força Bruta apenas como último recurso.

---
*Retornar ao [Menu Principal](../README.md)*
