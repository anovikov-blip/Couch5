# FX broker outreach — templates for the firms with no email address

Last updated: 2026-09-30

Fourteen enquiries are already sitting as drafts in Gmail (see
`reminders.md`). This file covers the rest: the counterparties on the list
that publish a contact form or a phone number but no mailbox, plus the two
that should be reached through an internal route rather than cold.

Paste the text below into their form, or into an email once a named address
comes back. Nothing here has been sent.

---

## Before sending anything

Every counterparty will come back asking for volumes by currency. That
question is still open internally — the same one outstanding on the Klarna
thread. Decide what may go out pre-NDA before the first reply lands, not
after.

---

## Not a cold approach — use the internal route

| Firm | Why | Route |
|---|---|---|
| Convera | Already an Unlimit partner | Go through the existing relationship owner, not the sales form |
| StoneX Payments | `a.koh@unlimit.com` is on their marketing list | Ask Andrew Koh who the contact is |
| Hecto Financial (Korea) | Live candidate, corporate FX approval Mar 2026 | Ask Payletter (DH Lee) or KG Inicis for an introduction — draft already prepared |
| Coins.ph (Philippines) | Bea Alcanar already sends us proofs of payment | Reply on the existing settlement thread |
| Currenxie (Hong Kong) | We are in Hong Kong | Walk in or call — faster than the form |

---

## English — for the global and Asia-Pacific forms

> Use for: Ballinger & Co · Lumon (lumonpay.com) · Wise Platform · CrossFX ·
> MoneyMatch · Merchantrade Asia · XFlow · Skydo · Thomas Cook India ·
> Centrum · EbixCash · Deemoney · Verto · Conduit · Onafriq

```
Unlimit is a global payment provider. We collect local-currency payments for
merchants across Latin America, Asia-Pacific, Europe and Africa, and we are
looking for a licensed non-bank FX counterparty to sit behind that flow.

How it works on our side:

1. We connect over API and send each collected transaction as it happens.
2. You return an exchange rate for that individual transaction, and hold it.
3. At the end of the day, or T+1, we send the day's aggregated local-currency
   balance and you pay us USD.
4. That USD goes to our UAE entity, from which we settle merchants once all
   markets have landed.

Ticket size on the settlement leg is USD 100,000 as a minimum and usually
considerably larger. The per-transaction rate lock sits on the collection
side, where individual amounts are small.

Five questions that decide this for us:

1. Is the rate you quote per transaction binding on you at settlement, or
   indicative?
2. How long can a rate be held? It has to survive from the moment of
   collection through to end-of-day or T+1 settlement.
3. How many locks per day can we run, and is there a cap on aggregate open
   exposure?
4. Is there a cap on the size of a single outward transfer, or on aggregate
   daily volume?
5. Under which purpose code would you book a pooled transfer of collected
   merchant funds to our UAE entity, and does your licence cover it?

We are happy to sign an NDA before going into volumes by currency.

Andrey Novikov
Head of Expansion, Unlimit
+52 55 7013 3122 · a.novikov@unlimit.com
```

**Add for Malaysia (MoneyMatch, Merchantrade):** the RM 200,000 per-day
aggregate ceiling under the MSB licence is the point to press — ask whether a
higher corporate limit exists, and under which class.

**Add for India (XFlow, Skydo, Thomas Cook, Centrum, EbixCash):** be explicit
that the per-unit ceiling written into the PA-CB rules applies to individual
consumer payments, not to our settlement leg. Ask instead about the ceiling on
the outward transfer, and whether an AD Category-II route lifts it.

**Add for Wise Platform:** the hold window is short by design. Ask directly
whether a rate can be held from collection through to end-of-day settlement,
because if it cannot, the rest does not matter.

---

## Portuguese — for the Brazilian corretoras

> Use for: B&T Câmbio · Treviso · Levycam · Ourinvest

```
A Unlimit é um provedor global de serviços de pagamento. Recebemos pagamentos
em moeda local para nossos lojistas em diversos mercados e procuramos uma
corretora de câmbio que possa converter essas cobranças em dólares.

Como funciona do nosso lado:

1. Conectamo-nos por API e enviamos cada transação recebida no momento em que
   ocorre.
2. Vocês retornam uma taxa de câmbio para aquela transação específica e a
   mantêm.
3. No fim do dia, ou em D+1, enviamos o saldo agregado em reais e vocês nos
   pagam em dólares.
4. Esses dólares vão para nossa entidade nos Emirados Árabes Unidos, de onde
   liquidamos os lojistas depois que os recursos de todos os mercados chegam.

O valor da operação de liquidação é de no mínimo USD 100.000, normalmente
bem maior. O travamento de taxa por transação fica do lado da cobrança, onde
os valores individuais são pequenos.

Cinco perguntas que definem isso para nós:

1. A taxa cotada por transação é vinculante para vocês na liquidação, ou é
   referencial?
2. Por quanto tempo uma taxa pode ser mantida? Ela precisa sobreviver do
   momento da cobrança até a liquidação no fim do dia ou em D+1.
3. Quantos travamentos diários podemos fazer, e há limite de exposição aberta
   agregada?
4. Como vocês tratam o teto por operação de câmbio aplicável às corretoras?
   Nosso volume diário por mercado excede esse valor.
5. Sob qual código de natureza da operação seria registrada uma transferência
   agrupada de recursos cobrados de lojistas para nossa entidade nos Emirados,
   e a licença de vocês permite isso?

Estamos à disposição para assinar um acordo de confidencialidade antes de
entrar em volumes.

Andrey Novikov
Head of Expansion, Unlimit
+52 55 7013 3122 · a.novikov@unlimit.com
```

**Note:** the per-operation ceiling for corretoras stands at USD 500,000
(Res. BCB 401/2024, held at that level by Res. BCB 521/2025). Question 4 is
about what happens when a day's collections exceed it — splitting across
operations, or routing through a bank.

---

## Spanish — for the Andean and Chilean forms

> Use for: Giros y Finanzas (Colombia) · Global66 (Colombia, Chile) ·
> Rextie (Peru) · Kambista (Peru)

```
Unlimit es un proveedor global de servicios de pago. Recaudamos pagos en
moneda local para nuestros comercios en varios mercados y buscamos una
contraparte con licencia que pueda convertir esas recaudaciones a dólares.

Cómo funciona de nuestro lado:

1. Nos conectamos por API y les enviamos cada transacción recaudada en el
   momento en que ocurre.
2. Ustedes nos devuelven un tipo de cambio para esa transacción individual, y
   lo mantienen.
3. Al cierre del día, o T+1, les enviamos el saldo agregado en moneda local y
   ustedes nos pagan en dólares.
4. Esos dólares van a nuestra entidad en Emiratos Árabes Unidos, desde donde
   liquidamos a los comercios una vez que han llegado los fondos de todos los
   mercados.

El monto de la operación de liquidación es de USD 100.000 como mínimo, y
normalmente bastante mayor. El bloqueo de tipo de cambio por transacción
corresponde al lado de la recaudación, donde los montos individuales son
pequeños.

Cinco preguntas que definen esto para nosotros:

1. ¿El tipo de cambio que cotizan por transacción es vinculante para ustedes
   al momento de la liquidación, o es referencial?
2. ¿Por cuánto tiempo puede mantenerse un tipo de cambio?
3. ¿Cuántos bloqueos diarios podemos realizar, y existe un tope de exposición
   abierta agregada?
4. ¿Existe un tope al monto de una transferencia individual al exterior, o al
   volumen diario agregado?
5. ¿Bajo qué licencia operan, y bajo qué concepto registrarían una
   transferencia agrupada de fondos recaudados de comercios hacia nuestra
   entidad en Emiratos?

Estamos disponibles para firmar un acuerdo de confidencialidad antes de
entrar en volúmenes por moneda.

Andrey Novikov
Head of Expansion, Unlimit
+52 55 7013 3122 · a.novikov@unlimit.com
```

**Add for Colombia (Giros y Finanzas):** the SICSFE registration is for
payment, collection and transfer — the closest licence fit of any of the ten
markets. Ask whether it covers holding a quoted rate, which is the part that
is not obviously inside it.

**Add for Peru (Rextie, Kambista):** ask what the SBS registration permits on
outward corporate transfers, and whether an ETF authorisation is needed on top.

---

## Do not approach

Recorded so nobody on the team writes to them by mistake.

| Firm | Market | Why |
|---|---|---|
| Argentex | UK | Stopped trading 17 Jul 2025, special administration 21 Jul 2025, GBP 112m owed, client funds still frozen |
| Advanced Corretora de Câmbio | Brazil | Extraordinary liquidation since 15 Jan 2026, reported insolvent |
| Frente Corretora | Brazil | Out-of-court liquidation, 25 Sep 2026 |
| CIBanco | Mexico | Licence revoked by CNBV Oct 2025, IPAB liquidation, named in the Jun 2025 FinCEN action |
| Intercam | Mexico | Jun 2025 FinCEN money-laundering action |
| Vector Casa de Bolsa | Mexico | Same FinCEN action, licence surrendered Dec 2025 |

And one name to get right: the broker is **lumonpay.com**. `lumon.com` is an
unrelated Finnish balcony-glazing company.
