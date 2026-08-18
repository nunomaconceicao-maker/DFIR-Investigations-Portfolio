# 🔍 DFIR: Aquisição Forense de Memória Volátil (RAM) em Ambientes Windows e Linux

## 📌 Scenario
Durante a fase de Primeira Resposta (*First Response*) a um incidente, a equipa de investigação deparou-se com estações de trabalho ativas (*Live Systems*) em ecossistemas Microsoft Windows e Linux (Ubuntu). O imperativo técnico consistiu em capturar a memória de acesso aleatório (RAM) das máquinas suspeitas antes de proceder ao seu isolamento energético. A RAM contém evidências voláteis inestimáveis, processos maliciosos em execução, ligações de rede, *passwords* em *plaintext* e chaves de encriptação — que são irremediavelmente perdidas assim que o sistema perde energia. 

## 🎯 Objectives
* Executar a aquisição de memória volátil (*Live RAM Acquisition*) em ambientes Windows contornando as proteções do sistema operativo.
* Compilar e injetar Módulos de Kernel (LKM) em sistemas Linux para neutralizar as restrições nativas de acesso à memória física.
* Minimizar a pegada forense (*footprint*) comparando métodos de extração local contra métodos de exfiltração remota (rede).
* Alcançar o *Footprint Magnético Zero* no disco rígido do suspeito através de canalização de dados via protocolo TCP/IP.

## 🧾 Evidence
* **EV-01 (Windows RAM):** Ficheiro `.mem` formatado cronologicamente (`yyyymmdd.mem`).
* **EV-02 (Linux Local):** Ficheiros de *dump* `ubuntu_local_ram.dd` e formato estruturado `.mem` (LiME).
* **EV-03 (Linux Remoto):** Imagem *Raw* `ubuntu_remote_ram.dd`.
* **Estado:** Extração Dinâmica (*Live Acquisition*).

## 🛠️ Environment & Tools
* **Sistemas Alvo:** Windows 10 VM e Ubuntu Suspect VM.
* **Sistema de Laboratório:** Ubuntu Forensics VM.
* **Ferramentas (Windows):** Belkasoft RAM Capturer (x64).
* **Ferramentas (Linux):** `fmem`, LiME (Linux Memory Extractor), `dd`, `netcat` (nc).

---

## 🔬 Methodology

### Fase 1: Aquisição em Ambiente Windows (Belkasoft)
1. **Bypass de Proteções:** Desativação de mecanismos ativos de proteção em tempo real que bloqueiam o acesso a endereços de memória físicos. A ferramenta *Belkasoft RAM Capturer* foi executada a partir de uma diretoria isolada com privilégios de Administrador.
2. **Definição de Destino Seguro:** A via de saída (*Output Path*) foi estritamente configurada para apontar para um Disco Forense dedicado (Drive F:), impedindo a gravação de dados na partição do sistema do suspeito.
3. **Extração:** Captura da memória física para o contentor `.mem`.

### Fase 2: Aquisição Local em Ambiente Linux (fmem e LiME)
1. **Preparação de Dependências:** Instalação de bibliotecas de compilação essenciais (`build-essential`) via *root* no sistema Linux alvo.
2. **Injeção de Módulos de Kernel:** Compilação a partir do código-fonte (*make*) dos utilitários `fmem` e `LiME`. Injeção do módulo no núcleo do sistema via `insmod lime-[versão]-generic.ko`, criando um pseudo-dispositivo de acesso à RAM (`/dev/fmem`).
3. **Extração Lógica:** Execução do utilitário `dd` (`dd if=/dev/fmem of=/home/james/ubuntu_local_ram.dd bs=1MB`) gerando uma cópia bit-a-bit para o diretório local do utilizador.

### Fase 3: Exfiltração Remota Furtiva (Netcat)
Para mitigar a contaminação destrutiva gerada na Fase 2, estabeleceu-se um canal de exfiltração remoto:
1. **No Laboratório (Ubuntu Forensics):** Levantou-se um *listener* passivo na porta TCP 1234, encaminhando todo o tráfego de entrada para um ficheiro raw (`nc -l 1234 > ubuntu_remote_ram.dd`).
2. **No Sistema Suspeito:** O processo de extração `dd` foi canalizado (via *pipe* `|`) para o serviço *netcat* e disparado contra o IP da *Forensic Workstation* (`dd if=/dev/fmem bs=1024 | nc [IP_Laboratório] 1234`), sem escrever no disco local.

---

## 🔎 Analysis

### Evidence 01 (Restrições de Sistema Operativo)
Sistemas Linux contemporâneos implementam a flag `CONFIG_STRICT_DEVMEM` no Kernel, bloqueando nativamente o acesso ao `/dev/mem` para prevenir ataques de injeção. A compilação *on-the-fly* do `fmem` e `LiME` provou ser o único método eficaz para expor o espaço de endereçamento de memória.

### Evidence 02 (Integridade dos Dumps)
Os ficheiros gerados em todas as fases apresentam um tamanho lógico rigorosamente igual à totalidade da RAM física alocada ao *hardware* (ex: 4GB RAM = ficheiro de ~4GB), atestando a captura integral do espaço, incluindo blocos vazios (*padding*).

## 🧩 Findings
* **Finding 1 (O Perigo da Escrita Local):** A metodologia aplicada na Fase 2 (despejar o ficheiro da RAM no `/home/` do suspeito) demonstra uma conduta de alto risco forense (*Forensic Fail*). Escrever gigabytes de dados no disco ativo destrói irreparavelmente o espaço não alocado (*Unallocated Space*) e o espaço de folga (*Slack Space*), impossibilitando o resgate futuro de dados eliminados (*Data Carving*).
* **Finding 2 (O Padrão de Excelência Netcat):** A exfiltração remota operada na Fase 3 atingiu o **Footprint Magnético Zero**. A canalização direta do `dd` para a rede limitou a contaminação apenas à RAM estritamente necessária para executar os dois comandos. A prova estática (disco rígido) manteve-se 100% inalterada, respeitando os *standards* máximos de preservação judicial.

---

## ⚠️ Forensic Considerations
* **O Paradoxo de Locard em Memory Forensics:** A aplicação do Princípio de Troca de Locard é inevitável. Para extrair a RAM, os utilitários (Belkasoft, LiME, Netcat) têm de carregar os seus próprios processos na RAM, sobrescrevendo uma ínfima percentagem da evidência que procuram salvar. Esta alteração deve ser legalmente documentada à luz do Princípio da Maior Relevância.
* **Segurança na Exfiltração Remota:** Embora taticamente superior, o envio da memória via `netcat` transmite dados em *plaintext* não cifrado. Num ambiente real não controlado, senhas e chaves criptográficas poderiam ser intercetadas na rede corporativa (MITM). Para cadeias de custódia restritas, protocolos encriptados como *SSH Pipes* (`dd | ssh`) são exigidos como mitigação.

## 📝 Conclusion
A captura de memória volátil é um processo delicado que exige profundo conhecimento da arquitetura do sistema operativo. Enquanto a extração em Windows beneficiou do direcionamento para *drives* externas, os ecossistemas Linux exigiram intervenção a nível de *Kernel* para contornar mecanismos de defesa. Conclui-se que a exfiltração de dados via rede (Remota) constitui o vetor tático preferencial em Digital Forensics, permitindo estabilizar o estado efémero da máquina sem comprometer as provas físicas latentes no suporte de armazenamento.
