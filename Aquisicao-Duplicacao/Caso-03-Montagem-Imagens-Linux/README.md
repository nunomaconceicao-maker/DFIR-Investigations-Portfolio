# 🔍 DFIR: Montagem Forense de Sistemas de Ficheiros (NTFS e HFS+) em Linux

## 📌 Scenario
Em sequência de processos de aquisição física, a investigação transitou para a fase de extração lógica de dados. O laboratório confrontou-se com duas imagens periciais *raw* (`.dd`) provenientes de ecossistemas operativos distintos: Microsoft (Windows) e Apple (macOS). O imperativo técnico consistiu em montar estes sistemas de ficheiros numa *Forensic Workstation* Linux de forma segura, abstraindo os metadados e artefactos ocultos, assegurando em simultâneo a imutabilidade estrita das provas durante a inspeção.

## 🎯 Objectives
* Explorar os utilitários nativos do ecossistema Linux (`mount` e `losetup`) para reconhecer partições heterogéneas.
* Inspecionar estruturas de ficheiros, metadados e identificar artefactos ocultos vitais para a reconstrução do incidente.
* Garantir a preservação da cadeia de custódia utilizando parâmetros estritos de *Read-Only* ao nível do *kernel*, bloqueando atualizações automáticas de *timestamps*.

## 🧾 Evidence
* **Evidence A:** Imagem física *Raw* de ecossistema Microsoft.
* **Evidence B:** Imagem física *Raw* de ecossistema Apple.
* **Formato:** `.dd`

## 🛠️ Environment & Tools
* **Sistema Operativo:** Ubuntu Linux (*Forensic Workstation*)
* **Ferramentas:** `mount`, `losetup`, Linux CLI

---

## 🔬 Methodology
1. **Montagem Direta NTFS (Windows):** Contornou-se a montagem automática (GUI) para evitar contaminação prévia. Estabeleceu-se um ponto de montagem isolado (`mkdir /mnt/dd`). O ficheiro pericial foi acoplado através da instrução de baixo nível `mount -o ro [imagem] /mnt/dd/`. A *flag* `-o ro` bloqueia operações de escrita, prevenindo que o Virtual File System (VFS) atualize os *MAC times* (Modified, Accessed, Created).
2. **Emulação de Dispositivo em Bloco (macOS):** Recorreu-se a dispositivos virtuais (*Loop Devices*) para emular a drive física da Apple. Executou-se a interrogação de blocos livres (`losetup -f`) e anexou-se a evidência ao canal lógico correspondente com a restrição `-r` (`losetup -r /dev/loopX [imagem]`).
3. **Reconhecimento Lógico:** A abstração do dispositivo *loop* permitiu o reconhecimento nativo da partição no Ubuntu, viabilizando a pesquisa interativa e a revelação de ficheiros ocultos do sistema.

---

## 🔎 Analysis

### Evidence 01 (macOS Image)
A inspeção visual aos diretórios raiz da evidência Apple, forçando a revelação de itens ocultos, confirmou a presença das estruturas `.Trashes`, `.fseventsd`, `.Spotlight-V100` e `.DS_Store`. Neste contexto, as pastas não ocultas continham apenas diretórios superficiais (*images*, *Songs* e *text*).

### Evidence 02 (macOS Image)
Foi identificada a existência do diretório `.HFS+ Private Directory Data` e de ficheiros de extensão `.journal` na raiz da partição emulada.

## 🧩 Findings
* **Finding 1 (Auditoria Criteriosa e Identificação de Arquitetura):** Embora o escopo inicial presumisse a utilização do *Apple File System* (APFS) padrão das versões modernas, a evidência estrutural 02 confirma inequivocamente que a drive se encontra formatada em **HFS+ (Mac OS Extended)**. Esta discrepância realça a importância da validação técnica em detrimento da aceitação cega de pressupostos processuais.
* **Finding 2 (Vectores de Reconstrução de Atividade):** A preservação intata das bases de dados identificadas na evidência 01 fornece ao laboratório pilares fundamentais para a investigação:
  * O diretório `.Trashes` viabiliza a recuperação de dados suprimidos.
  * O `.fseventsd` (*File System Events*) garante o registo de alterações para reconstrução precisa da linha do tempo (*Timeline*).
  * O `.Spotlight-V100` retém metadados cacheados que atestam a existência histórica de ficheiros já eliminados permanentemente.
  * A presença cronológica do `.DS_Store` atesta o acesso visual direto a diretórios específicos pelo utilizador.

---

## ⚠️ Forensic Considerations
* **Imutabilidade em Primeiro Lugar:** A simples abertura visual de uma imagem forense através de interfaces gráficas (com permissões *auto-mount*) invalida imediatamente o valor pericial da prova, atualizando datas de acesso. O domínio de montagens manuais com diretivas restritivas (`-o ro` e `-r`) é o mecanismo de defesa mais eficaz do analista contra a contaminação acidental.
* **Segregação de Ferramentas Comerciais:** A versatilidade do utilitário `losetup` provou ser essencial para abstrair blocos complexos, mitigando a dependência do laboratório de plataformas periciais proprietárias e licenciadas para a simples triagem primária de evidências.

## 📝 Conclusion
O isolamento e a montagem forense lógica de ecossistemas distintos (NTFS e HFS+) foram executados com sucesso numa única infraestrutura *open-source*. A aplicação consistente de protocolos de leitura restrita garantiu a integridade matricial das evidências, assegurando a validade técnica e jurídica dos artefactos identificados para as fases subsequentes de correlação da investigação.
