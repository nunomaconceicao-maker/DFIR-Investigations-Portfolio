# 🔬 Lab 1: SSD File Carving num Sistema de Ficheiros Windows

## 📝 Descrição e Cenário
O **File Carving** é uma técnica forense utilizada para recuperar ficheiros (ou fragmentos de ficheiros) a partir do espaço não alocado de um disco, baseando-se em assinaturas de ficheiros (Magic Numbers/Headers e Footers), ignorando o sistema de ficheiros (ex: NTFS, FAT32) e a ausência de metadados.

O objetivo deste laboratório é executar o *file carving* numa imagem forense de um disco SSD Windows e demonstrar, na prática, o impacto crítico que a funcionalidade **TRIM** tem na destruição de evidências e na taxa de sucesso da recuperação de dados.

## 🧰 Ferramentas Utilizadas
* **Autopsy** (Plataforma de Digital Forensics)
* **Imagens Forenses analisadas:**
  * `Windows_Evidence_SSD_TE.dd` (Imagem de SSD com TRIM ativado)
  * `Windows_Evidence_SSD_TD.dd` (Imagem de SSD com TRIM desativado)

---

## ⚙️ Metodologia e Execução

### Fase 1: Análise com TRIM Ativado (Trim Enabled)
Nesta primeira fase, simulámos a análise de um SSD moderno onde a funcionalidade TRIM estava ativa no sistema operativo.

1. Criação de um novo caso no Autopsy: `SSD File Carving (Windows, TRIM Enabled)`.
2. Adição da fonte de dados (`Windows_Evidence_SSD_TE.dd`) como *Disk Image*.
3. Execução dos módulos de ingestão padrão do Autopsy.
4. **Resultado da Análise:** Após o processamento da imagem, verificou-se que a ferramenta não gerou qualquer diretório de `CarvedFiles`. 

> **Observação:** O Autopsy não conseguiu recuperar nenhum ficheiro do espaço não alocado. Isto ocorreu porque, com o TRIM ativado, o sistema operativo instruiu o controlador do SSD a apagar fisicamente (zero-fill/garbage collection) os blocos marcados como apagados.

---

### Fase 2: Análise com TRIM Desativado (Trim Disabled)
Na segunda fase, analisámos uma imagem onde o TRIM estava inativo (comportamento semelhante a um HDD tradicional ou pen drive USB).

1. Criação de um novo caso no Autopsy: `SSD File Carving (Windows, TRIM Disabled)`.
2. Adição da fonte de dados (`Windows_Evidence_SSD_TD.dd`) como *Disk Image*.
3. Execução dos módulos de ingestão e análise profunda.
4. **Resultado da Análise:** Concluída a ingestão, o Autopsy gerou com sucesso o diretório `CarvedFiles`.
5. **Extração:** 
   * Acedeu-se à pasta `CarvedFiles` na árvore de diretórios.
   * Foi possível visualizar múltiplos ficheiros recuperados, sinalizados pela ferramenta (ex: `f0475952.gif`).
   * Através do painel inferior, os ficheiros foram validados em formato *Hex* e *Application* (visualização nativa).
   * Procedeu-se à extração (*Extract File(s)*) das evidências para o diretório local do caso.


---

## 🧠 Conclusões de DFIR (Digital Forensics & Incident Response)

Este laboratório comprova que a arquitetura dos discos de estado sólido (SSD) altera drasticamente a abordagem tradicional de forense digital:

1. **A Ameaça do TRIM:** Ao contrário dos discos magnéticos (HDD) onde os dados apagados permanecem no disco até serem subscritos, os SSDs com suporte a TRIM eliminam fisicamente os dados residuais para manter a performance de escrita. 
2. **Tempo é o fator mais crítico:** Num cenário real de Resposta a Incidentes, se o sistema estiver ligado e equipado com um SSD, o processo de *Garbage Collection* pode estar a destruir evidências em *background*.
3. **Preservação:** A extração de memória volátil (RAM) e o isolamento imediato da máquina (desligar puxando o cabo de alimentação em certas políticas estritas, ou suspensão) tornam-se imperativos para evitar a destruição irreversível do espaço não alocado.

---
*Retornar ao [Menu Principal](../README.md)*