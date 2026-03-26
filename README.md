<div align="center">

```
   ██████╗  ██████╗ ██████╗ ███╗  ██╗██████╗
   ██╔══██╗██╔════╝ ██╔══██╗████╗ ██║██╔══██╗
   ██████╦╝██║  ███╗██████╔╝██╔██╗██║██████╔╝
   ██╔══██╗██║   ██║██╔═══╝ ██║╚████║██╔══██╗
   ██████╔╝╚██████╔╝██║     ██║ ╚███║██║  ██║
   ╚═════╝  ╚═════╝ ╚═╝     ╚═╝  ╚══╝╚═╝  ╚═╝
```

**BGP Network Recon** — ferramenta de linha de comando para consulta detalhada de ASNs

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
![Python](https://img.shields.io/badge/Python-3.6%2B-blue)
![Platform](https://img.shields.io/badge/Platform-Linux-lightgrey)

</div>

---

## O que é o bgpnr?

O **bgpnr** (BGP Network Recon) é uma ferramenta CLI que combina dados do **RIPE Stat** e do **PeeringDB** para entregar uma visão completa de qualquer ASN diretamente no terminal — prefixos, upstreams, downstreams, Internet Exchanges, facilities, política de peering, contatos e muito mais.

Inspirado no [bgprr](https://github.com/remontti/bgprr), mas com escopo maior: comparação de IXs entre ASNs, análise de oportunidades de peering, suporte a API Key, layout visual com box-drawing e ASCII art.

---

## Requisitos

- Python 3.6+
- `python3-requests`

```bash
sudo apt install python3-requests
```

Sem outras dependências obrigatórias. Funciona em qualquer distribuição Linux moderna.

---

## Instalação

### Instalação rápida (wget)

```bash
sudo wget https://raw.githubusercontent.com/efilho89/bgpnr/main/bgpnr -O /usr/local/bin/bgpnr
sudo chmod +x /usr/local/bin/bgpnr
```

### Via git clone

```bash
git clone https://github.com/efilho89/bgpnr.git
cd bgpnr
sudo cp bgpnr /usr/local/bin/bgpnr
sudo chmod +x /usr/local/bin/bgpnr
```

### Verificar instalação

```bash
bgpnr 13335
```

---

## Uso

```
bgpnr <ASN|nome>                  Consulta um ASN ou busca por nome
bgpnr --compare <ASN1> <ASN2>     IXs em comum entre dois ASNs
bgpnr --transit <ASN>             Trânsito IP vs oportunidades de peering
bgpnr --help                      Exibe ajuda
bgpnr                             Modo interativo
```

---

## Referência de Comandos

### 1. Consulta de ASN

Pesquisa completa por número de ASN. Aceita qualquer um dos formatos abaixo:

```bash
bgpnr 13335
bgpnr AS13335
bgpnr as13335
```

**O que é exibido:**

| Seção                        | Fonte        | Descrição                                                  |
|------------------------------|--------------|------------------------------------------------------------|
| Cabeçalho                    | RIPE + PDB   | ASN, nome, país, RIR, data de alocação, status de anúncio, website, tipo, escopo, tráfego, ratio, IPv6, multicast, total de IXs e facilities, max prefixos IPv4/v6, IRR AS-Set, Looking Glass |
| Política de Peering          | PeeringDB    | Política geral (Open/Selective/Restrictive/No), URL, restrição local, ratio, contratos, "nunca via Route Server" |
| Contatos                     | PeeringDB    | Papéis NOC / Policy / Tech com nome, e-mail e telefone     |
| Abuse Contact                | RIPE Stat    | E-mail(s) para reporte de abuso                            |
| Prefixos Anunciados          | RIPE Stat    | Todos os prefixos IPv4 e IPv6 em grid visual               |
| RPKI / IRR Consistency       | RIPE Stat    | Status RPKI por prefixo (Valid/Invalid/Not Found), cobertura ROA, consistência IRR/WHOIS vs BGP |
| Upstream Providers           | RIPE Stat    | ASNs que fornecem trânsito (relação left)                  |
| Downstream Customers         | RIPE Stat    | ASNs que recebem trânsito (relação right)                  |
| Peers                        | RIPE Stat    | Vizinhos sem relação definida (relação uncertain), até 50  |
| Internet Exchanges           | PeeringDB    | Nome do IX, velocidade, IPs IPv4/IPv6, RS peer, BFD        |
| Facilities / Colocation      | PeeringDB    | Data centers onde o AS está presente                       |

**Exemplos:**

```bash
bgpnr 15169        # Google
bgpnr 13335        # Cloudflare
bgpnr 1916         # RNP — Rede Nacional de Ensino e Pesquisa
bgpnr 6939         # Hurricane Electric
bgpnr AS262979     # Aceita prefixo "AS"
```

---

### 2. Busca por Nome

Quando o argumento não é um número, o bgpnr busca por nome no PeeringDB e apresenta uma lista numerada para seleção:

```bash
bgpnr Cloudflare
bgpnr Embratel
bgpnr "NTT Communications"
bgpnr ANSP
bgpnr Locaweb
```

**Fluxo:**

```
  Buscando Cloudflare ...

┌──┬──────────┬──────────────────────┬──────────────────────────┐
│ #│ ASN      │ Nome                 │ Descrição                │
├──┼──────────┼──────────────────────┼──────────────────────────┤
│ 1│ AS13335  │ Cloudflare, Inc.     │ CLOUDFLARENET            │
│ 2│ AS209242 │ Cloudflare WARP      │                          │
└──┴──────────┴──────────────────────┴──────────────────────────┘

  Selecione (Enter cancela): 1
```

Após selecionar, exibe a consulta completa do ASN escolhido.

---

### 3. Modo Interativo

Sem argumentos, entra no modo interativo com menu de ajuda:

```bash
bgpnr
```

```
  ┌──────────────────────────────────────────────────────────────┐
  │ bgpnr 13335               →  consulta ASN                   │
  │ bgpnr Cloudflare          →  busca por nome                 │
  │ bgpnr --compare 1234 5678 →  IXs em comum entre dois ASNs  │
  │ bgpnr --transit 1234      →  trânsito IP vs IXs (peering)  │
  └──────────────────────────────────────────────────────────────┘

  ASN / Organização: _
```

Use `Ctrl+C` para sair a qualquer momento.

---

### 4. Comparar IXs entre Dois ASNs (`--compare`)

Descobre quais Internet Exchanges dois ASNs têm em comum — útil para avaliar peering bilateral direto.

```bash
bgpnr --compare <ASN1> <ASN2>
bgpnr -c <ASN1> <ASN2>
bgpnr compare <ASN1> <ASN2>
```

**Exemplos:**

```bash
bgpnr --compare 13335 15169      # Cloudflare vs Google
bgpnr --compare 1916 28598       # RNP vs Claro Brasil
bgpnr -c AS6939 AS174            # Hurricane Electric vs Cogent
```

**O que é exibido:**

1. **Resumo**: total de IXs de cada ASN e quantos têm em comum
2. **IXs em Comum**: tabela com nome do IX, velocidade de cada ASN, endereços IPv4/IPv6 de cada ASN e status de RS peer
3. **IXs Exclusivos**: IXs que cada ASN possui e o outro não

**Caso de uso típico:** verificar se dois peers potenciais já estão presentes no mesmo IX antes de abrir negociação de peering.

---

### 5. Trânsito IP vs IXs — Oportunidades de Peering (`--transit`)

Para um dado ASN, lista todos os seus **upstreams de trânsito** (via RIPE Stat) e verifica em quais deles o AS já existe no mesmo IX — identificando oportunidades de substituir trânsito pago por peering gratuito via IX.

```bash
bgpnr --transit <ASN>
bgpnr -t <ASN>
bgpnr transit <ASN>
```

**Exemplos:**

```bash
bgpnr --transit 1916       # RNP
bgpnr --transit 28598      # Claro Brasil
bgpnr -t 262979
```

**O que é exibido:**

1. **Resumo**: ASN consultado, total de IXs e total de upstreams a verificar
2. **Oportunidades de Peering Direto**: upstreams que compartilham pelo menos um IX — com detalhes do IX, velocidade dos dois lados, IPs e RS
3. **Upstreams sem IX em Comum**: upstreams verificados mas sem IX compartilhado (com motivo)
4. **Não encontrados no PeeringDB**: upstreams sem presença no PeeringDB

**Caso de uso típico:** operador de rede quer reduzir custo de trânsito. Roda `--transit` no próprio ASN e vê quais upstreams já estão no mesmo IX — candidatos imediatos a peering settlement-free.

> **Nota:** este modo faz diversas chamadas à API (um request por upstream). Para ASNs com muitos upstreams, pode levar alguns segundos.

---

## API Key do PeeringDB (opcional, recomendado)

Por padrão o bgpnr acessa o PeeringDB sem autenticação (acesso público). Com uma API Key você obtém:

- Rate limit muito maior (evita erros `429 Too Many Requests`)
- Acesso a dados marcados como privados ou restritos a membros

### Como configurar

1. Crie uma conta gratuita em [peeringdb.com](https://www.peeringdb.com)
2. Acesse **Profile → API Keys → Add**
3. Gere uma chave com permissão de leitura

### Uso temporário (sessão atual)

```bash
export PEERINGDB_API_KEY=sua_chave_aqui
bgpnr 13335
```

### Uso permanente

```bash
echo 'export PEERINGDB_API_KEY=sua_chave_aqui' >> ~/.bashrc
source ~/.bashrc
```

Quando a chave está ativa, o bgpnr exibe `PeeringDB: API Key ativa` no rodapé da saída.

---

## Exemplos de Saída

### RPKI e IRR Consistency

```
┌────────────────────────────────────────────────────────────────────────────┐
│ ⚑  RPKI  /  IRR Consistency                                                │
└────────────────────────────────────────────────────────────────────────────┘

  IPv4  — 15 prefixo(s)
    RPKI  :  ✔ 14 válidos  ? 1 sem ROA    Cobertura: 93%
    IRR   :  ⚠  1 prefixo(s) sem route object no IRR / WHOIS

┌─────────────────────┬─────────────┬──────────────────────┬─────┬─────┐
│ Prefixo             │ RPKI Status │ IRR / Fonte          │ BGP │ IRR │
├─────────────────────┼─────────────┼──────────────────────┼─────┼─────┤
│ 1.1.1.0/24          │ ✔ VALID     │ APNIC-NONAUTH        │  ✔  │  ✔  │
│ 104.16.0.0/12       │ ✔ VALID     │ ARIN                 │  ✔  │  ✔  │
│ 192.0.2.0/24        │ ✘ INVALID   │ —                    │  ✔  │  ✘  │
│ 198.51.100.0/24     │ ? NOT FOUND │ RIPE                 │  ✔  │  ✔  │
└─────────────────────┴─────────────┴──────────────────────┴─────┴─────┘
```

**Interpretação dos status RPKI:**

| Status       | Significado                                                              |
|--------------|--------------------------------------------------------------------------|
| `✔ VALID`    | ROA existe, origin AS correto, prefixo dentro do max-length              |
| `✘ INVALID`  | ROA existe mas origin AS errado ou prefixo mais específico que max-length — pode indicar hijack ou erro de configuração |
| `? NOT FOUND`| Nenhum ROA cobre este prefixo — sem proteção RPKI                        |

**Interpretação das colunas BGP / IRR:**

| Col | `✔`                              | `✘`                                             |
|-----|----------------------------------|-------------------------------------------------|
| BGP | Prefixo visto no BGP global      | Registrado no IRR mas não anunciado (rota ghost)|
| IRR | Route object existe no IRR/WHOIS | Anunciado no BGP sem route object (sem filtro)  |

---

### Cabeçalho de um ASN

```
┌────────────────────────────────────────────────────────────────────────────┐
│ ◈  AS13335  —  Cloudflare, Inc.                                            │
└────────────────────────────────────────────────────────────────────────────┘

  ┌──────────────────────────────────┬──────────────────────────────────┐
  │ ASN          AS13335             │ Tipo         Content             │
  │ Nome         Cloudflare, Inc.    │ Escopo       Global              │
  │ País         US                  │ Tráfego      100-1000Gbps        │
  │ RIR          ARIN                │ IPv6          ✔ SIM              │
  │ Website      https://cloudflare… │ IXs          78                  │
  │                                  │ IRR AS-Set   RIPE::AS-CLOUDFLARE │
  └──────────────────────────────────┴──────────────────────────────────┘
```

### Internet Exchanges

```
┌──────────────────────────┬───────────┬─────────────────┬──────────────────┬──────┬──────┐
│ Internet Exchange        │ Velocidade│ IPv4            │ IPv6             │ RS   │ BFD  │
├──────────────────────────┼───────────┼─────────────────┼──────────────────┼──────┼──────┤
│ ● AMS-IX                 │  10 Gbps  │ 80.249.211.130  │ 2001:7f8:1::…   │  RS  │  BFD │
│ ● DE-CIX Frankfurt       │  10 Gbps  │ 80.81.194.23    │ 2001:7f8::3417… │  RS  │  —   │
│ ● IX.br São Paulo        │  10 Gbps  │ 187.16.216.185  │ 2001:12f8::185  │  RS  │  BFD │
└──────────────────────────┴───────────┴─────────────────┴──────────────────┴──────┴──────┘

  Total: 78 IX(s)
```

### Comparação de IXs

```bash
bgpnr --compare 13335 15169
```

```
┌──────────┬──────────────────┬───────┬──────┐
│ AS13335  │ Cloudflare, Inc. │ 78    │ IXs  │
│ AS15169  │ Google LLC       │ 62    │ IXs  │
│ Em comum │                  │ 41    │ IXs  │
└──────────┴──────────────────┴───────┴──────┘

┌────────────────────────────────────────────────────────────────────────────┐
│ ★  IXs em Comum  (41)  —  AS13335 ↔ AS15169                               │
└────────────────────────────────────────────────────────────────────────────┘

┌──────────────────────┬──────────┬──────────┬──────────────┬──────────────┬──────┬──────┐
│ Internet Exchange    │ Vel13335 │ Vel15169 │ IPv4 13335   │ IPv4 15169   │ RS…  │ RS…  │
├──────────────────────┼──────────┼──────────┼──────────────┼──────────────┼──────┼──────┤
│ AMS-IX               │ 10 Gbps  │ 100 Gbps │ 80.249.211…  │ 80.249.208…  │  RS  │  RS  │
│ DE-CIX Frankfurt     │ 10 Gbps  │ 10 Gbps  │ 80.81.194.…  │ 80.81.192.…  │  RS  │  RS  │
└──────────────────────┴──────────┴──────────┴──────────────┴──────────────┴──────┴──────┘
```

### Análise de Trânsito

```bash
bgpnr --transit 1916
```

```
┌────────────────────────────────────────────────────────────────────────────┐
│ ★  Oportunidades de Peering Direto  (3)                                    │
└────────────────────────────────────────────────────────────────────────────┘

  Estes upstreams estão no mesmo IX — candidatos a peering gratuito.

  AS174   Cogent Communications  — 2 IX(s)
  ┌────────────────┬──────────┬──────────┬─────────────┬─────────────┬──────┬──────┐
  │ IX             │ Vel Meu  │ Vel AS174│ IPv4 Meu    │ IPv4 AS174  │ RS…  │ RS…  │
  ├────────────────┼──────────┼──────────┼─────────────┼─────────────┼──────┼──────┤
  │ IX.br São Paulo│ 10 Gbps  │ 100 Gbps │ 187.16.x.x  │ 187.16.y.y  │  RS  │  RS  │
  └────────────────┴──────────┴──────────┴─────────────┴─────────────┴──────┴──────┘
```

---

## Referência Rápida de Badges e Símbolos

| Símbolo / Badge          | Significado                                              |
|--------------------------|----------------------------------------------------------|
| `● verde`                | IX operacional                                           |
| `● vermelho`             | IX não operacional                                       |
| `RS`  (verde)            | Conectado ao Route Server do IX                          |
| `—`   (cinza, no RS)     | Não conectado ao Route Server                            |
| `BFD` (ciano)            | Suporta BFD (Bidirectional Forwarding Detection)         |
| `—`   (cinza, no BFD)    | Sem suporte a BFD                                        |
| `✔ SIM` (verde)          | Funcionalidade ativa (IPv6, multicast etc.)              |
| `✘ NÃO` (vermelho)       | Funcionalidade inativa                                   |
| `▲ Upstream`             | Provedor de trânsito (o ASN recebe trânsito destes)      |
| `▼ Downstream`           | Cliente de trânsito (estes recebem trânsito do ASN)      |
| `↔ Peers`                | Relação de peering não classificada                      |
| `★ Oportunidade`         | Upstream que já está no mesmo IX (candidato a peering)   |

---

## Glossário

| Termo               | Descrição                                                                      |
|---------------------|--------------------------------------------------------------------------------|
| **ASN**             | Autonomous System Number — identificador único de uma rede na internet         |
| **IX / IXP**        | Internet Exchange Point — ponto de troca de tráfego onde redes se interconectam |
| **RS**              | Route Server — servidor de rotas do IX que facilita o peering multilateral     |
| **BFD**             | Bidirectional Forwarding Detection — protocolo de detecção rápida de falhas    |
| **RPKI**            | Resource Public Key Infrastructure — sistema de validação criptográfica de rotas BGP via ROAs |
| **ROA**             | Route Origin Authorization — registro que declara qual AS pode anunciar um prefixo e o max-length permitido |
| **RPKI Valid**      | Prefixo coberto por ROA com origin AS e max-length corretos                    |
| **RPKI Invalid**    | ROA existe mas origin AS está errado ou prefixo mais específico que max-length — risco de hijack |
| **RPKI Not Found**  | Nenhum ROA cobre o prefixo — sem proteção RPKI                                 |
| **IRR**             | Internet Routing Registry — banco de dados de route objects e AS-Sets usados para filtragem |
| **Route Object**    | Registro no IRR que documenta um prefixo e seu AS de origem                    |
| **IRR AS-Set**      | Conjunto de ASes registrado em um IRR para filtragem de prefixos               |
| **Looking Glass**   | Ferramenta pública para visualizar rotas BGP a partir dos roteadores do AS     |
| **Upstream**        | Provedor de trânsito IP — de quem o AS recebe conectividade para a internet    |
| **Downstream**      | Cliente de trânsito — AS que recebe conectividade através do AS consultado     |
| **Peering**         | Acordo de troca de tráfego direta entre dois ASes, geralmente sem custo        |
| **Settlement-free** | Peering sem custo financeiro, típico em IXs                                    |
| **Facility**        | Data center onde o AS possui equipamento físico (colocation)                   |
| **RIR**             | Regional Internet Registry (ARIN, RIPE, LACNIC, APNIC, AFRINIC)               |
| **Prefix**          | Bloco de endereços IP anunciado via BGP (ex: 1.1.1.0/24)                      |
| **Max-length**      | Comprimento máximo de prefixo permitido em um ROA (ex: /24 não permite /25)   |
| **Abuse Contact**   | E-mail para reporte de atividades abusivas originadas do AS                    |

---

## Fontes de Dados

| API        | Endpoint base                        | Dados fornecidos                                                   |
|------------|--------------------------------------|--------------------------------------------------------------------|
| RIPE Stat  | `stat.ripe.net/data`                 | Visão geral, prefixos anunciados, vizinhos BGP, RPKI/IRR consistency, abuse contact |
| PeeringDB  | `www.peeringdb.com/api`              | IXs, facilities, política de peering, contatos, IRR AS-Set, LG    |

**Endpoints RIPE Stat utilizados:**

| Endpoint                   | Uso                                              |
|----------------------------|--------------------------------------------------|
| `as-overview`              | Nome, holder, alocação, status de anúncio        |
| `announced-prefixes`       | Lista de prefixos IPv4/v6 anunciados             |
| `asn-neighbours`           | Upstreams, downstreams e peers BGP               |
| `as-routing-consistency`   | RPKI status + consistência IRR vs BGP            |
| `abuse-contact-finder`     | E-mail de abuse do AS                            |

O bgpnr não depende da BGPView API (que foi descontinuada) — usa exclusivamente RIPE Stat e PeeringDB.

---

## Comparação com bgprr

| Recurso                          | bgprr | bgpnr |
|----------------------------------|-------|-------|
| Info básica de ASN               | ✔     | ✔     |
| Prefixos IPv4/IPv6               | ✔     | ✔     |
| Upstreams / Downstreams          | ✔     | ✔     |
| Internet Exchanges               | ✔     | ✔     |
| Busca por nome                   | ✔     | ✔     |
| Route Server peer (RS)           | ✘     | ✔     |
| BFD support                      | ✘     | ✔     |
| Política de Peering              | ✘     | ✔     |
| IRR AS-Set                       | ✘     | ✔     |
| Looking Glass                    | ✘     | ✔     |
| Contatos NOC / Policy / Tech     | ✘     | ✔     |
| Abuse Contact                    | ✘     | ✔     |
| **RPKI por prefixo (Valid/Invalid/Not Found)** | ✘ | ✔ |
| **IRR Consistency (BGP vs route objects)**     | ✘ | ✔ |
| **Cobertura ROA com percentual**               | ✘ | ✔ |
| **Data de alocação do ASN**                    | ✘ | ✔ |
| **Max prefixos IPv4/IPv6**                     | ✘ | ✔ |
| Facilities / Colocation          | ✘     | ✔     |
| Tipo / escopo / tráfego da rede  | ✘     | ✔     |
| Autenticação PeeringDB (API Key) | ✘     | ✔     |
| Comparar IXs entre dois ASNs     | ✘     | ✔     |
| Análise de oportunidade peering  | ✘     | ✔     |
| Layout visual com box-drawing    | ✘     | ✔     |
| ASCII art logo                   | ✘     | ✔     |
| Sem BGPView (API offline)        | ✘     | ✔     |
| Sem dependências externas extras | ✘     | ✔     |

---

## Distribuições Testadas

| Distro              | Status |
|---------------------|--------|
| Debian 12 / 13      | ✔      |
| Ubuntu 22.04 / 24.04| ✔      |
| Kali Linux          | ✔      |
| Rocky Linux 9       | ✔      |
| Arch Linux          | ✔      |

---

## Atualização

```bash
sudo wget https://raw.githubusercontent.com/efilho89/bgpnr/main/bgpnr -O /usr/local/bin/bgpnr
sudo chmod +x /usr/local/bin/bgpnr
```

---

## Licença

MIT License — veja [LICENSE](LICENSE)

---

## Créditos

- Inspirado no [bgprr](https://github.com/remontti/bgprr) de [@remontti](https://github.com/remontti)
- Dados fornecidos por [RIPE Stat](https://stat.ripe.net) e [PeeringDB](https://www.peeringdb.com)
