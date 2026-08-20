# 🔍 DFIR: Montagem e Emulação de Imagens E01 como RAW em Linux

## 📌 Scenario
Este laboratório aborda um desafio técnico recorrente em investigações de Digital Forensics: a incompatibilidade de formatos de matrizes periciais. Frequentemente, evidências adquiridas e armazenadas em formatos proprietários comprimidos, como o *Expert Witness Format* (`.E01`), exigem tradução para formato *Raw* (`.dd`) para permitir a análise nativa e o *parsing* por ferramentas *open-source* especializadas em infraestruturas Linux.

## 🎯 Objectives
* Instalar e operar ferramentas de virtualização forense num ambiente Linux (Ubuntu).
* Emular e montar contentores `.E01` em tempo real (*on-the-fly*) recorrendo a um *Virtual File System* (VFS).
* Garantir a preservação da evidência pericial original sem incorrer na duplicação física dos dados e no respetivo consumo de armazenamento adicional.

## 🧾 Evidence
* **Tipo de Evidência:** Imagem Forense Comprimida
* **Formato de Origem:** `.E01` (*Expert Witness Format*)
* **Formato Emulado (Destino):** RAW (`.dd`)

## 🛠️ Environment & Tools
* **Sistema Operativo:** Ubuntu Linux (*Forensic Workstation*)
* **Ferramentas:** Utilitário `xmount` (Virtual File System)
* **Gestor de Pacotes:** APT (Advanced Package Tool)

---

## 🔬 Methodology
1. **Isolamento da Evidência:** O ficheiro comprimido `Windows_Evidence_001.E01` foi isolado localmente a partir do diretório de rede partilhado. Este passo previne constrangimentos de I/O ou bloqueios de leitura inesperados induzidos pela latência da rede durante a montagem do disco virtual.
2. **Escalação de Privilégios e Instalação:** Através do terminal Linux, procedeu-se à escalação para o utilizador *root* (`sudo su`). Utilizou-se o gestor de pacotes para obter e instalar a dependência vital de emulação (`apt-get install -y xmount`).
3. **Emulação VFS:** Executou-se o comando de emulação indicando a *flag* restrita do formato de entrada (`--in ewf`). O utilitário leu os cabeçalhos comprimidos do ficheiro de origem e projetou o ficheiro abstrato `Windows_Evidence_001.dd` num diretório alvo designado.

---

## 🔎 Analysis

### Evidence 01
A execução do utilitário `xmount` gerou com sucesso a apresentação de um ficheiro com extensão `.dd` no diretório de montagem, refletindo o tamanho lógico (descompactado) da drive física original.

## 🧩 Findings
* **Finding 1 (Eficiência Pericial por Abstração):** O ficheiro virtual projetado permite que qualquer motor de análise ou ferramenta nativa de *Data Carving* baseada em Linux (como *The Sleuth Kit* ou *Foremost*) inspecione a imagem de forma ininterrupta, interpretando-a como uma cópia bit-a-bit legítima. Como a operação é suportada unicamente por um *Virtual File System*, atinge-se interoperabilidade absoluta sem desencadear a duplicação física de dados, economizando janelas temporais de clonagem e espaço em disco no laboratório.

---

## ⚠️ Forensic Considerations
* **Transitoriedade da Evidência Virtual:** O ficheiro `.dd` exposto pelo `xmount` é intrinsecamente efémero. Um reinício da *Workstation* (reboot) ou o esgotamento do processo VFS resultará na desintegração imediata da montagem virtual, obrigando à repetição do processo de emulação para retomar a análise.
* **Integridade do Contentor:** O ficheiro proprietário `.E01` operou de forma rigorosa em modo de leitura (*Read-Only*). A técnica de abstração garante que não foram conduzidas quaisquer operações de escrita sobre a evidência original.

## 📝 Conclusion
O obstáculo técnico da interoperabilidade de formatos periciais foi mitigado com sucesso através do recurso a emulação lógica *on-the-fly*. A capacidade de transitar fluidamente de E01 para DD consolida a preparação do laboratório, viabilizando o imediato escrutínio de baixo nível e a reconstrução de atividade utilizando as ferramentas especializadas do ecossistema *open-source*.
