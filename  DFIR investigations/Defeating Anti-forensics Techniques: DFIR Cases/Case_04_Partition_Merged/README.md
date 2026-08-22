# 🔬 Case 4: Recuperação de Dados em Partições Fundidas (Deleted & Merged)

## 📝 Descrição e Cenário
Uma técnica anti-forense comum para ocultar dados consiste em apagar a partição maliciosa e, de seguida, realizar um *merge* (Estender Volume / *Extend Volume*) incorporando esse espaço não alocado numa partição contígua legítima. O objetivo do atacante é não deixar evidências de espaço não alocado no disco, fazendo com que uma análise superficial não detete anomalias na topologia da *drive*.

**Cenário de Investigação:** Um atacante armazenou dados exfiltrados numa partição, apagou-a e estendeu a partição primária adjacente para ocupar esse espaço livre. O utilizador reportou que o sistema perdeu uma drive lógica e a sua drive principal aumentou misteriosamente de tamanho. A investigação foca-se em fazer um *scan* aos blocos lógicos da drive estendida para identificar as fronteiras da antiga partição e recuperar o seu conteúdo.

## 🧰 Ferramentas Utilizadas
* **Windows Disk Management** (Para simulação da técnica de evasão / *Merge*)
* **R-Studio Data Recovery** (Plataforma profissional de Data Recovery e reconstrução de File Systems)

---

## ⚙️ Metodologia e Execução

### Fase 1: Simulação da Técnica Anti-Forense
* Utilizando o `diskmgmt.msc`, localizou-se um volume legítimo adjacente ao espaço não alocado deixado pelo apagamento da partição maliciosa.
* Executou-se a função *Extend Volume*, assimilando o espaço livre. Visualmente, a partição apagada deixou de existir nas tabelas do sistema operativo.

### Fase 2: Análise Forense e Identificação
* Iniciou-se a ferramenta **R-Studio** para contornar a leitura das tabelas lógicas (MBR/GPT/MFT) geradas pelo sistema operativo, focando a análise nos blocos físicos da drive agora estendida.
* Executou-se um *Detailed Scan* à drive inteira.
* **Resultado:** O algoritmo de heurística do R-Studio conseguiu identificar fronteiras lógicas (*File System Boundaries*) residuais no meio do volume estendido. A ferramenta rotulou a partição original oculta como `Recognized2` (O tamanho reportado correspondia exatamente ao tamanho da partição apagada pelo atacante).

### Fase 3: Extração de Evidência
* Explorou-se o interior do volume lógico identificado (`Recognized2`).
* A árvore de diretórios original encontrava-se intacta, uma vez que a operação de "Estender Volume" do Windows não realiza formatação de baixo nível (Zero-fill) nos novos blocos adquiridos.
* Selecionou-se a estrutura de pastas e efetuou-se a recuperação (*Recover Marked*) para um disco de destino seguro, isolado do sistema em análise (ex: `D:\Recovered_Partition\Root`).

---

## 🧠 Conclusões de DFIR (Digital Forensics & Incident Response)

1. **Estender Volumes não é Apagar Dados:** A fusão de uma partição em espaço não alocado (através das ferramentas standard dos sistemas operativos) altera os limites lógicos do *File System* primário, mas **não subscreve os dados físicos** previamente existentes nesses setores.
2. **Reconhecimento Heurístico:** Ferramentas forenses avançadas conseguem procurar por tabelas MFT (*Master File Table*) ou estruturas FAT residuais, identificando "sistemas de ficheiros dentro de sistemas de ficheiros". 
3. **Persistência da Evidência:** A menos que o atacante utilize ativamente ferramentas de *wiping* (*Secure Erase* / Comando `dd` com `/dev/zero`), as técnicas baseadas na manipulação de tabelas de partições são facilmente mitigadas durante a fase de exame forense.

---
*Retornar ao [Menu Principal](../README.md)*