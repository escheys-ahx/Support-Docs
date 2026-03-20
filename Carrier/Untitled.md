# Address Flow: TRUPATH LOGISTICS LLC (DOT 4474169) — Packet #9370064

## 1. Raw Packet Data

### Factor (SUNBELT FINANCE)
- physical_address: 2900 BROWNS LN, JONESBORO, AR, 72401, US
- remit_address:    PO BOX 1000, DEPT 144, MEMPHIS, TN, 38148, US

### Carrier Detail Addresses
- physical: 3303 LAKESIDE DR, AUSTIN, TX, 78723, US
- mailing:  1 CHISHOLM TRAIL RD, STE 450, ROUND ROCK, TX, 78681, US

### Payment
- payment_term.is_type: "factoring"
- payment_method:       null

---

## 2. highway-packet-complete.ts — Address Mapping

### Step A: Default billingAdd (from payment_method)
billingAdd = {
  line1: undefined,   // payment_method is null
  city:  undefined,
  ...
}

### Step B: shippingAdd (from carrier_detail.addresses where is_type == 'physical')
shippingAdd = {
  line1:   "3303 LAKESIDE DR",
  city:    "AUSTIN",
  state:   "TX",
  zipCode: "78723",
  country: "US"
}

### Step C: Evaluate conditions
hasRemit = (payment_term.is_type == 'factoring')         → true
        && (!!factor)                                     → true
        && (!!factor.remit_address_street1 = "PO BOX 1000") → true
        && (!!factor.remit_address_city = "MEMPHIS")         → true
        && (!!factor.remit_address_state = "TN")             → true
        && (!!factor.remit_address_postal_code = "38148")    → true
        && (!!factor.remit_address_country = "US")           → true
        ✅ hasRemit = TRUE

hasFactor = (payment_term.is_type == 'factoring')                → true
         && (!!factor)                                            → true
         && (!!factor.physical_address_street1 = "2900 BROWNS LN") → true
         && (!!factor.physical_address_city = "JONESBORO")         → true
         && (!!factor.physical_address_state = "AR")               → true
         && (!!factor.physical_address_country = "US")             → true
         && (!!factor.physical_address_postal_code = "72401")      → true
         ✅ hasFactor = TRUE

### Step D: Override billingAdd (hasRemit is TRUE, so first branch wins)
billingAdd = {
  name:  "SUNBELT FINANCE",
  line1: "PO BOX 1000",
  line2: "DEPT 144",
  city:  "MEMPHIS",
  state: "TN",
  zipCode: "38148",
  country: "US"
}

### Step E: Set remitAdd (hasRemit is TRUE)
remitAdd = {
  name:  "SUNBELT FINANCE",
  line1: "PO BOX 1000",
  line2: "DEPT 144",
  city:  "MEMPHIS",
  state: "TN",
  zipCode: "38148",
  country: "US"
}

### Step F: Set factorAdd (hasFactor is TRUE)
factorAdd = {
  name:  "SUNBELT FINANCE",
  line1: "2900 BROWNS LN",
  line2: null,
  city:  "JONESBORO",
  state: "AR",
  zipCode: "72401",
  country: "US"
}

### CarrierData constructed with:
new CarrierData(
  billingAddress:   PO BOX 1000, MEMPHIS, TN        (factor remit)
  shippingAddress:  3303 LAKESIDE DR, AUSTIN, TX     (carrier physical)
  remitAddress:     PO BOX 1000, MEMPHIS, TN         (factor remit)
  factoringAddress: 2900 BROWNS LN, JONESBORO, AR    (factor physical)
)

---

## 3. Routing Decision

Carrier status: "onboarded" (not PROCESSING or REQUESTED)
→ isProcessing = FALSE
→ Takes the ELSE IF branch (line 257)
→ Calls: updateCarrierHighway()  ← NOT registerCarrier()

---

## 4. updateCarrierHighway() — What Actually Gets Saved

### Step A: Billing address override (line 1320-1321)
billingAdd = carrierData.factoringAddress     ← "2900 BROWNS LN, JONESBORO, AR"
          || carrierData.remitAddress          ← (never reached)
          || carrierData.billingAddress        ← (never reached)

⚠️  factoringAddress (factor physical) wins over remitAddress (factor remit)

### Step B: Only ONE address record created (line 1358-1365)
Address.findOrCreate({
  uid:     carrierUser.uid,
  type:    AddressType.BUSBILL,            ← ONLY this type
  line1:   "2900 BROWNS LN",
  city:    "JONESBORO",
  state:   "AR",
  zipCode: "72401",
  country: "US"
})

### Addresses NOT created:
❌ AddressType.SHIP   (shippingAddress)  — never written
❌ AddressType.CARREM (remitAddress)     — never written
❌ AddressType.FACREM (factoringAddress) — never written

---

## 5. Final Result in UI (Manage Carriers)

| Field        | Expected                              | Actual                               | Status |
|-------------|---------------------------------------|--------------------------------------|--------|
| Billing     | PO BOX 1000, MEMPHIS, TN (remit)     | 2900 BROWNS LN, JONESBORO, AR       | ❌ Wrong (factor physical, not remit) |
| Shipping    | 3303 LAKESIDE DR, AUSTIN, TX         | N/A                                  | ❌ Missing |
| Remittance  | PO BOX 1000, MEMPHIS, TN             | N/A                                  | ❌ Missing |
| Factoring   | 2900 BROWNS LN, JONESBORO, AR        | N/A                                  | ❌ Missing |

---

## 6. Root Causes

### Cause 1: updateCarrierHighway only writes BUSBILL
registerCarrier creates 4 address types (BUSBILL, SHIP, CARREM, FACREM).
updateCarrierHighway creates 1 address type (BUSBILL only).

### Cause 2: Wrong billing priority in updateCarrierHighway
Line 1320: const billingAdd = carrierData.factoringAddress || carrierData.remitAddress || ...
→ factoringAddress (factor physical) takes precedence over remitAddress (factor remit)
→ The remit address (where payments should go) gets overwritten by the factor physical address