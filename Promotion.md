
## Category
- Promotion category (rebate RM3 etc)

## Best buy (Bonus buy)
- Fixed Price -> Z001
- Range (pct) -> Z002
- Formula:
	- P, R, %
	- P = Price deduction (fixed price)
		- `Original price: RM20`, `P = RM13`, can claim `20-13 = 7 `from supplier
		- Claim amount: Share ratio

### Bonus buy: Buy 1 Free 1 | 3 @ RM 18
1. B1F1
	- Row 1: Item=1002345, Price=23, BonusBuyCode=NULL
	- Row 2: Item=1002345, Price=0, BonusBuyCode=10001...
2. 3 @ RM x
	- Assume original price = RM10, 3 @ RM 18, user buys 4
	- Row: Qty=4, ItemCode=1002346, Price=RM28 (18 + 10), BonusBuyCode
	- Tie back to BonusBuyCode to get BonusBuyCat, and tie to original price to calculate:
		- 3 = Bonus buy
		- 1 = Original price
3. Price entry handled by cashier

