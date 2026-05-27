# Closing the Greenwood Coverage Gap: Historical Street Name Mapping

## Context

We've geocoded the Tulsa 1921 pipeline CSV against the NYTimes burned-area polygon and found:
- **862** entries from the alphabetical `(c)` section inside the burned area
- **462** entries from the street directory inside the burned area
- **~1,294** unique names combined

The NYTimes published **1,675** entries. The ~380-entry gap is concentrated on **historical Tulsa street names** that no longer exist — BULLETTE AV (109 entries), POCAHONTAS (45), BRYANT (68), HARTFORD (12), EXETER PLACE (68), CRUICE AV (61), RUTH AV (24), VERNON (26), WILLIAMS (63), FAIRVIEW (157). Google Maps geocodes these streets to wrong locations (or generic Tulsa center), so the point-in-polygon test rejects them all.

The NYTimes closed this gap using 1921 street maps and Sanborn insurance maps. We need to do the same — build a lookup table of historical name → modern name (or → coordinate range), then re-geocode with translated names.

**Key constraint**: `tulsa-roads.geojson` from the NYTimes repo has NO street name attributes — only `RecNum` IDs. It cannot be used as a name lookup.

## The gap is primarily a research task, not a coding task

The implementation is straightforward once the street name mapping exists. The hard part is finding where each historical street is today.

---

## Step 1: Build a historical street name lookup table (research)

Create `output/tulsa_1921/historical_streets.json` — a JSON object mapping 1921 street name → modern geocodable substitute:

```json
{
  "BULLETTE AV": "N Greenwood Ave",
  "POCAHONTAS": "N Elgin Ave",
  "BRYANT": "...",
  ...
}
```

**Sources to consult (in order of ease):**
1. The NYTimes city directory CSV itself — addresses on these streets appear alongside people we can cross-reference to the alphabetical section (which has geocodable modern addresses), letting us infer approximate block locations
2. Oklahoma Historical Society documentation on Greenwood street renames
3. OSM historical name tags — search OpenStreetMap for `name:historic` or `old_name` tags around the Greenwood neighborhood
4. The Oklahoma Commission report (2001) cited in the NYTimes README — the burn boundary source

**Streets to resolve** (by NYTimes entry count, highest priority first):

| Street | Our entries | NYTimes entries | Notes |
|--------|------------|-----------------|-------|
| BULLETTE AV | 109 | 96 | High priority |
| FAIRVIEW | 157 | 19 | May be outside polygon — verify |
| BRYANT | 68 | 63 | High priority |
| EXETER PLACE | 68 | 64 | High priority |
| WILLIAMS | 63 | 62 | High priority |
| CRUICE / CRUCE AV | 61 | 31 | Possibly a spelling variant |
| POCAHONTAS | 45 | 40 | High priority |
| HARTFORD AV | 12 | 83 | Large NYTimes gap — our OCR may have missed entries |
| RUTH AV | 24 | 22 | |
| VERNON | 26 | 22 | |

---

## Step 2: Add street alias translation to the geocoding script (code)

**File**: `analysis/find_greenwood_street_entries.py`

Add a `_STREET_ALIASES` dict and apply it in `build_address()` before constructing the geocodable string:

```python
_STREET_ALIASES = {
    "BULLETTE": "N Greenwood",   # example — fill in from Step 1
    "POCAHONTAS": "...",
    # ...
}

def build_address(house_number, street_name):
    num = house_number.strip()
    street = street_name.strip()
    # strip direction suffix ...
    street_upper = street.upper()
    for alias, modern in _STREET_ALIASES.items():
        if alias in street_upper:
            street = modern
            break
    return f"{num} {street}, Tulsa, OK"
```

Same change applies to `analysis/find_greenwood_entries.py` for the alphabetical section if any `(c)` entries have historical street names.

---

## Step 3: Re-run geocoding with aliases applied (code)

Since new addresses (with translated names) won't be in the cache, only the historical-street entries need new API calls. Estimated: ~400–600 new calls, well within free tier.

```bash
python analysis/find_greenwood_street_entries.py
python analysis/find_greenwood_entries.py
```

---

## Step 4: Combine and compare (code / analysis)

Run the deduplication comparison script to get a final combined unique count vs NYTimes.

---

## Alternative shortcut: NYTimes name-matching bridge

If the historical research in Step 1 proves too time-consuming, a faster alternative:

1. Fuzzy-match our pipeline entries against the NYTimes CSV by normalized name
2. For matched entries, mark `in_burned_area = True` without geocoding (we trust NYTimes' curation)
3. For unmatched pipeline entries on historical streets, flag as `likely_greenwood` pending research

This is less principled (relies on NYTimes curation rather than independent verification) but fast.

---

## Files involved

| File | Change |
|------|--------|
| `output/tulsa_1921/historical_streets.json` | New: manually created lookup table (Step 1) |
| `analysis/find_greenwood_street_entries.py` | Add `_STREET_ALIASES` + apply in `build_address()` |
| `analysis/find_greenwood_entries.py` | Same alias logic for alphabetical entries |

## Verification

After re-running both scripts:
- Check that BULLETTE AV, POCAHONTAS, etc. entries now appear in `greenwood_street_entries.csv`
- Verify coordinates land in the correct part of north Tulsa (not city center) by opening `greenwood_street_geocoded.geojson` in a GIS viewer or geojson.io
- Re-run the combined deduplication count and compare to NYTimes 1,675
