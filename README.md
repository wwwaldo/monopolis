# Monopolis

A browser-based economics simulator for a 1,024-parcel Monopoly-themed town. Residential and commercial mortgages, three industries with their own boom-bust cycles, a central bank, and one bank whose health feeds back into credit, all rendered as a 3D city you can orbit and inspect.

Everything lives in a single file, `index.html`. It needs no build step and no dependencies beyond two CDN scripts (three.js r128 and its OrbitControls).

## Run it

```bash
open index.html                 # or:
python3 -m http.server 8000     # then visit http://localhost:8000
```

## What's simulated

**The town.** A 32×32 grid laid out as 8×8 city blocks. Each block belongs to a Monopoly colour group:

| Neighbourhood | Group | Mix |
|---|---|---|
| Baltic Flats → Marvin Gardens | brown, light blue, pink, orange, red, yellow | houses, apartments, corner shops |
| Downtown | green | office towers, shops, apartments |
| Boardwalk | dark blue (beachfront) | hotels, condos, shops |
| Railroad Yards | grey | factories, warehouses |

The cheaper neighbourhoods react more strongly to house-price swings.

**Industries.** Manufacturing, Tech and Tourism & Retail. Each one's output gap is built from:
- a shared town cycle and the industry's own cycle (damped second-order oscillators with a set period and size),
- sensitivity to the real interest rate,
- a housing-wealth effect (strongest for tourism and retail),
- drag from a credit crunch,
- any shocks you trigger.

Each industry's gap sets its unemployment rate, wages, and office or industrial rents.

**Central bank.** By default it follows a Taylor rule toward a neutral real rate and an inflation target. Inflation responds to the output gap. You can switch the rule off and set the policy rate by hand.

**Mortgages.**
- *Houses* carry residential mortgages: fixed-rate loans that renew every N years at the market rate, or variable-rate loans that reprice monthly. New loans are capped by loan-to-value and payment-to-income limits.
- *Apartments, shops, hotels, offices and industrial buildings* carry commercial loans with a lump sum due at the end of the term. At maturity the loan must be refinanced within the loan-to-value and debt-service-coverage limits. If it can't be, the owner pays down the gap, sells, or defaults.

**Households and owners.** Each house is owned by an occupier who works in one industry, or by a landlord. Occupiers lose and find jobs at rates set by their industry's unemployment. Every owner keeps cash reserves. Three missed payments lead to a sale if the property still has equity, or to foreclosure if it doesn't.

**Prices.** The house price index moves toward a value set by incomes, population, how much people can borrow, and the cost of owning (mortgage rate + tax − expected price growth). Buyers extrapolate recent price growth, which creates bubbles. Foreclosure fire sales push prices down. Commercial price indices work the same way, using each industry's rents and occupancy.

**Foreclosure spillovers.** Each foreclosure knocks a few percent off nearby parcels' values, fading over time. Bank-owned buildings sell when there's enough credit to buy them. Ones left unsold too long get demolished back to vacant lots.

**Construction.** Vacant lots get developed when market value runs above construction cost. Developers use construction loans that must be refinanced a year after completion, so a bust that hits mid-boom causes defaults at maturity.

**The bank.** It earns a net interest margin and absorbs losses at the difference between the loan balance and the fire-sale price. When capital falls toward the requirement, it cuts loan-to-value limits and raises risk premiums, which slows the economy further. Below 2% capital the town bails it out.

## Interface

- **Left panel:** sliders for every variable, plus shock buttons (Tech crash, Factory boom, Tourism collapse, Rate shock +3%, Housing mania, Bank loss).
- **Centre:** the 3D map. Colour buildings by type, mortgage status, loan-to-value, 12-month value change, neighbourhood, or industry. Hover for a property card; click to pin it.
- **Right panel:** headline numbers, 20-year charts with recessions shaded, and a town news feed.

The seed box makes runs reproducible.
