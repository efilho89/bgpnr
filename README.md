# bgpnr — BGP Network Recon

Ferramenta de linha de comando para consulta detalhada de informações de ASN (Autonomous System Number), combinando dados do **PeeringDB** e da **BGPView API**.

Inspirado no [bgprr](https://github.com/remontti/bgprr), porém com mais recursos: política de peering, facilities, contatos, RS peer, BFD e muito mais.

---

## Demonstração

```
  bgpnr ─ BGP Network Recon

  ASN               AS13335
  Nome              Cloudflare, Inc.
  País              US
  RIR               ARIN
  Alocado em        14/09/2010
  Website           https://www.cloudflare.com
  Tipo              Content
  Escopo            Global
  IPv6              Sim
  IXs               78
  Facilities        36
  IRR AS-Set        RIPE::AS-CLOUDFLARE

──────────────────────────────────────────────────────────────────────
  Política de Peering (PeeringDB)
──────────────────────────────────────────────────────────────────────
  Política geral      Open
  URL da política     https://www.cloudflare.com/peering-policy
  Nunca via RS        Não

──────────────────────────────────────────────────────────────────────
  Internet Exchanges (PeeringDB)
──────────────────────────────────────────────────────────────────────
  Internet Exchange      Velocidade  IPv4              IPv6                RS   BFD
  ---------------------  ----------  ----------------  ------------------  ---  ---
  ● AMS-IX               10 Gbps     80.249.211.130    2001:7f8:1::a502:…  RS   BFD
  ● DE-CIX Frankfurt     10 Gbps     80.81.194.23      2001:7f8::3417:0:1  RS   —
  ● IX.br São Paulo      10 Gbps     187.16.216.185    2001:12f8::185      RS   BFD
```

---

## Funcionalidades

| Seção                          | Fonte         |
|-------------------------------|---------------|
| Info básica (ASN, País, RIR)  | BGPView       |
| Tipo, escopo, tráfego, ratio  | PeeringDB     |
| IRR AS-Set, Looking Glass     | PeeringDB     |
| Política de Peering           | PeeringDB     |
| Contatos (NOC / Policy / Tech)| PeeringDB     |
| Prefixos IPv4 / IPv6          | BGPView       |
| Upstream Providers            | BGPView       |
| Downstream Customers          | BGPView       |
| Internet Exchanges (RS / BFD) | PeeringDB     |
| Facilities / Colocation       | PeeringDB     |

---

## Requisitos

- Python 3.6+
- `python3-requests`

```bash
sudo apt install python3-requests
```

**Opcional** — tabelas mais formatadas:

```bash
sudo apt install python3-tabulate
```

---

## Instalação

### Via wget (instalação rápida)

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
bgpnr 15169
```

---

## Uso

### Por ASN (direto)

```bash
bgpnr 13335        # Cloudflare
bgpnr 15169        # Google
bgpnr AS1916       # Rede Nacional de Ensino e Pesquisa (RNP)
bgpnr 6939         # Hurricane Electric
```

### Por nome de organização

Quando o argumento não é um número, o bgpnr busca por nome e exibe uma lista para seleção:

```bash
bgpnr Cloudflare
bgpnr Embratel
bgpnr "NTT Communications"
bgpnr ANSP
```

### Modo interativo

Sem argumentos, entra no modo interativo:

```bash
bgpnr
```

```
  bgpnr ─ BGP Network Recon
  Consulta ASN via PeeringDB + BGPView

  ASN  →  ex: 1916, 15169, AS6939
  Nome →  ex: Cloudflare, ANSP, Embratel, NTT
  Ctrl+C para sair

  ASN / Organização: _
```

---

## API Key do PeeringDB (opcional)

O acesso ao PeeringDB é público por padrão. Para obter rate limit mais alto e acesso a dados restritos, configure uma API Key:

1. Crie uma conta em [peeringdb.com](https://www.peeringdb.com)
2. Acesse **Profile → API Keys → Add**
3. Exporte a chave antes de usar o bgpnr:

```bash
export PEERINGDB_API_KEY=sua_chave_aqui
bgpnr 13335
```

Para tornar permanente, adicione ao `~/.bashrc`:

```bash
echo 'export PEERINGDB_API_KEY=sua_chave_aqui' >> ~/.bashrc
source ~/.bashrc
```

---

## Exemplos de saída

### Upstreams e Downstreams

```
──────────────────────────────────────────────────────────────────────
  Upstream Providers (BGPView)
──────────────────────────────────────────────────────────────────────
  ASN      Nome                    Descrição          IPv4  IPv6
  -------  ----------------------  -----------------  ----  ----
  AS174    Cogent Communications   COGENT-174          ✔     ✔
  AS3356   Lumen Technologies      LEVEL3              ✔     ✔
  AS6461   Zayo Bandwidth          MFNX                ✔     ✘
```

### Prefixos

```
──────────────────────────────────────────────────────────────────────
  Prefixos Anunciados (BGPView)
──────────────────────────────────────────────────────────────────────

  IPv4 — 15 prefixo(s)
  Prefixo          Descrição                            RIR
  ---------------  -----------------------------------  ----
  1.1.1.0/24       APNIC and Cloudflare DNS Resolver    APNIC
  104.16.0.0/12    CLOUDFLARENET                        ARIN

  IPv6 — 6 prefixo(s)
  Prefixo              Descrição        RIR
  -------------------  ---------------  ----
  2606:4700::/32       CLOUDFLARENET    ARIN
```

### Facilities

```
──────────────────────────────────────────────────────────────────────
  Facilities / Colocation (PeeringDB)
──────────────────────────────────────────────────────────────────────
  Facility                              Localização
  ------------------------------------  -----------------------
  Equinix AM7 (Science Park)            Amsterdam, NL
  Equinix DA1                           Dallas, US
  Equinix SP2 (antiga ISACO / Tivit)    São Paulo, BR

  Total: 36 facility(ies)
```

---

## Distribuições testadas

| Distro             | Status |
|--------------------|--------|
| Debian 12/13       | ✔      |
| Ubuntu 22.04/24.04 | ✔      |
| Kali Linux         | ✔      |
| Rocky Linux 9      | ✔      |
| Arch Linux         | ✔      |

---

## Fontes de dados

| API       | URL                                                | Dados                                      |
|-----------|----------------------------------------------------|--------------------------------------------|
| BGPView   | [api.bgpview.io](https://api.bgpview.io)           | ASN info, prefixos, upstreams, downstreams |
| PeeringDB | [peeringdb.com/api](https://www.peeringdb.com/api) | IXs, facilities, política, contatos        |

---

## Comparação com bgprr

| Recurso                      | bgprr | bgpnr |
|------------------------------|-------|-------|
| Info básica de ASN           | ✔     | ✔     |
| Prefixos IPv4/IPv6           | ✔     | ✔     |
| Upstreams                    | ✔     | ✔     |
| Downstreams                  | ✔     | ✔     |
| Internet Exchanges           | ✔     | ✔     |
| Route Server peer (RS)       | ✘     | ✔     |
| BFD support                  | ✘     | ✔     |
| Política de Peering          | ✘     | ✔     |
| IRR AS-Set                   | ✘     | ✔     |
| Looking Glass / Route Server | ✘     | ✔     |
| Contatos NOC/Policy/Tech     | ✘     | ✔     |
| Facilities / Colocation      | ✘     | ✔     |
| Tipo/escopo/tráfego da rede  | ✘     | ✔     |
| Autenticação PeeringDB       | ✘     | ✔     |
| Busca por nome               | ✔     | ✔     |
| Sem dependências externas    | ✘     | ✔     |

---

## Licença

MIT License — veja [LICENSE](LICENSE)

---

## Créditos

- Inspirado no [bgprr](https://github.com/remontti/bgprr) de [@remontti](https://github.com/remontti)
- Dados fornecidos por [BGPView](https://bgpview.io) e [PeeringDB](https://www.peeringdb.com)
