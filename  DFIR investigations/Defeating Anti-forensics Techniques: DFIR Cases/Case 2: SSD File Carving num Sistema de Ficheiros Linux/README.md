# 🔬 Case 2: SSD File Carving num Sistema de Ficheiros Linux

## 📝 Descrição e Cenário
O *File Carving* é uma técnica de recuperação agnóstica em relação ao sistema de ficheiros. Isto significa que, independentemente de estarmos a lidar com NTFS (Windows) ou EXT4 (Linux), a ferramenta procura blocos de dados órfãos no espaço não alocado através de assinaturas de cabeçalho e rodapé (*Headers* e *Footers* / *Magic Numbers*).

Neste caso de estudo, o objetivo é executar o *file carving* numa imagem forense extraída de um SSD com um sistema operativo Linux, utilizando uma *workstation* de análise em ambiente Windows. A investigação visa recuperar ficheiros fragmentados na ausência de metadados válidos.

## 🧰 Ferramentas Utilizadas
* **Autopsy** (Plataforma de Digital Forensics - Ambiente Windows)
* **Imagem Forense analisada:**
  * `Linux_Evidence_SSD.dd` (Cópia bit-a-bit de um SSD Linux)

---

## ⚙️ Metodologia e Execução

### Fase de Processamento e Análise
Uma vez que o *carving* lida com o formato em bruto (*raw data*) e o Autopsy suporta a leitura de múltiplos sistemas de ficheiros (incluindo os da família *nix*), a análise decorreu num ambiente isolado Windows.

1. **Inicialização do Caso:**
   * Criação de um novo caso no Autopsy: `SSD File Carving (Linux File System)`.
   * Adição da evidência (`Linux_Evidence_SSD.dd`) como *Disk Image / VM File*.

2. **Ingestão de Dados:**
   * Configuração dos módulos de ingestão para análise profunda do espaço não alocado (*Unallocated Space*).
   * O processamento demorou cerca de 10 a 15 minutos para varrer os setores do disco em busca de assinaturas conhecidas.

3. **Recuperação e Exame:**
   * Após a conclusão, a árvore de diretórios (*Data Sources*) populou com sucesso a pasta `Carved Files`.
   * O acesso à pasta revelou a recuperação bem-sucedida de ficheiros apagados que já não constavam na tabela do sistema de ficheiros.
   * Selecionou-se um ficheiro de exemplo (ex: `f0203056.jpg`). Através do painel inferior do Autopsy, procedeu-se à verificação da integridade do ficheiro via visualização **Hexadecimal** (para confirmar o *header* do ficheiro JPEG) e **Application** (para visualizar a imagem).

4. **Extração de Evidência:**
   * O ficheiro foi exportado com sucesso através da função *Extract File(s)*, gerando um artefacto limpo na diretoria de `Export` do caso, preservando-o para documentação e *Reporting*.

---

## 🧠 Conclusões de DFIR (Digital Forensics & Incident Response)

Este caso consolida várias premissas fundamentais na resposta a incidentes multiplataforma:

1. **Agnosticismo do File Carving:** A ausência de metadados (como os *inodes* no Linux) não impede a recuperação de dados. Desde que a funcionalidade TRIM não tenha destruído os blocos físicos (como visto no Case 1), os dados permanecem recuperáveis.
2. **Interoperabilidade de Ferramentas:** Ferramentas modernas de DFIR como o Autopsy conseguem interpretar imagens de disco Linux (`.dd` ou `.raw`) nativamente em Windows, agilizando a triagem de evidências sem a necessidade imediata de montar a imagem num sistema operativo Linux.
3. **Validação Hexadecimal:** A recuperação de *Carved Files* gera frequentemente falsos positivos ou ficheiros corrompidos. A validação manual do cabeçalho hexadecimal do ficheiro extraído é uma prática obrigatória do analista para confirmar a verdadeira natureza e integridade da evidência.

---
*Retornar ao [Menu Principal](../README.md)*