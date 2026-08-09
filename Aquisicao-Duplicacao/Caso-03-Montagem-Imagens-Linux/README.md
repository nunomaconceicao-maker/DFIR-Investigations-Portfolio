# 🐧 DFIR: Montagem Forense de Sistemas de Ficheiros (NTFS e HFS+) em Linux

## 📌 Sobre a Intervenção
Este laboratório foca-se na extração lógica de dados a partir de imagens periciais (`.dd`), demonstrando como "abrir" cópias bit-a-bit de forma segura numa *Forensic Workstation* Linux. O caso aborda a interoperabilidade entre sistemas, montando imagens provenientes de ambientes Microsoft (NTFS) e Apple (HFS+) garantindo a imutabilidade das provas.

## 🎯 Objetivos Técnicos
* Explorar os utilitários de montagem nativos do ecossistema Linux (`mount` e `losetup`).
* Garantir a preservação da cadeia de custódia utilizando parâmetros estritos de *Read-Only*.
* Inspecionar estruturas de ficheiros, metadados e identificar artefactos ocultos vitais para a investigação.

## 🛠️ Stack Tecnológico e Ambiente
* **Sistema Operativo:** Ubuntu Linux (Forensic Workstation)
* **Utilitários de Extração Lógica:** `mount`, `losetup`
* **Sistemas de Ficheiros Alvo:** NTFS (Windows) e HFS+ / Mac OS Extended (macOS)

---

## 📂 Metodologia e Execução

### Fase 1: Montagem Direta e Segura de Volumes NTFS
Para aceder à evidência Windows, contornou-se a montagem automática do sistema para evitar contaminação:
* Estabeleceu-se um ponto de montagem isolado (`mkdir /mnt/dd`).
* O ficheiro pericial foi acoplado através da instrução `mount -o ro`. A *flag* `-o ro` (Read-Only) assegura o bloqueio total de operações de escrita ao nível do *kernel*, prevenindo que o sistema operativo atualize os *timestamps* de acesso aos ficheiros investigados.

### Fase 2: Gestão de Dispositivos Loop para Volumes macOS
Recorreu-se a dispositivos virtuais de bloco (*Loop Devices*) para emular o disco físico Apple:
* Executou-se a interrogação de blocos livres no sistema operativo (através de `losetup -f`), isolando o canal lógico de comunicação.
* A evidência foi anexada ao canal com a restrição `-r` (`losetup -r /dev/loopX [imagem]`), emulando hardware em modo de leitura rigorosa.
* A interface gráfica do Ubuntu permitiu o reconhecimento nativo da partição, viabilizando a pesquisa através da revelação de ficheiros ocultos do sistema.

---

## 🔍 Análise de Artefactos Forenses (macOS)
Durante a inspeção da imagem Apple, foram identificados e validados os seguintes artefactos ocultos, que constituem pilares fundamentais numa investigação cibernética:

* 🗑️ **`.Trashes`**: Diretório correspondente à Reciclagem do macOS. Em contexto forense, constitui o primeiro vetor de análise para recuperar ficheiros que o perpetrador tentou eliminar (neste cenário, existiam apenas pastas comuns visíveis como *images*, *Songs* e *text*, elevando a importância do conteúdo da reciclagem).
* ⏱️ **`.fseventsd` (File System Events)**: Uma verdadeira mina de ouro forense. O macOS regista neste diretório todas as alterações, criações e eliminações de ficheiros, permitindo a reconstrução da *timeline* (linha do tempo) exata dos crimes informáticos.
* 🔎 **`.Spotlight-V100`**: Base de dados do motor de pesquisa nativo do macOS. É de extrema relevância investigativa pois, mesmo após a eliminação definitiva de um ficheiro, os seus metadados (nome, tamanho, datas) podem permanecer cacheados nesta base de dados, provando inequivocamente que o ficheiro existiu.
* 👁️ **`.DS_Store`**: Ficheiro gerado automaticamente pelo sistema operativo sempre que um utilizador abre um diretório no *Finder*. A sua presença e *timestamps* provam que alguém acedeu visualmente aos conteúdos daquela pasta específica.

---

## 💡 Parecer Técnico e Lições Retiradas
1. **Auditoria Criteriosa de Sistemas de Ficheiros:** Embora o escopo inicial antecipasse um *Apple File System* (APFS), a inspeção manual aos diretórios ocultos (nomeadamente a existência de `.HFS+ Private Directory Data` e `.journal`) revelou que a evidência se encontrava, na verdade, formatada em **HFS+ (Mac OS Extended)**. Esta discrepância demonstra a importância da validação visual e técnica por parte do analista forense, em vez da aceitação cega de pressupostos.
2. **Imutabilidade em Primeiro Lugar:** A simples abertura de uma imagem forense sem as devidas proteções invalida imediatamente o valor legal da prova. A mestria dos comandos de montagem manual em Linux (`-o ro` e `-r`) provou ser o mecanismo de defesa mais eficaz contra a contaminação acidental de provas.
