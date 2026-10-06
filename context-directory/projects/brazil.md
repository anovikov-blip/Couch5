# Projeto Brasil — Brazil project

Last updated: 2026-10-06
Owner: Andrey Novikov · Entity: Unlimit Brasil (authorised payment institution,
collecting for foreign merchants)

Status: `[!]` urgent · `[~]` soon · `[ ]` no deadline set · `[?]` blocked on an
answer

---

## Board

| # | Workstream | State | Deadline | Next step |
|---|---|---|---|---|
| 1 | Serpro — valid CPFs declined on verification | `[!]` live incident | now | Check which API version we call |
| 2 | Banco Genial — DOC transactions no longer accepted | `[!]` | — | Establish what "DOC" is in our stack |
| 3 | Banco Genial — crypto merchants dropped · own VASP licence or umbrella | `[!]` | **30 Oct 2026** | Legal opinion on the PSAV perimeter · Ebury thread already open |
| 4 | Additional FX banks | `[~]` | — | Approach Braza, Ebury, Ouribank, BS2 |
| 5 | eFX — declare the service in Unicad | `[!]` | **30 Oct 2026** | Sign the pending DocuSign on the FX Market licence |

**Two hard deadlines land on the same day — 30 October 2026, 24 days out.** The
PSAV authorisation filing and the Unicad eFX declaration are unrelated rules
that happen to share a date. Both are filings, not projects; both are missed by
forgetting rather than by failing.

---

## 1 · Serpro — valid CPFs coming back declined

**What we see:** we send CPFs that are valid, and verification returns a
decline.

**Most likely cause, and it is testable today.** Serpro replaced the CPF
lookup. The timing fits what we are seeing almost exactly:

- **Consulta CPF v3** went live **29 May 2026**, under *Portaria RFB nº 667 de
  24 de março de 2026*.
- The query key changed. **Date of birth is now mandatory and part of the key.**
  A CPF number on its own is no longer a valid query — Serpro validates CPF
  *and* date of birth against the Receita Federal registry together, and
  returns nothing if the pair does not correspond.
- New endpoint: `https://gateway.apiserpro.serpro.gov.br/consulta-cpf-df/v3`
- The field `ano de óbito` was removed from the response.
- **The previous versions were switched off on 2 October 2026.**

Four days ago. If our declines started around then, this is the answer and not
a coincidence.

**Three failure modes to check, in this order:**

1. **Are we still calling v2?** If so the endpoint is dead and every lookup
   fails, which downstream will look like "every CPF is invalid". Check the
   base URL in the integration against the v3 endpoint above.
2. **If we have migrated — are we sending the right date of birth?** Under v3 a
   mismatched DOB returns no correspondence. A customer who mistypes their
   birth date, or whose DOB we never collected, now fails a check that used to
   pass on the CPF alone. This is a data-collection problem in our own form as
   much as an API problem.
3. **How do we treat `situação cadastral`?** The API returns a status code, and
   only **0 = Regular**. The others are **2** Suspensa, **3** Titular falecido,
   **4** Pendente de regularização, **5** Cancelada por multiplicidade,
   **8** Nula, **9** Cancelada de ofício. **Code 4 is extremely common among
   ordinary Brazilians** — it usually means an unfiled tax return, not a
   fraudulent or invalid CPF. If our rule is "anything other than 0 is a
   decline", we are rejecting a meaningful share of perfectly real customers.
   That one is worth checking even if v3 turns out not to be the cause.

> *Hypothesis, not a diagnosis.* I have not seen our integration or the actual
> error payloads. Pull a handful of declined CPFs with the raw Serpro response
> and the answer should be immediate.

**Contacts**

| Channel | Detail |
|---|---|
| Support — Central de Serviços | css.serpro@serpro.gov.br · 0800 728 2323 |
| Commercial | comercial@serpro.gov.br |
| API documentation | apicenter.estaleiro.serpro.gov.br/documentacao/consulta-cpf |
| v3 release notice | centraldeajuda.serpro.gov.br/duvidas/pt/avisos/avisoconsultacpfv3 |
| Help centre | centraldeajuda.serpro.gov.br |

Serpro is a state-owned company and the support line is a government service
desk — expect a ticket, not a relationship manager. Raise it through
`css.serpro@serpro.gov.br` with the contract number, sample CPFs and timestamps,
and in parallel ask `comercial@serpro.gov.br` who our account contact is.

**Next step:** before contacting Serpro at all, have the team confirm which
endpoint version the integration calls. If it is v2, this is ours to fix, not
theirs, and the ticket is unnecessary.

*Open:* who owns the Serpro integration on our side; what our contract covers;
whether we also use Datavalid (a separate product, separate contract).

---

## 2 · Banco Genial no longer accepts DOC transactions

**Before designing a replacement flow, settle what is actually being refused.**

**DOC no longer exists in Brazil.** Febraban member banks stopped issuing and
scheduling DOC on **15 January 2024 at 22:00 BRT**; the last scheduled
transfers were processed and the systems shut down on **29 February 2024**.
TEC was discontinued at the same time. Pix had made both redundant. That was
two and a half years ago and it was market-wide, not a Genial decision.

So "Genial no longer accepts DOC" in October 2026 means one of these, and they
lead to completely different fixes:

- **(a) It is our internal label.** A payin or payout method still named "DOC"
  in our stack that has actually been routing as TED since 2024, and Genial has
  now stopped accepting whatever it really is. Fix: rename and re-map.
- **(b) It is a different "DOC".** A document-based or manual instruction flow —
  settlement against documentation rather than an electronic order. Fix: ask
  Genial what replaces it.
- **(c) It is TED under an old name**, and Genial has changed its TED
  acceptance rules for third-party processors. Fix: this is the same
  conversation as the eFX account condition in §3 below.

**Next step:** get the actual decline from Genial in writing — the message, the
reason code, and the method name as Genial writes it. One screenshot settles
which of the three this is.

**Likely destination regardless:** Pix. It is what replaced DOC, it is
instant, and it is what the market uses for exactly this. If the flow is
merchant payouts, Pix with a chave is the default answer; if it is collection,
we already do Pix.

*Open:* what the flow is for — payin, payout, or settlement; which merchants it
affects; what the volume is.

---

## 3 · Banco Genial no longer accepts crypto merchants — own licence or umbrella

**Why it happened.** Not de-risking. **Resolução BCB 561/2026**, in force since
**1 October 2026**, bans virtual assets — stablecoins included — as a means of
settling eFX operations between the Brazilian provider and its counterpart
abroad. Genial withdrew the product because the regulation closed it. Any other
bank is under the same rule, so shopping the same flow around will not work.

**The decision in front of us is not yet "licence or umbrella".** It is a prior
question: **is Unlimit Brasil inside the PSAV perimeter at all?**

We do not intermediate, custody or exchange virtual assets. We collect payments
in reais for merchants who buy and sell crypto from the Brazilian public
(on-ramp / off-ramp). Whether that makes us a PSAV, or merely a payment
institution serving PSAVs, is a legal reading — and it decides everything
downstream. The questions are already drafted for Brazilian counsel:

> **Brazil VASP Rules — Questions for Legal**
> https://claude.ai/code/artifact/c7f12c06-e9e2-4bf3-a032-6b7a99af03dc

### The regime

Resoluções BCB 519, 520 and 521 of 2025, in force **2 February 2026**. The
authorised entity is an **SPSAV** (*Sociedade Prestadora de Serviços de Ativos
Virtuais*). Three modalities: **intermediação**, **custódia**, **corretagem**.
Monthly reporting to the BCB started 4 May 2026.

**Anyone already intermediating, custodying or exchanging crypto for third
parties on 2 February 2026 must file for authorisation by 30 October 2026** —
270 days from entry into force — or stop.

### Option A — our own SPSAV licence

Minimum capital, by modality:

| Modality | Minimum capital |
|---|---|
| Intermediação only | R$ 9.2m |
| Intermediação + custódia | R$ 13m+ |
| Full range, upper end | up to R$ 37.2m |

Plus AML/CFT controls, segregation of client assets, governance, audit and
monthly BCB reporting. This is a real balance-sheet and compliance commitment,
not a filing.

**And the deadline cuts against it.** 30 October is 24 days away. If we are
inside the perimeter and have not filed, a licence application cannot be
assembled from a standing start in 24 days — the realistic move is to file what
is required to stop the clock and build the substance afterwards, which is a
decision for legal, not for us.

### Option B — umbrella with an authorised player

Worth being precise about what is and is not available here. The regulation
contemplates an **SPSAV outsourcing to relevant third parties** — custodians,
liquidity providers, market makers, e-money issuers, payment account providers,
technology suppliers — while remaining responsible for compliance. It does
**not** contemplate an unauthorised firm operating under someone else's
licence. "Umbrella" in the loose sense is not a recognised structure.

What *is* available, and is the realistic version of Option B: **restructure so
that Unlimit never touches the virtual asset.** The licensed PSAV is the
counterparty to the end customer for the crypto leg; we are its payment
institution for the reais leg. That is a commercial and contractual
restructuring, and it may be what we are already doing — which loops back to
the perimeter question.

### Already in motion — do not start this from zero

**Messias Andrade at Ebury has an open thread with you titled "Unlimit & Ebury
(efx for crypto)"**, 29 September. That is the exact product Genial just
withdrew, offered by a licensed banco de câmbio, and the conversation is
already live. It is waiting on one thing: Messias asked for a copy of your
**Brazilian RNM** for ID validation. That is a personal identity document —
confirm the request is genuine with him directly and send it through a secure
channel, not as an email attachment.

This does not answer the licensing question, but it may answer the commercial
one. If Ebury will bank the crypto merchant flow under Res. 561, the urgency
shifts from "replace the product" to "get the perimeter opinion right".

### Recommendation

Do not pick between A and B yet. **Get the perimeter opinion first**, this
week, because:

- if we are outside the perimeter, both options are moot and the real task is
  finding a bank that will keep banking merchants who are themselves PSAVs;
- if we are inside it, the 30 October filing becomes the only thing that
  matters this month and the choice between A and B happens after.

**Next step:** put the drafted questions to Brazilian counsel with a deadline
of this week, flagging 30 October explicitly.

*Open:* how much revenue the crypto merchant book represents; whether those
merchants are themselves authorised or filing; who at Genial can tell us
whether the whole relationship is at risk or only this segment.

---

## 4 · Additional banks for FX

Research is done: **`context-directory/research/brazil-efx-banks.md`**

Shortlist, in order:

1. **Braza Bank** — largest exclusive FX bank in Brazil, USD 67.8bn in 2024,
   FXaaS over API, no competitor on the cap table.
   sales@brazabank.com.br · +55 41 3123-0100
2. **Ebury Bank** (ex-Bexs) — we already have Messias Andrade. Run the Brazil
   and the global rate-lock conversations as one.
   comercial@br.ebury.com · +55 11 4130-3800
3. **Ouribank** — sells to facilitadoras by name; Nomad is its FX
   correspondent. +55 11 4081-4444
4. **Banco BS2** — cambio@bancobs2.com.br
5. **Travelex Bank** — explicit eFX product for facilitadoras, batch settlement
   over API/SFTP. infocomercial@travelexbank.com.br
   *Being acquired by StoneX: announced 12 Aug 2026, CADE cleared, BCB pending.
   Same conversation as StoneX, not a separate one.*

**Ask first, before price:** does your eFX account accept funds from a
third-party payment processor, or only directly from end clients and
acquirers? Genial requires the latter, and if that is the market standard our
pooled model has a structural problem rather than a commercial one.

**Do not approach:** Banco Letsbank, Banco Master, Banco Pleno (Master group,
liquidated from Nov 2025), Advanced Corretora, Frente Corretora.
**Conflict:** EBANX holds 30% of Banco Topázio.

---

## 5 · eFX — Unicad declaration

Institutions already authorised by the BCB that provide eFX must declare that
service in their **Unicad** registration by **30 October 2026**.

Separately: under Res. BCB 561/2026 an authorised payment institution can
provide eFX **directly**, without a separate FX-market authorisation, if it is
authorised as an *emissor de moeda eletrônica*, an *emissor de instrumento de
pagamento pós-pago*, or a *credenciador*.

**Which modality is Unlimit Brasil authorised in?** If it is one of those
three, §4 becomes a question of pricing and reach rather than of permission.

**There is an unsigned DocuSign envelope on this.** *"Unlimit IP: assistance
obtaining the FX Market licence"* — Yulia checked it and asked you to sign on
30 September. It has been sitting since. If that engagement is the route to the
FX market authorisation, signing it is the unblock, and it has been waiting six
days.

*Open:* both answers sit with the Brazilian legal team. Ask alongside §3 — same
people, same week. Confirm whether the DocuSign engagement covers the Unicad
declaration too, or only the licence application.

---

## Open questions register

| # | Question | Who answers | Blocks |
|---|---|---|---|
| 1 | Which Serpro API version does our integration call? | Tech / integration owner | §1 |
| 2 | Do we collect date of birth, and does it reach Serpro? | Tech / product | §1 |
| 3 | Do we decline on any `situação cadastral` other than 0? | Tech / risk | §1 |
| 4 | What exactly is "DOC" in our stack, and what did Genial actually refuse? | Genial + our ops | §2 |
| 5 | Is Unlimit Brasil inside the PSAV perimeter? | Brazilian counsel | §3 |
| 6 | Which IP modality is Unlimit Brasil authorised in? | Brazilian legal | §5, §4 |
| 7 | Has the Unicad eFX declaration been filed? | Brazilian legal | §5 |
| 8 | What may we disclose pre-NDA to FX counterparties? | Internal | §4 |
| 9 | Is Genial's condition — funds direct from end clients and acquirers, not via a third-party processor — market standard? | The five banks in §4 | §4, §2 |
| 10 | Will Ebury bank the crypto merchant flow under Res. 561? | Messias Andrade | §3 |
| 11 | Does the pending DocuSign engagement cover the Unicad declaration, or only the licence? | Yulia / Brazilian legal | §5 |

---

## Parking lot

Items to be added. Andrey said he would add more.

---

## Sources

Serpro Consulta CPF v3 release notice and API documentation · Portaria RFB
nº 667 de 24.03.2026 · Febraban — banks to stop offering DOC, and the Jan/Feb
2024 shutdown dates · Resolução BCB 561/2026 (Machado Meyer, Lefosse,
Finsiders, ABBC) · Resoluções BCB 519/520/521 de 2025 (Mattos Filho, Machado
Meyer, Madrona, Lefosse, VBSO, NDM Advogados) · StoneX investor relations —
acquisition of Banco Travelex · Exame — Braza Bank ranking · NeoFeed — EBANX
stake in Banco Topázio.

Network note: this environment's egress proxy blocks bcb.gov.br, serpro.gov.br
and most Brazilian corporate domains, so nothing here was read from a primary
site directly. Dates and figures come from legal-firm commentary and press and
should be confirmed against the primary text before anything is filed.
