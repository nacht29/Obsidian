
## SSSG:

![[Pasted image 20260714112423.png]]

Caring SSSSG definition:
- Store has generated sales for past 24 months
- Must be a full month: e.g. current Jul 14, 2026 - calculation until 31 Jun, 2026


---

## ArticleCode vs Article Settlement

- Each action has an `ArticleCode`
- `ArticleSettlement` is used to signify vendor/supplier rebates and is meant for trade related activities
- Non-trade activities, such as laptop purchases do not have `SettlementArticle` but will have `ArticleCode`

---

## Data layers (silver)

- R1 R2 not sure
- R3 = combine Big and Caring data

---

## BOType
Priority items: Caring Pharmacy exclusive items, hence prices can be set higher. BCG puts priority on these items.

---

## PostingDate & SettlementDate

- `PostingDate` is supposedly the same as `SettlementDate`
- The dates are generated when a contract (`sap_conditionalcontract`) is settled
- Hence some Trade Income records can have NULL `PostingDate`

---

## ManufacturerCode

Not all Trade Income articles are assigned a `ManufacturerCode`.

