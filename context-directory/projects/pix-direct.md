# Projeto Pix Direto — Unlimit as a Direct Participant in the SPI

Last updated: 2026-10-06
Owner: Andrey Novikov · Counterparty: **Celcoin** · Entity: Unlimit Brasil IP

> **On the name.** You called this "SoCoin". There is no SoCoin anywhere in the
> mailbox, Drive or the Brazilian market. Everything you described — the Pix
> Direct contract, the optic-cable question — sits in the **Celcoin** threads,
> and you asked Danielle Paiva about optic cable providers on 29 September and
> again on 2 October. I have written this as Celcoin. If SoCoin is a genuinely
> separate party I could not find, say so and I will split the file.

---

## What this project actually is

We move from **Pix Indireto to Pix Direto**. Today we reach the SPI through
somebody else's institution — Dock/BPP for settlement, Celcoin and Matera for
technology. As a **direct participant** Unlimit connects to the SPI itself,
holds its own **Conta PI** at the Central Bank, and stops depending on another
institution's licence to reach the rail.

**This is the structural answer to the problem in `brazil.md` §2.** Genial
refuses conversion funds from BPP because BPP is not eFX-authorised. Direct
participation removes BPP from the chain entirely. The two projects should be
read together — this one has a long lead time, so the sooner it starts the
sooner §2 stops being a workaround.

In the Celcoin structure **Unlimit is the "Regulated Institution"** and Celcoin
supplies the technology. That is why the RSFN connection, the certificates and
the cable are *our* obligations, not theirs.

---

## 1 · The contract — and the deadlock nobody has named

**Where it stands.** Danielle Paiva put the renegotiated proposal on
29 September: keep the existing contract, change the scope from Pix Indireto to
**Pix Direto**, at **R$ 0.012 per transaction**. Core Banking and Payments
unchanged. Formalised through an **aditivo contratual** — an amendment, not a
new agreement.

Conditional on clearing the open balance, **R$ 478,333.33 in total**:

| Item | Amount |
|---|---|
| 1/3 of the setup fee | R$ 28,333.33 |
| September minimum (franquia mínima) | R$ 75,000.00 |
| Six months of minimum paid in advance, September included | R$ 375,000.00 |

**Final board terms, Thiago Ellero, 5 October** — *this message is unread*:

- **Pix Automático** — no adjustment possible, already at their minimum viable
  threshold.
- **DERE** (Declaração de Regimes Específicos) — **full waiver of the minimum
  fee, worth R$ 120,000 per year to us.** That is the real win in this round.
- **October minimum fee waiver** — refused, as September was.

### The deadlock

You asked for the draft contract twice — 29 September and 2 October — so Legal
could start. Celcoin's position is that the **aditivo is prepared after formal
acceptance**, not before. So:

- we will not accept without Legal seeing the text;
- they will not write the text until we accept.

Nobody has said this out loud, and it has cost a week. **The way out is to
split it**: accept the *commercial terms* in writing, explicitly subject to
Legal review of the aditivo, and ask them to start drafting on that basis. One
email, and it unblocks both sides.

Kirill is waiting too — you sent him the full Pix Direct proposal on
29 September and he replied *"жду самарри от юристов"*. That summary has not
come, because Legal has nothing to summarise.

### Carried over from the existing contract

Two clauses you objected to in August and that were never resolved: the
**24-month lock-in**, and **early termination requiring payment of the
remaining remuneration through to the end of the lock-in**. Thiago raised them
with Danielle; her Legal team never came back. **The aditivo is the moment to
reopen them** — once it is signed, they are locked for another two years.

*Open:* has the R$ 478,333.33 been paid or approved? Celcoin also chased
unpaid invoices separately on 28 September.

---

## 2 · The optic cable — what is actually required

Short version: **we do not lay cable.** We buy a Turn Key circuit and the
operator installs it at our address. The work is choosing operators, naming
the sites, and proving redundancy.

### How a direct participant connects

Direct participants reach the SPI through the **RSFN** — *Rede do Sistema
Financeiro Nacional*. Two ways in:

1. Contract circuits directly from the telecom operators that provide the
   network, or
2. Go through a **PSTI** — an IT service provider authorised by the Central
   Bank.

**The RSFN is operated by exactly two homologated carriers: RTM and
Embratel.** The standard posture is dedicated circuits with **both**, so that
no single carrier is a point of failure.

Installation is **Turn Key**: the carrier installs the local access at the
address you nominate — metallic pair, **fibre optic** or radio — and delivers a
configured router. So the question is not how to run fibre; it is which
addresses, which carrier profile, and whether the two paths are physically
diverse.

The BCB's guidance is explicit that participants should review their end-to-end
communication infrastructure for **redundant equipment and redundant paths**,
matching the redundancy level of the connection profile contracted, so there is
no single point of failure anywhere between the message origin and the
carriers' CPE. **That is where the cost and the lead time live** — two diverse
physical routes into a data centre is a construction question, not a purchase.

### What Celcoin has already told us

Danielle Paiva, 29 September — **and this answers half the question you asked
again on 2 October**:

> *"Regarding the communication link, **our native integration is through
> RTM**."*

So RTM is the default path on their side. What is still unanswered is the part
you asked on 2 October: contacts at the providers, and what exactly we must
procure. Worth asking Celcoin precisely:

1. Does your native RTM integration mean we contract RTM ourselves, or do we
   ride your circuit?
2. Do we also need Embratel for redundancy, or does the RSFN profile you use
   already cover it?
3. Which address do the circuits terminate at — our premises, our data centre,
   or yours?
4. Who at RTM should we talk to, and what is the lead time for a Turn Key
   install?
5. Are you acting as our PSTI, or are we contracting the carriers directly?

### Everything else a direct participant needs

| Requirement | Note |
|---|---|
| Formal application to the BCB | Per the *Roteiro para Participação Direta no SPI e abertura de Conta PI* — current version 1.11, September 2026 |
| **Conta PI** at the Central Bank | The settlement account; opened as part of the same process |
| RSFN connection | As above — RTM and/or Embratel, or via a PSTI |
| **CERTPIA** | Digital certificate for signature |
| **CERTPIC** | Digital certificate for channel encryption |
| Notify **Deinf/CSTI** | Required alongside the connection |
| Homologation testing | Before going live |

Reference documents, all published by the BCB:

- *Roteiro para Participação Direta no SPI e abertura de Conta PI*, v1.11
- *Manual de Redes do SFN*, v9.3
- *DRT-Redes* — Documento de Requisitos Técnicos (Redes), v9.4

**Get these to the infrastructure team before talking to carriers.** The
Manual de Redes is the document that decides what we have to buy.

---

## 3 · What we already have working for us

Not starting from zero:

- **Unlimit IP is already a Pix participant** — the BCB sends us monthly IGA
  availability reports (*Índice Geral de ANS*), so we are inside the Pix
  arranjo and known to pix-operacional@bcb.gov.br.
- **A CCME account process is already running with Bacen**, with a deadline of
  **8 December** to complete testing and prove technological and operational
  capacity. Whether that testing overlaps with the SPI homologation is worth
  checking — if it does, we should not run two test programmes.
- **Regulatório+** comes free with the Celcoin relationship, saving the
  MK Consulting fee.
- Celcoin already holds our Core Banking and Payments contracts, so this is an
  amendment to a live relationship rather than a new vendor.

---

## Contacts

| Who | Role | Detail |
|---|---|---|
| **Danielle Yamasaki Paiva** | Celcoin — Comercial, owns the proposal | danielle.paiva@celcoin.com.br · +55 11 95169-4021 |
| **Thiago de Lucca Ellero** | Celcoin — Head Comercial, Core Banking | thiago.ellero@celcoin.com.br · +55 11 94556-9409 |
| Celcoin Jurídico | Contract drafting | juridico@celcoin.com.br |
| Thiago Genda | Unlimit — Legal & Compliance Brazil | t.genda@unlimit.com |
| Rose Del Col | Unlimit — Brazil | r.delcol@unlimit.com · +55 11 98132-5254 |
| Anastasiya Piatrkouskaya | Unlimit — Head of APMs, owns the integration | a.piatrkouskaya@unlimit.com |
| Kirill | Awaiting the legal summary | k@unlimit.com |
| BCB Pix operations | Sends our IGA reports | pix-operacional@bcb.gov.br |

---

## Next steps

1. **Break the contract deadlock.** Accept the commercial terms in writing,
   expressly subject to Legal review of the aditivo, and ask them to draft on
   that basis. Flag the 24-month lock-in and the early-termination clause in
   the same email, because the aditivo is the last chance to move them.
2. **Read the 5 October message** — the DERE waiver is worth R$ 120k a year and
   has not been acknowledged.
3. **Send the five cable questions to Celcoin** in one email, rather than
   asking again in general terms.
4. **Pull the three BCB documents** and hand them to whoever owns infrastructure.
5. **Check the 8 December CCME deadline** against the SPI homologation plan.
6. **Tell the Brazil project** — direct participation is the real fix for
   `projects/brazil.md` §2, and the timeline for this should be visible there.

---

## Open questions

| # | Question | Who answers |
|---|---|---|
| 1 | Has the R$ 478,333.33 been approved or paid? | Finance / Konstantinos |
| 2 | Do we contract RTM ourselves or ride Celcoin's circuit? | Danielle / Celcoin |
| 3 | Is Embratel needed for redundancy, or does the profile cover it? | RTM / Celcoin |
| 4 | Which physical address terminates the circuits? | Infrastructure |
| 5 | Is Celcoin acting as our PSTI? | Celcoin |
| 6 | What is the Turn Key lead time, and what does the install cost? | RTM |
| 7 | Can the 24-month lock-in and termination clause be reopened in the aditivo? | Celcoin Jurídico |
| 8 | Does the 8 Dec CCME testing overlap with SPI homologation? | Thiago Genda |
| 9 | What is the full timeline from acceptance to live as a direct participant? | Celcoin + BCB roteiro |

---

## Method and limits

The regulatory and network requirements come from BCB publications read via
web search — this environment's egress proxy blocks bcb.gov.br directly, so
the *Roteiro*, the *Manual de Redes* and the *DRT-Redes* have **not** been read
in full here. Treat the requirement list as a map, not as the specification;
pull the PDFs before anyone signs or orders a circuit. The commercial terms and
the thread history come from the mailbox and are quoted directly.
