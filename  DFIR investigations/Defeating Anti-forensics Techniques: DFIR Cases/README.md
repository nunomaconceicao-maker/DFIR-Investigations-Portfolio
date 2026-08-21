# 🕵️‍♂️ Defeating Anti-forensics Techniques: DFIR Cases

Este repositório documenta a minha análise técnica e resolução de casos de estudo práticos focados na identificação e mitigação de técnicas anti-forenses.

A evasão forense é um dos maiores desafios na resposta a incidentes (IR) e análise forense digital (DFIR). Os cibercriminosos utilizam ativamente técnicas para destruir, ocultar ou ofuscar evidências. O objetivo deste projeto é demonstrar as metodologias e ferramentas utilizadas para ultrapassar estas barreiras.

## 🎯 Objetivos de Aprendizagem e Casos Investigados

* [x] **Case 1 & 2: SSD File Carving (Windows & Linux)**
  * **Foco:** Recuperação de ficheiros (*carving*) no espaço não alocado e o impacto crítico da funcionalidade **TRIM** na destruição de evidências em discos de estado sólido (SSD).
* [ ] **Case 3 & 4: Data Recovery de Partições Apagadas**
  * **Foco:** Restauro de tabelas de partições corrompidas, apagadas ou fundidas, e recuperação dos respetivos dados estruturados.
* [ ] **Case 5: Cracking de Passwords de Aplicações**
  * **Foco:** Extração de *hashes* e execução de ataques de dicionário/força bruta para aceder a ficheiros ofuscados ou encriptados.
* [ ] **Case 6: Deteção de Esteganografia**
  * **Foco:** Identificação de *payloads* e informações ocultas no interior de ficheiros multimédia (análise LSB).
* [ ] **Case 7: Alternate Data Streams (ADS)**
  * **Foco:** Deteção e extração de ficheiros ocultos em partições NTFS, utilizando as propriedades dos Alternate Data Streams.
* [ ] **Case 8: File Extension Mismatch**
  * **Foco:** Análise de assinaturas de ficheiros (*Magic Numbers*) através de editores hexadecimais para descobrir a verdadeira natureza de um ficheiro camuflado.
* [ ] **Case 9: Unpacking de Programas**
  * **Foco:** Deteção e desempacotamento de binários comprimidos/ofuscados (ex: UPX) para permitir a análise estática do *malware*.

---

## 🧰 Ferramentas Utilizadas

Durante estas investigações, utilizei as seguintes ferramentas forenses num ambiente controlado (Windows 11 / Windows Server / Linux):

* **Análise e Carving:** Autopsy, PhotoRec, Foremost
* **Recuperação de Partições:** TestDisk, Active@ Partition Recovery
* **Quebra de Passwords:** Hashcat, John the Ripper, Passware Kit
* **Análise Hexadecimal & Esteganografia:** HxD, Stegsolve, OpenStego
* **Análise de Ficheiros:** Detect It Easy (DIE), UPX, Sysinternals (Streams)

---

## 💡 Notas Técnicas em Destaque

### O Desafio do TRIM em Discos SSD
Durante as investigações de *File Carving*, ficou documentado que a presença da tecnologia **TRIM** ativada num SSD torna a recuperação de dados tradicionais virtualmente impossível. O sistema operativo notifica o controlador do disco sobre os blocos não utilizados, e o próprio hardware encarrega-se de os limpar fisicamente para otimizar futuras escritas (Garbage Collection).
* **Conclusão de DFIR:** Num cenário de Resposta a Incidentes, ao identificar que o alvo possui um SSD, o tempo de resposta e isolamento do sistema é o fator mais crítico.

---

## 📂 Estrutura do Repositório

Para cada caso de estudo, criei uma documentação detalhada (incluindo *screenshots*, linha de comandos utilizada e conclusões analíticas). Podes navegar pelos mesmos nas respetivas pastas:

* `📁 /Case_01_SSD_Carving_Windows`
* `📁 /Case_02_SSD_Carving_Linux` *(Em progresso)*
* `📁 /Case_03_Partition_Recovery` *(Por iniciar)*
* `📁 /Docs` *(Evidências e notas soltas)*

---

**Nota:** *Este repositório foi criado para fins exclusivamente educativos e de demonstração de competências no âmbito de Digital Forensics & Incident Response (DFIR).*

<div align="center">
  <b>🔐 Learn. Investigate. Defend.</b>
</div>
