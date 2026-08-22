# 🔬 Case 6: Deteção e Extração de Esteganografia

## 📝 Descrição e Cenário
A esteganografia é a prática de ocultar ficheiros, mensagens ou binários maliciosos dentro de outros ficheiros aparentemente inofensivos (como imagens ou ficheiros de áudio). Ao contrário da criptografia, que ofusca o conteúdo, a esteganografia ofusca a **existência** da própria comunicação, permitindo aos atacantes exfiltrar dados ou entregar *malware* sem alertar as soluções de segurança (EDR/Antivírus).

**Cenário de Investigação:** Durante a triagem forense ao computador de um suspeito, foram encontrados vários ficheiros multimédia (imagens `.png` e áudios `.wav`) que, apesar de abrirem normalmente e não apresentarem anomalias visuais ou sonoras, geraram suspeitas contextuais. O objetivo deste caso é submeter estes ficheiros a ferramentas de análise esteganográfica para identificar anomalias estatísticas, confirmar a presença de dados ocultos (Payloads) e extrair os artefactos.

## 🧰 Ferramentas Utilizadas
* **StegSpy / Ferramentas de Análise Estatística** (Para deteção primária)
* **Image Steganography** (Teste de extração - Algoritmo não compatível)
* **OpenStego** (Ferramenta open-source para extração de dados em imagens via LSB)
* **DeepSound** (Análise e extração de esteganografia em ficheiros de áudio)

---

## ⚙️ Metodologia e Execução

### Fase 1: Identificação e Tentativas de Extração (Imagem)
1. **Alvo:** `Model.png`
2. **Deteção:** O ficheiro foi inicialmente submetido a motores de análise estatística. O resultado acusou modificações anormais nos bits menos significativos (LSB - *Least Significant Bit*), confirmando a presença de dados ocultos.
3. **Tentativa 1 (Falha):** Utilizou-se a ferramenta *Image Steganography*. A ferramenta tentou descodificar o ficheiro, mas retornou um erro indicando que a imagem estava corrompida ou era inválida. *(Isto provou que o atacante não usou o algoritmo nativo desta ferramenta).*
4. **Tentativa 2 (Sucesso):** O ficheiro foi processado no **OpenStego**. Utilizando a função de *Data Extraction*, o algoritmo conseguiu ler a matriz de dados ocultos e extraiu com sucesso um ficheiro de texto (`Testing.txt`) contendo informações confidenciais em *plaintext*.

### Fase 2: Análise de Ficheiros de Áudio
1. **Alvo:** `Dangerous.wav` (Ficheiro Carrier)
2. **Abordagem:** Ficheiros de áudio não comprimidos (WAV/FLAC) são vetores perfeitos para ocultar grandes volumes de dados. A ferramenta **DeepSound** foi utilizada para analisar as frequências e o espetro do ficheiro.
3. **Extração:** Ao carregar o ficheiro *carrier* no DeepSound, a aplicação detetou imediatamente a presença de ficheiros embebidos. Utilizando a função *Extract secret files*, extraiu-se o *payload* malicioso oculto para análise subsequente.
---

## 🧠 Conclusões de DFIR (Digital Forensics & Incident Response)

1. **A Importância da Tentativa e Erro:** Como demonstrado na Fase 1, se uma ferramenta não consegue extrair dados, isso não significa que o ficheiro esteja limpo. A esteganografia tem múltiplos algoritmos (LSB, F5, OutGuess, JSteg). O analista forense deve testar o artefacto com várias ferramentas diferentes até encontrar correspondência algorítmica.
2. **Inspeção Humana é Insuficiente:** Visualizar a imagem `Model.png` ou ouvir o áudio `Dangerous.wav` não revela qualquer anomalia. As alterações ocorrem a um nível binário que os sentidos humanos não captam. A análise estatística de entropia é estritamente necessária.
3. **Evasão Avançada (Stegomalware):** Na resposta a incidentes moderna, os atacantes estão a embutir *scripts* de PowerShell e binários maliciosos em imagens hospedadas em redes sociais ou repositórios públicos. Quando um processo interno descarrega a imagem e a descodifica na memória, o *malware* é executado de forma "Fileless", iludindo sistemas tradicionais de deteção de intrusão (IDS).

---
*Retornar ao [Menu Principal](../README.md)*
