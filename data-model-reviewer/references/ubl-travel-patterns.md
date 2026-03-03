# UBL Patterns for Travel Data

## Core Reusable Components

### Party
Any person or organization involved in a transaction.
```
Party:
  PartyIdentification: [{ ID, SchemeID }]  # e.g., AAA club ID, agent ID
  PartyName: { Name }
  PostalAddress: Address
  Contact: { Name, Telephone, Email }
```

### Address
Structured postal address. Never free-text a full address.
```
Address:
  StreetName
  AdditionalStreetName  # optional
  CityName
  PostalZone
  CountrySubentity  # state/province
  Country: { IdentificationCode }  # ISO 3166-1 alpha-2
```

### Period
A time range. Always ISO 8601.
```
Period:
  StartDate  # YYYY-MM-DD
  StartTime  # HH:MM:SS (optional)
  EndDate
  EndTime
```

### Amount
Money. Always pair value with currency.
```
Amount:
  Value: decimal
  CurrencyCode: string  # ISO 4217 (USD, CAD, etc.)
```

### Quantity
Countable things. Always pair value with unit.
```
Quantity:
  Value: decimal
  UnitCode: string  # UN/ECE Rec 20 unit codes
```

### DocumentReference
Link to another document.
```
DocumentReference:
  ID: string
  DocumentType: string
  IssueDate: date
```

## Travel-Specific Patterns

### TravelProduct (not standard UBL — TST domain concept)
Represents a bookable travel product. Map to UBL where possible:
- Use `Item` pattern for product description
- Use `Price` for pricing (Amount + calculation basis)
- Use `Period` for travel dates
- Use `Address` for origin/destination

### Booking / Reservation
Map to UBL `Order` pattern:
```
Booking:
  ID
  IssueDate
  BookingPeriod: Period  # travel dates
  BuyerParty: Party  # the traveler or AAA club
  SellerParty: Party  # the provider
  BookingLine: [  # line items
    { Item, Quantity, Price, Period }
  ]
  Status: code  # lifecycle state
```

### Traveler Information
Map to UBL `Party` with travel extensions:
```
Traveler:
  Party  # base identity
  TravelDocuments: [{ DocumentType, ID, IssueDate, ExpiryDate, IssuingCountry }]
  LoyaltyPrograms: [{ ProgramName, MemberID }]
  Preferences: { SeatPreference, MealPreference, etc. }
```

## Code Lists (Use Standard Values)

| Domain | Standard | Example |
|--------|----------|---------|
| Country | ISO 3166-1 alpha-2 | US, CA, MX |
| Currency | ISO 4217 | USD, CAD |
| Language | ISO 639-1 | en, es, fr |
| Date/Time | ISO 8601 | 2024-03-15T14:30:00Z |
| Units | UN/ECE Rec 20 | DAY, HUR, KMT |

## Anti-Patterns to Flag
- Free-text addresses instead of structured fields
- Currency assumed (USD) instead of explicit
- Dates in non-ISO formats (MM/DD/YYYY)
- Provider-specific IDs used as internal IDs (use your own, map externally)
- Nested objects that should be references (embedding full Party inside every Booking)
- Missing lifecycle states (booking with no status field)
