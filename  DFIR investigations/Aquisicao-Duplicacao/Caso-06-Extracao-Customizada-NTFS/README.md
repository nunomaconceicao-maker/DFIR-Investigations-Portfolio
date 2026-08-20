# 🔍 DFIR: Micro-Segmentação e Extração Customizada em Partições NTFS

## 📌 Scenario
Em cenários de Resposta a Incidentes (IR) envolvendo servidores empresariais com armazenamento massivo (na ordem dos Terabytes), o processamento integral e a indexação de uma imagem forense tornam-se incomportáveis face à urgência da intervenção (SLAs de resposta). Durante a investigação a um servidor comprometido, a equipa necessitou de isolar e analisar exclusivamente as estruturas vitais do sistema de ficheiros (como o *Volume Boot Record* e fragmentos da *Master File Table*) sem processar o *payload* do disco inteiro. Este caso documenta a dissecação de uma imagem RAW a nível de setor magnético, navegando na sua representação hexadecimal pura, para gerar "Imagens Customizadas" de tamanho reduzido focadas apenas nos artefactos críticos.

## 🎯 Objectives
* Ingerir uma imagem forense massiva e forçar o *parsing* estrutural das fundações da partição NTFS.
* Navegar de forma cirúrgica na matriz de dados através de Endereçamento Lógico de Blocos (LBA / *Absolute Sector*).
* Executar a leitura hexadecimal direta de blocos específicos e extrair fragmentos do disco para um novo contentor pericial customizado (`.img`), reduzindo drasticamente o *dataset* da investigação.

## 🧾 Evidence
* **Origem:** Imagem física bit-a-bit de servidor (`SRV-FIN01_DriveC.dd`).
* **Formato Resultante:** Imagem customizada / fragmentada (`VBR_MFT_Fragment.img`).
* **Sistema de Ficheiros Alvo:** NTFS (New Technology File System).

## 🛠️ Environment & Tools
* **Motor Analítico e Editor de Blocos:** DiskExplorer for NTFS / Editor Hexadecimal Avançado.
* **Sistema Operativo:** *Forensic Workstation* (Windows Sandbox).
* **Estado:** Análise a frio (*Dead Analysis*).

---

## 🔬 Methodology
1. **Montagem Lógica e Parsing:** Ingestão não montada da imagem `.dd` primária na aplicação de interrogação de disco. A ferramenta efetuou o mapeamento imediato do *Boot Sector*, validando a geometria da partição (setores lógicos, dimensionamento de *clusters* e localização dos metadados principais).
2. **Navegação de Baixo Nível:** Interrogação da superfície da imagem através de salto LBA (*Logical Block Addressing*) para *offsets* absolutos específicos, em vez de depender da árvore de diretórios gráfica.
3. **Conversão Analítica:** Transição da visualização abstrata para o *Hex View*, revelando o código binário/hexadecimal cru gravado nos pratos magnéticos antes da interpretação pela API do Windows.
4. **Extração Cirúrgica (Carving de Setores):** Seleção em bloco das *strings* hexadecimais adjacentes ao VBR e execução do despejo direto (*Copy to file...*) para um contentor lógico independente (`VBR_MFT_Fragment.img`), consolidando a extração num ficheiro único.

---

## 🔎 Analysis

### Evidence 01 (Validação do Boot Record)
No momento da importação da prova `SRV-FIN01_DriveC.dd`, o motor analítico isolou o *Boot Record* do volume NTFS, delineando inequivocamente onde o sistema de ficheiros inicia e detalhando a indexação de *clusters*.

### Evidence 02 (Camada Física Simulada)
A navegação via setores (ex: saltos para LBAs iniciais da partição) permitiu ler secções não reportadas pelo explorador de ficheiros convencional. A visualização hexadecimal expôs dados estruturais latentes, ignorando as hierarquias visuais forçadas pelo sistema operativo de origem.

## 🧩 Findings
* **Finding 1 (Triagem Ágil por Micro-Segmentação):** A intervenção validou que a equipa forense não está refém da morosidade de analisar o tamanho total do ficheiro `.dd`. A capacidade de selecionar e exportar blocos hexadecimais puros para um contentor miniaturizado permitiu isolar rapidamente a zona de impacto. Isto acelerou exponencialmente a posterior fase de engenharia reversa.
* **Finding 2 (Agnosticismo de SO e Bypass de Evasão):** Ao interrogar o disco através de um editor de blocos dedicado, a investigação operou num nível inferior à API de ficheiros do Windows, contornando cifras a nível de utilizador, bloqueios de *Access Control Lists* (ACLs) e atributos de ofuscação (ex: ficheiros injetados com atributos de sistema escondidos pelo atacante).

---

## ⚠️ Forensic Considerations
* **Risco de Corrupção por Desalinhamento (Offset):** A extração manual de fragmentos hexadecimais requer precisão absoluta no cálculo de *offsets*. O corte de dados a partir do meio de um setor físico resultaria num contentor `.img` corrompido, inutilizando a prova para motores de análise automatizada (*parsers*).
* **Cadeia de Custódia em Artefactos Derivados:** O ficheiro resultante da extração (`VBR_MFT_Fragment.img`) não representa a prova primária, constituindo um **Artefacto Derivado**. À luz dos procedimentos de DFIR, o seu valor de Hash (MD5/SHA-256) foi calculado no segundo exato da exportação e apensado ao relatório como uma derivação controlada da imagem original primária, garantindo a sua rastreabilidade em tribunal.

## 📝 Conclusion
A dissecação estrutural baseada em edição de setores foi concluída com elevado rigor tático. O caso demonstrou que a fluência técnica em navegação hexadecimal e endereçamento LBA capacita o analista de DFIR a segmentar matrizes de dados massivas de forma cirúrgica. A micro-segmentação restringiu o escrutínio pericial ao volume de dados estritamente necessário para responder ao incidente, otimizando os recursos computacionais do laboratório e isolando os artefactos vitais sem comprometer a prova global original.
