# Guia Consolidado de Estudos: Operações de SOC e Vetores de Ataque

> **Fonte original:** TryHackMe, Trilha SOC Nível 1
>
> **Aviso importante:** este documento é um relatório didático e uma compilação de anotações técnicas baseadas nas aulas e nos laboratórios práticos. Destina-se ao estudo individual, à consulta rápida e à composição de um portfólio de aprendizado. Ele **não substitui** a realização dos módulos práticos e dos conteúdos originais da plataforma TryHackMe.

---

## Índice

1. [Visão geral da operação em um SOC](#1-visão-geral-da-operação-em-um-soc)
2. [Ciclo prático de triagem e resposta a alertas](#2-ciclo-prático-de-triagem-e-resposta-a-alertas)
3. [Sistemas como vetores de ataque](#3-sistemas-como-vetores-de-ataque)
4. [Estratégias de defesa e mitigação](#4-estratégias-de-defesa-e-mitigação)
5. [Resumo didático para fixação](#5-resumo-didático-para-fixação)
6. [Referências técnicas](#6-referências-técnicas)

---

## 1. Visão geral da operação em um SOC

Um **Centro de Operações de Segurança (SOC)** é o núcleo responsável por monitorar, detectar, analisar e responder a ameaças cibernéticas em uma organização, 24 horas por dia, 7 dias por semana.

### 1.1 Estrutura e papéis da equipe Blue Team

Em um ambiente Blue Team, as responsabilidades são divididas para garantir eficiência no atendimento e na investigação:

| Papel | Função principal | Atividades no SOC |
|---|---|---|
| **Analista SOC Nível 1 (L1)** | Monitoramento e triagem | Primeira linha de defesa; analisa alertas diários, filtra falsos positivos e aplica contenções primárias. |
| **Analista Sênior (L2 / L3)** | Investigação avançada | Atua em alertas escalonados, realiza análise tática detalhada e investiga incidentes complexos. |
| **Engenheiro de SOC** | Infraestrutura e regras | Mantém plataformas de SIEM/SOAR, configura regras de detecção e garante a ingestão correta de logs. |
| **Gerente de SOC** | Gestão e métricas | Gerencia a equipe, garante o cumprimento de SLAs e reporta riscos para a diretoria. |
| **Incident Responder (DFIR)** | Resposta a crises | Atua em emergências graves (ex.: infecções por ransomware) para erradicação e recuperação. |

---

## 2. Ciclo prático de triagem e resposta a alertas

A rotina diária de um Analista SOC Nível 1 envolve a recepção e o tratamento estruturado de eventos de segurança.

### 2.1 Fluxo metodológico de atendimento

```text
[ 1. Alerta SIEM ] --> [ 2. Extração de IoC ] --> [ 3. Comunicação/Escalonamento ] --> [ 4. Contenção no Firewall ]
```

- **Recepção do alerta:** identificação de uma anomalia registrada pelo sistema de monitoramento.
- **Identificação de IoCs:** extração de Indicadores de Comprometimento (ex.: endereços IP maliciosos, hashes de arquivos ou domínios suspeitos).
- **Comunicação tática:** registro do progresso no sistema de chamados e notificação ao Analista Sênior ou à liderança quando necessário.
- **Contenção primária:** execução de bloqueios temporários (como regras de firewall) para impedir a expansão do ataque.

---

## 3. Sistemas como vetores de ataque

Para defender um ambiente, o analista precisa entender como os atacantes exploram os sistemas corporativos como porta de entrada (vetores de ataque).

### 3.1 Classificação dos vetores de entrada

- **Ataques liderados pelo fator humano:** uso de engenharia social (phishing), download de arquivos maliciosos ou reuso de credenciais vazadas.
- **Vulnerabilidades de software (falhas de código):** erros no desenvolvimento de softwares ou sistemas operacionais. Quando catalogadas publicamente, recebem um código **CVE** (*Common Vulnerabilities and Exposures*).
- **Ataques à cadeia de suprimentos (*supply chain*):** comprometimento de fornecedores confiáveis ou de bibliotecas de código de terceiros para distribuir malware em massa.
- **Configurações incorretas (*misconfigurations*):** falhas de implementação causadas por equipes de TI (ex.: senhas padrão, serviços desnecessários expostos ou permissões públicas em nuvem).

---

## 4. Estratégias de defesa e mitigação

A resposta a um vetor de ataque varia de acordo com a natureza da falha identificada:

```text
                 +-- Vulnerabilidade de código (CVE) --> Aplicação de patch de atualização
Vetor detectado -+
                 +-- Configuração incorreta ----------> Reconfiguração / Hardening (CIS Benchmarks)
```

### 4.1 Controles compensatórios no SOC

- **Patch Management:** atualização oficial fornecida pelo fabricante para corrigir falhas de código.
- **Virtual Patching (WAF / IPS):** regras de assinatura aplicadas em firewalls de aplicação ou IPS para bloquear tentativas de exploração enquanto o patch oficial não é instalado.
- **Auditorias e Hardening:** aplicação de padrões de segurança, como os CIS Benchmarks, para desativar recursos inseguros e reduzir a superfície de ataque.
- **Testes de Penetração (Pentest):** simulações autorizadas de ataques cibernéticos para descobrir falhas antes que agentes maliciosos as explorem.

---

## 5. Resumo didático para fixação

- **O que é um IoC?**
  É uma evidência digital (como um IP ou um hash) que indica que um sistema foi comprometido.
- **Qual a diferença entre bug e misconfiguration?**
  Um bug é uma falha no código escrito pelo fabricante; uma misconfiguration é um erro na forma como a equipe configurou o sistema.
- **Qual é o papel da contenção primária?**
  Interromper a ação do atacante imediatamente (ex.: bloquear um IP no firewall) para ganhar tempo enquanto a investigação aprofundada é realizada.

---

## 6. Referências técnicas

- **Plataforma de treinamento:** [TryHackMe, Trilha SOC Level 1](https://tryhackme.com/)
- **Estrutura de táticas e técnicas:** [MITRE ATT&CK Framework](https://attack.mitre.org/)
- **Padrões de configuração segura:** [Center for Internet Security (CIS) Benchmarks](https://www.cisecurity.org/cis-benchmarks)
