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
| 2 | Genial refuses conversion funds from Dock (BPP) — not eFX-licensed | `[!]` live, daily | — | Reply to Genial; ask Travelex for its written position |
| 3 | Banco Genial — crypto merchants dropped · own VASP licence or umbrella | `[!]` | **30 Oct 2026** | Legal opinion on the PSAV perimeter · Ebury thread already open |
| 4 | Additional FX banks | `[~]` | — | Approach Braza, Ebury, Ouribank, BS2 |
| 5 | eFX — declare the service in Unicad | `[!]` | **30 Oct 2026** | Pinheiro Neto engaged 5 Oct — confirm Unicad is in scope |
| 6 | Non-resident accounts (NRA/CNR) — five banks, all stalled | `[!]` | — | Press Ouribank for its Cayman structure; Genial opinion now sent |
| 7 | Genial concentration — ~80% of approved volume at a sanctioned bank | `[!]` | — | Put a current number on the exposure |

**Three of these are the same problem wearing different clothes.** Genial will
not take conversion funds from Dock/BPP because BPP is not eFX-authorised (§2).
Ouribank will not open a non-resident account because our entity does not hold
a licence covering third-party flows (§6). Genial requires eFX funds to arrive
directly from end clients and acquirers rather than through a third-party
processor (§3, §4). That is one rule being applied three times: **after
1 October, every institution in the chain has to hold the licence for what it
is doing, and pass-through structures no longer qualify.** Fixing each
symptom separately will not work — and §5, which modality Unlimit Brasil is
actually authorised in, is the question underneath all three.

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

## 2 · Genial will not accept conversion funds from Dock (BPP)

> **Correction.** My first pass read this as *DOC*, the Brazilian transfer
> instrument abolished in 2024, and worked out what might have replaced it.
> Wrong instrument. It is **Dock**, the banking-as-a-service provider, and
> **BPP**, the payment institution Dock acquired in 2021 — bank code 301, a
> direct participant in the SPI. Everything below replaces that section.

### The flow, as it runs today

1. We collect by **Pix as an indirect participant**, through **Dock / BPP**.
2. Collections land in the **BPP Pix account (72467-9)**.
3. Ellen Inhauser requests a transfer out of that account to a conversion
   bank; you approve each one by email; Dock's treasury executes.
4. The conversion bank converts and the funds leave Brazil.

This is live, daily, and material: individual transfers run **R$ 300k to
R$ 1.2m**, several a day. R$ 1,230,839.45 on 5 October. R$ 737,864.17 on
1 October. R$ 782,515.54 plus R$ 377,597.96 on 29 September.

### What broke

Res. BCB 561/2026 came into force on **1 October 2026**. Ellen put the
question to Dock on 29 September: from 1 October we can only process
conversion transactions through a company licensed for eFX and regulated by
the Central Bank.

**Dock answered on 5 October, in writing, through Alan Paiva:**

> *"Please be advised that Dock is not authorized to conduct eFX activities."*

Genial's position follows from that, as Dimitris reported the same day:

> *"Genial inform us that can not accept funds from BPP for any conversion
> because claim that BPP has not the relevant license."*

So this is not Genial being difficult. Our Pix collection rail sits behind an
institution that is not authorised for eFX, and from 1 October that breaks the
chain at the conversion step.

**It was called five months early and nobody acted.** Thiago circulated the
Res. 561 summary on **11 May 2026**, flagging that all eFX funds must be
received by Unlimit in an account at an authorised bank. Ellen, 28 September:
*"That email went unanswered, and there is a crucial point in item 6:
'Exclusive bank account…'"*

### What we have actually done about it so far — and why it is a risk, not a fix

Look at where the money went either side of 1 October. Before: Banco Genial
(125), ag. 0001, c/c 4599755-1. After: **Travelex Banco de Câmbio (095),
ag. 0001, c/c 3600-0** — the 1 October and 5 October transfers both went
there.

In other words, the volume moved to the bank that has not objected yet.
**Genial and Travelex are reading the same rule differently, and we have
concentrated the flow on the more permissive reading.** Nobody has written
that down as a decision. If Travelex adopts Genial's position — and Travelex
is a *banco de câmbio*, so it has more reason to be strict, not less — the
Pix collection-to-conversion chain stops with no fallback.

**This is the thing to get ahead of.** Ask Travelex directly, in writing,
whether they accept conversion funds routed from BPP post-561. A "no" we
provoke on our own timetable is survivable. A "no" that arrives on a Tuesday
morning with R$ 1.2m in flight is not.

### The alternative on the table — CIP via BTG

Dimitris proposed on 5 October: convert the **CIP traffic we already receive
into our BTG account**, which holds the proper licence, rather than routing
through BPP. Thiago's legal read, 6 October:

> *"I am not aware of any legal restrictions regarding the use of our BTG
> account for operations other than settlements received via CIP, so this
> appears to be a viable alternative. **However, please keep in mind that the
> funds from Pix transactions must be received directly into the BTG account
> if that is the plan. They cannot pass through the Dock account first before
> going to BTG. If Genial notices this flow, they will likely continue to
> refuse the transactions.**"*

That caveat is the whole problem. The CIP route works **for CIP traffic**. It
does not rescue the Pix traffic unless Pix collections land **directly** in
BTG — which means replacing or bypassing Dock/BPP as the Pix rail, not just
re-pointing the outbound transfer. Washing the same funds through BTG on the
way out is not a fix; it is the same flow with an extra hop, and Thiago says
so plainly.

**Both of these emails are unread in your inbox**, and Dimitris marked his
High Importance and asked Thiago to clear it legally *before anyone replies to
Genial in the WhatsApp group*. Thiago has now answered. The reply to Genial is
the next move and it is waiting on you.

### There may already be a compliant route at Genial, half-built

A separate Genial thread — *"Aditional Account — Unlimit Brasil"*, with
**Gabriel Labate**, Corporate Desk — has been running since August. Thiago
opened it on 10 August:

> *"Due to a Central Bank request, we must start sending our FX transactions
> not related to credit card operations directly through an **Unlimit Brasil
> IP account** instead of Unlimit Brasil PSP."*

Gabriel confirmed the account supports the expected flow, with compliance
approval needed only if the merchants were new — they are the same merchants,
only the sending entity changes. On **30 September** Ellen reported the account
was open but needed the **Unlimit Brasil / Unlimit Uruguai contract** to
activate; she sent it the same day and Gabriel replied that he is proceeding.

This matters because it is the same shape as the fix: **send from the licensed
IP entity rather than from a structure the bank will not accept.** Before
designing anything new, find out where that account now stands and whether it
solves the BPP problem or is unrelated to it. Gabriel Labate —
gabriel.labate@genial.com.vc, +55 11 3206-8000. Bernardo Duarte is on the
thread too.

### Options, in the order I would test them

1. **Become a direct Pix participant, or move to a rail whose institution is
   eFX-authorised.** The real fix. Long lead time, so start the clock now.
2. **Pix collections directly into BTG.** Thiago's condition — no Dock hop.
   Needs Dock and BTG to say whether it is operationally possible at all.
3. **Convert at an institution that accepts the BPP chain.** This is what we
   are doing by default with Travelex. Fine as a bridge; dangerous as a plan,
   and only if Travelex confirms it in writing.
4. **Ask Dock whether it intends to seek eFX authorisation.** They have until
   31 May 2027 to file. If they are going to, that changes the calculus; if
   they are not, our Pix rail has a permanent ceiling and we should know now.

### Contacts

| Who | Role | Detail |
|---|---|---|
| **Alan Paiva** | Dock — gave the written "not authorized for eFX" | alan.silva@dock.tech |
| Elton Rezende | Dock — executes the daily transfers | elton.rezende@dock.tech |
| David Alves | Dock — treasury | david.alves@dock.tech |
| William Monte | Dock | william.monte@dock.tech |
| Bruno Fernandes | Dock — indirect-Pix contract billing | bruno.fernandes@dock.tech |
| **Ellen Inhauser** | Unlimit — Settlements Officer, runs this daily | e.inhauser@unlimit.com · +55 11 98244-3389 |
| Dimitris Dimitriou | Unlimit — proposed the CIP route | d.dimitriou@unlimit.com |
| Thiago Genda | Unlimit — Legal & Compliance Brazil | t.genda@unlimit.com |
| **Gabriel Labate** | Banco Genial — Corporate Desk | gabriel.labate@genial.com.vc · +55 11 3206-8000 |
| Bernardo Duarte | Banco Genial | bernardo.duarte@genial.com.vc |

*Open:* what share of Brazilian collection volume runs through BPP versus CIP;
whether Dock intends to apply for eFX authorisation; whether Pix can land
directly in BTG at all; and what Travelex's written position is.

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

### The authorisation is two stages, and stage one is smaller than it looks

I previously wrote that a licence application cannot be assembled from a
standing start in 24 days. That was too pessimistic, and the correction
matters.

**Stage 1, due 30 October 2026:** file the request with corporate documents,
audited financial statements and a formal communication to the BCB. That is a
filing, and it is achievable.

**Stage 2, after the BCB responds favourably:** business plan, proof of
economic-financial capacity, approval of administrators. That is the real
work, and it happens on the regulator's clock, not ours.

So if we are inside the perimeter, the 30 October date is a document exercise,
not a project. Missing it is a choice, not a resource problem.

Prudential regime: **Resolução BCB 580/2026** brought SPSAVs inside the
Central Bank's prudential framework as Type 3 institutions, held in Segment S4
until 30 June 2028 regardless of size.

### Option A — our own SPSAV licence

Minimum capital, by modality:

| Modality | Minimum capital |
|---|---|
| Intermediação only | R$ 9.2m |
| Intermediação + custódia | R$ 13m+ |
| Full range, upper end | up to R$ 37.2m |

Plus AML/CFT controls, segregation of client assets, governance, audit and
monthly BCB reporting — the latter already running since 4 May 2026 for
authorised firms.

### Option B — PSAV as a service

**Correction to what I wrote before.** I said the regulation does not
contemplate an unauthorised firm operating under someone else's licence. That
was too absolute. **"PSAV as a service" is a real and established model in
Brazil**, and there is a market of providers around it.

How it works: **an authorised SPSAV may contract third parties while remaining
fully responsible for regulatory compliance.** That creates a legitimate route
for a company to offer virtual-asset services by connecting to an authorised
partner's licence rather than holding its own. Commercially this is the
umbrella you were describing.

**But there is a test that decides whether it actually works for us, and it is
written into Res. BCB 520.** The regulation applies a **functional test**: what
matters is not the label — not "SaaS", not "non-custodial", not
"infrastructure" — but whether the provider

- holds or controls the instruments needed to move the assets,
- executes transfer instructions on the holder's behalf, or
- can block or restore access.

If any of those is true of us, we are the PSAV regardless of what the contract
with the umbrella provider says. The label does not survive contact with the
test. **So Option B is viable exactly to the extent that our flow fails all
three limbs** — which, since we handle the reais leg and not the asset, it
plausibly does. That is the thing to put to counsel, in those words.

**One more rule that may bite, and it shares the same date.** Foreign entities
that were active in Brazil on 2 February 2026 must **transfer their operations
and clients to a licensed bank, broker or SPSAV within 270 days — by
30 October 2026 — and then cease their own activity.** If any Unlimit entity
outside Brazil touches this flow, that provision needs checking against it.

### Recommendation

Do not pick between A and B yet. **Get the perimeter opinion first**, this
week, because:

- if we are outside the perimeter, both options are moot and the real task is
  finding a bank that will keep banking merchants who are themselves PSAVs;
- if we are inside it, the 30 October filing becomes the only thing that
  matters this month and the choice between A and B happens after.

**Next step: put the drafted questions to Pinheiro Neto.** You engaged them on
5 October for the FX market licence (§5) — they are already retained, they are
a top-tier Brazilian firm, and this is squarely their work. Do not go hunting
for separate counsel, and do not keep looking for a Mexican firm for the Genial
legal opinion before PN has given a view. Flag 30 October explicitly, and ask
the Res. 520 functional test in the three limbs above. (The separate Mexican
opinion for the Genial NRA is done — Jorge Luna at KNP delivered it and it has
been sent to Genial. That was the §6 question, not this one.)

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

**This is already in motion — counsel is engaged.** The DocuSign
*"Unlimit IP — Assistance in obtaining License to Operate in the FX Market"*
**completed on 5 October 2026**, all parties signed. The firm is **Pinheiro
Neto Advogados**; the contact is **Maria Luiza Kjekshus Mansur Haddad**
(mhaddad@pn.com.br, Rua Hungria 1100, São Paulo, +55 11 3247-8400). Yulia
Shevchenko wrote on 2 October that she wants to start work on the licence the
following week — so that work is starting now.

**Use them for §3 as well.** The PSAV perimeter question, the Res. 520
functional test and the modality question below are all the same kind of
question, for the same firm, already retained, in the same week.

*Open:* confirm with Yulia and PN whether the engagement covers the **Unicad
eFX declaration due 30 October**, or only the licence application. Those are
different filings and it would be easy for the smaller one to fall between
them.

---

## 6 · Non-resident accounts (NRA / CNR)

**Why this is not a side task.** Under Res. BCB 561/2026 the financial
settlement between a Brazilian eFX provider and its counterpart abroad must go
through **either an FX operation or a non-resident real account held in
Brazil** — those are the only two permitted routes. The NRA is therefore not
just convenient treasury plumbing; it is one half of the legal settlement
mechanism for everything in §4 and §5.

Regulatory basis: Res. BCB 277/2022 (updated version in force 2 Feb 2026),
which lets any institution authorised in the FX market open and maintain real
accounts for non-residents on the same terms as for residents. **Res. BCB 575
of 18 June 2026** amended 277 on foreign-currency accounts and widened the list
of who may hold one — worth a read before we fix the structure, because it may
open an option we have not considered.

### The blocker, stated plainly by one of them

On **21 August 2026** Deborah Marreiros at Ouribank declined our onboarding:

> *"Unfortunately, we are unable to proceed with the onboarding based on the
> license currently provided. In order to open an NRA in Brazil, the entity
> must hold a license that allows it to process third-party flows."*

That is almost certainly the same wall the other four are standing behind,
whether or not they have said it as directly. We have been treating five
separate onboardings as five separate paperwork exercises. **It is one problem:
the entity we are presenting does not hold a licence covering third-party
flows.** Chasing documents will not fix it. Choosing the right entity, or the
right structure, will.

### And Ouribank already proposed a structure — seven weeks ago

In the same message:

> *"Ouribank recently opened a branch in the Cayman Islands, and this branch
> has an NRA in Brazil. This structure would allow us to support third-party
> flows, including flows such as those currently handled through Unlimit… We
> will send you next week the expected flow that we believe could work for
> you!"*

**That was 21 August. The flow document never arrived, and the message is
still unread in your inbox.** This is the single highest-value follow-up on the
page: a bank that understands the problem, has a structure that solves it, and
offered to write it up.

### Status by counterparty

As of Thiago's last written update, 4 August 2026, plus what has happened since.

| Bank | Entity | State | What is actually blocking it |
|---|---|---|---|
| **Ouribank** | UNL BR / group | **Declined 21 Aug** on licence grounds; Cayman-branch alternative offered | Their flow document never came. Also: merchant details Ouribank asked for were never sent — Thiago followed up with Dimitris and Ellen twice |
| **Travelex** | UNL MX | Stalled since 16 June | PoA proving Andrey can represent UNL MX **in Brazil**, apostilled and sworn-translated. Lorena Chávez confirmed no such PoA exists expressly. Compliance call promised "next week at the latest" on 16 June — nothing since. A new relationship manager took over in early August and promised a final position on the UNL MX NRA "by the end of the week" |
| **Genial** | UNL MX | **Legal opinion sent — ball with Genial** | Resolved. **Jorge Luna at KNP** (jluna@knp.com.mx) produced it; Enrique Pina sent proof of payment 29 Sep and Jorge confirmed receipt. The opinion has now gone to Genial. Next step is chasing Genial's review, not finding a lawyer |
| **Ebury** | — | Proposal with you since 30 July | Documentation and commercial proposal were sent to you for review. **Still with you, ten weeks** |
| **Braza** | — | Call on the proposed flow | You and Dimitris are in a WhatsApp group with them |

### Two questions from Thiago that were never answered

Both of his update emails — 30 July and 4 August — are **still unread**. Each
asks you something directly:

1. **Genial:** *"Do we still want to move forward with them?"* — the lawyer
   question is now moot, KNP delivered and the opinion is with Genial. But the
   first half still stands, and §7 below makes it sharper, not easier.
2. **Ouribank:** *"Please help me follow up with Dimitris and Ellen"* on the
   merchant details Ouribank requested. **Still open.**

### Contacts

| Bank | Person | Detail |
|---|---|---|
| Ouribank | **Deborah Marreiros**, Hub — eFX | deborah.marreiros@ouribank.com · +55 11 97767-0003 · Av. Paulista 1728, Sobreloja |
| Ouribank | Lucas Santos · team inbox | lucas.santos@ouribank.com · efx@ouribank.com |
| Ouribank | Erica Rodrigues França (onboarding) | erica.franca@ouribank.com |
| Travelex Bank | **Leandro Reis**, Sr Sales Manager | lersousa@travelexbank.com.br · +55 11 3728-8449 · *note: a new relationship manager took over in Aug — confirm who owns it now* |
| Banco Rendimento | **Andreia Cunha** | andreia.cunha@rendimento.com.br — inbound partnership approach, 24 Sep, unanswered |
| Internal | Thiago Genda, Legal & Compliance Officer Brazil | t.genda@unlimit.com — owns all five |
| Internal | Dolores (Lorena) Chávez | d.chavez@unlimit.com — Mexican corporate documents |
| **KNP (Mexico)** | **Jorge Luna** — wrote the legal opinion for Genial | jluna@knp.com.mx |
| Internal | Olegs Bulgakovs, Head of Treasury | o.bulgakovs@unlimit.com · +357 25388614 — has his own "NRA accounts in Brazil" thread |
| Internal | Enrique Pina | e.pina@unlimit.com — handled the KNP payment |

### Next steps, in order

1. **Reply to Deborah at Ouribank** asking for the Cayman-branch flow document
   she offered on 21 August. One email, highest return on the page.
2. **Answer Thiago's two questions**, which have been open since 30 July.
3. **Decide which entity applies for the NRA** — the licence-for-third-party-
   flows requirement is the root cause, and it is a structuring decision, not a
   paperwork exercise. Tie it to the §5 question about Unlimit Brasil's own
   authorised modality.
4. **Review the Ebury proposal** that has been with you since 30 July.
5. **Read Res. BCB 575/2026** on foreign-currency accounts before fixing the
   structure.

*Open:* which legal entity should hold each NRA, and for what purpose —
UNL MX, Unlimit Brasil, the UAE entity, or a holding company; and whether one
NRA serves everything or we need several.

---
## 7 · Genial — concentration and counterparty risk

This is not a workstream anyone opened. It is the thing that sits underneath
§2, §3 and §6, and it should be looked at directly before any more energy goes
into repairing the Genial relationship.

### What the regulator did to them

On **12 August 2026** the Banco Central fined Banco Genial **R$ 21.6m across
three penalties**, and its Administrative Sanctioning Committee (Copas)
**disqualified André Schwartz, the CEO, for four years**, with a personal fine
of R$ 516k. The bank said it would appeal and seek suspensive effect.

The grounds matter more than the numbers. The findings were:

- failures in **certifying the qualification of foreign exchange clients**
- problems **reporting suspicious operations to Coaf**
- deficiencies in **AML policies, procedures and internal controls**

Mostly covering operations from 2020–2021.

### Why that explains everything else on this page

A bank fined for failing to verify that its FX clients were qualified to do
what they were doing is going to be unusually strict about exactly that, for
years. That is why Genial refuses funds from BPP (§2), why it requires funds
to arrive directly from end clients and acquirers rather than a third-party
processor (§3), and why it wants a Mexican legal opinion before opening a
non-resident account (§6).

**Genial is not being difficult. Genial is under sanction and is doing
precisely what the Central Bank fined it for not doing.** That reframes the
negotiation: there is no commercial argument that will move them, and no
relationship manager who can make an exception. Every ask has to be answered
with a document.

### The concentration number

Dimitris, 31 August: *"currently in Genial we have almost **80% of our total
approved** [volume]."*

So roughly four fifths of approved Brazilian volume sits with a bank that has
just been fined R$ 21.6m, whose CEO has been disqualified for four years and is
appealing, and which has already withdrawn two product lines from us this
quarter. Ellen said on the same thread that she would resume the Ebury account
opening to find a replacement.

**This is the single largest unmanaged risk in the Brazil book**, and it is not
on anyone's list as a risk — only as a series of individual problems.

### What was already decided, and should be checked

Thiago, 1 September: *"we should start evaluating other banks for the FX
transfers to prevent future issues. **Kirill approved moving forward with our
own FX license**, but this is a long process…"*

So the own-licence route (§5) is not a lawyer's suggestion — **it carries CEO
approval from early September**, and the Pinheiro Neto engagement signed on
5 October is the execution of that decision. Worth saying out loud, because it
means §5 is funded and mandated, and the only open question is scope and speed.

### Next steps

1. **Put a number on it.** What share of Brazilian volume, and which merchants,
   depend on Genial today — Dimitris's 80% is five weeks old and two product
   lines have gone since.
2. **Treat §4 as risk reduction, not procurement.** Braza, Ebury, Ouribank and
   BS2 are a second rail, not a price exercise.
3. **Watch the appeal.** If the suspensive effect fails and Schwartz has to
   step down, Genial's posture may change again — in either direction.

*Open:* whether Genial's sanction affects its ability to hold our funds at all,
as opposed to its appetite; and whether our own compliance team has assessed
the bank since August.

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
| 12 | Which entity holds a licence permitting third-party flows, and should it be the NRA holder? | Brazilian legal + group legal | §6 |
| 13 | What does Ouribank's Cayman-branch NRA structure actually look like? | Deborah Marreiros | §6 |
| 14 | Do we still pursue Genial for the NRA, and keep hunting a Mexican law firm? | Andrey | §6 |
| 15 | Does Res. BCB 575/2026 open a foreign-currency account option we have not considered? | Brazilian legal | §6 |
| 16 | Will Travelex accept conversion funds routed from BPP after 1 Oct? Get it in writing | Leandro Reis / new RM | §2 |
| 17 | Does Dock intend to apply for eFX authorisation before 31 May 2027? | Alan Paiva, Dock | §2 |
| 18 | Can Pix collections land directly in BTG, bypassing Dock? | Dock + BTG + product | §2 |
| 19 | What share of Brazilian volume is BPP/Pix versus CIP? | Ellen / Finance | §2 |
| 20 | Where does the Genial "Unlimit Brasil IP" account stand, and does it solve the BPP problem? | Gabriel Labate / Thiago | §2 |
| 21 | Does our flow fail all three limbs of the Res. 520 functional test — control of instruments, executing transfers, blocking access? | Pinheiro Neto | §3 |
| 22 | Does the 270-day foreign-entity transfer rule (by 30 Oct) catch any Unlimit entity outside Brazil? | Pinheiro Neto | §3 |
| 23 | Does the Pinheiro Neto engagement cover the Unicad eFX declaration, or only the licence? | Yulia / PN | §5 |
| 24 | What share of Brazilian volume, and which merchants, depend on Genial today? | Dimitris / Ellen | §7 |
| 25 | Has our compliance team assessed Genial since the 12 Aug sanction? | Anna Jacobson | §7 |
| 26 | Does Genial's sanction affect its ability to hold our funds, or only its appetite? | Pinheiro Neto / compliance | §7 |

---

## Parking lot

**Noticed while researching §6, not yet written up — say the word and I will.**
There is a live BACEN supervisory workstream on UNL.BR running since July:
report resubmissions (4010 monthly, 4111 daily, 4016 semi-annual), the CCS
submission, Patrimônio de Referência, the safeguarding calculation, audited
semi-annual financial statements, and a formal SISCOM process. Owners: Egor
Trusov, Konstantinos Charalampous, Thiago Genda, Anna Jacobson. It is the
largest Brazil workstream by message volume and it is not on this page.

Other items to be added. Andrey said he would add more.

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
