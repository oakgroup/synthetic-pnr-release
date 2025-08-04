
# Synthetic PNR Dataset: Technical Data Release Report (v1.86)

## 1. Executive Summary

This technical report describes the release of a synthetic Passenger Name Record (PNR) dataset simulating international air travel across 36 countries in the Schengen region. The dataset supports research in mobility analytics, aviation security, and synthetic data generation. It comprises over 4.7 million synthetic passengers across more than 4,000 flights occurring during the year 2019. All data is privacy-preserving and formatted in XML following the IATA PNRGOV schema.

## 2. Data Generation Pipeline

The data generation follows a five-stage workflow:

1. **Synthetic Population Generation**
   - Based on UN, World Bank, and Eurostat statistics.
   - Households and individuals generated using demographically-consistent sampling.
   - Names, addresses, and other personal fields assigned using the `Faker` library.

2. **Social Network Creation**
   - Community-level social links define likelihood of travel companionship.
   - Pairwise relationships derived probabilistically.

3. **Trip Planning via Agent-Based Model**
   - Individual agents plan trips based on travel propensities.
   - Trips include purpose, duration, group size, and destination selection.

4. **Flight Booking Simulation**
   - Available flights simulated based on real-world routes and schedules.
   - Booking algorithm includes ±2 day search window and maximum 8-hour layovers.
   - Additional attributes include ticket purchase date, baggage, agency, and payment method.

5. **XML PNR File Construction**
   - Each PNR written as a standalone XML file validated against IATA schema.
   - Includes metadata and structured tags for passenger, itinerary, and document info.

## 3. Dataset Contents and Schema

The XML schema used in this dataset follows the IATA PNRGOV XML Implementation Guide v16.1, published by the International Air Transport Association (IATA).

🔗 [IATA PNRGOV XML Implementation Guide v16.1 (PDF)](https://www.iata.org/contentassets/18a5fdb2dc144d619a8c10dc1472ae80/pnrgov20xml20implementation20guide2016_1.pdf)

- **Total passengers**: ~4.7 million
- **Flights**: ~4,250
- **Occupied seats**: 378,000
- **Capacity**: 664,000
- **Countries simulated**: 36 (EU + selected international origin/destination pairs)
- **Data format**: XML compliant with IATA PNRGOV XSD
- **Data location**: `data/sample/` folder for examples, full dataset in release



### Schema Overview

- **Passenger**: ID, name, date of birth, nationality, document type/number, gender
- **Trip/Booking**: Booking ID, purpose, group size, purchase lead time
- **Flights**: Origin, destination, departure/arrival time, occupancy, carrier code

## 4. Validation and Quality Checks

We compared the synthetic dataset to real aggregated statistics using Kullback-Leibler Divergence (KLD), Wasserstein Distance (WD), and RMSE. Key validation outcomes:

| Metric                            | Result       | Interpretation                            |
|----------------------------------|--------------|-------------------------------------------|
| Age-gender distribution (FR, GR) | KLD ≤ 0.048  | Good demographic realism                   |
| Hourly occupancy per flight      | KLD = 0.015  | Matches diurnal flow                       |
| Daily PAX count (Mar–May 2019)   | KLD = 0.071  | Drift later in year observed               |

Known over-representations:
- High number of solo travelers
- Long-tail in number of checked bags

## 5. Known Limitations and Issues

| ID      | Issue                                             | Impact                                     | Planned Fix                             |
|---------|---------------------------------------------------|--------------------------------------------|------------------------------------------|
| INC-001 | Greek PNRs without ATH/HER (e.g., 3967778.xml)    | Spurious Greek routes                      | Re-check flight source/dest alignment    |
| INC-002 | PNRs without passenger's home country             | Unrealistic OD chains                      | Add synthetic population for other nodes |
| INC-003 | Unrealistic names for nationality (e.g., Uganda)  | Breaks cultural realism                    | Locale fallback for `Faker` by region    |
| INC-004 | Inconsistent doc vs birth vs nationality          | Implausible identity records               | Force alignment with birth country       |
| INC-005 | Disconnected or implausible flights               | Invalid transit logic                      | Align return bookings by day skip        |

Additional systemic limitations:
- No expert-in-the-loop validation yet
- Seasonal bias toward early-year flights
- Some underrepresented countries in population model

## 6. Versioning and Updates

- **Current version**: v1.86
- **Release date**: 2025-08-04
- **Next update**: Planned v1.9 to correct INC-001–005 issues
- Follows semantic versioning (MAJOR.MINOR.PATCH)

## 7. Recommended Use

Appropriate for:
- Prototyping mobility models and travel network analytics
- Security and risk modeling research
- Benchmarking entity resolution and synthetic data generation tools

Not appropriate for:
- Real-world passenger ID re-identification
- Operational security systems without retraining on real-world data

## 8. License and Citation

This dataset is licensed under **CC-BY 4.0**. Cite the original paper:

> Fadlian, M. F., Ireson, N., & Lanfranchi, V. (2025). *Generating realistic Passenger Name Records with privacy compliance for security analysis*. Proceedings of the International ISCRAM Conference. [https://doi.org/10.59297/mbf3dq94](https://doi.org/10.59297/mbf3dq94)

DOI for the dataset will be provided in v1.9 via Zenodo.

---

---

## Appendix A – Synthetic PNR Data‑Set Field Reference

Each table below lists the XML fields produced by **`PNRGeneration()`** and delivered in the released synthetic Passenger Name Record (PNR) data‑set.

* **Default** – the value is a fixed constant or placeholder inserted by the generator.
* **Generated** – the value is created from the underlying synthetic flight / booking / passenger inputs or computed during generation.

---

## Flight‑Level Fields

| XML Path / Attribute | Description | Default / Generated | Comment (business meaning) |
| --- | --- | --- | --- |
| `/Originator/@AirlineCode` | Carrier IATA code sending the message | Generated | Identifies the airline that created the PNR‑GOV transmission. |
| `/Originator/@SystemCode` | Internal system identifier | **Default** | Placeholder representing the airline’s reservation system code. |
| `/Originator/@AirlineContactInfo` | Airline contact text | **Default** | Human‑readable contact information for the airline. |
| `/FlightLeg/@CarrierCode` | Operating carrier IATA code | Generated | Same as AirlineCode; shows carrier actually operating the flight. |
| `/FlightLeg/@FlightNumber` | Flight number | Generated | Scheduled flight number published to passengers. |
| `/FlightLeg/@DepartureDateTime` | Scheduled departure timestamp | Generated | Date & time the flight is planned to depart (local airport time, ISO‑8601). |
| `/FlightLeg/@ArrivalDateTime` | Scheduled arrival timestamp | Generated | Date & time the flight is planned to arrive. |
| `/FlightLeg/@DateChangeNbr` | Calendar‑day difference | Generated | `0`, `1`, etc. – indicates arrival the next day, two days later, etc. |
| `/FlightLeg/DepartureAirport/@LocationCode` | Origin airport (IATA) | Generated | Three‑letter airport code where the flight begins. |
| `/FlightLeg/ArrivalAirport/@LocationCode` | Destination airport (IATA) | Generated | Three‑letter airport code where the flight ends. |
| `/PNRs/@NumberOfPnrs` | Number of bookings reported in this message | Generated | Count of distinct reservations (PNRs) associated with the flight leg. |

---

## Booking‑Level Fields

| XML Path / Attribute | Description | Default / Generated | Comment (business meaning) |
| --- | --- | --- | --- |
| `/PNR/@NumberOfPassengers` | Party size contained in the booking | Generated | How many travellers share this reservation. |
| `/PNR/@PNR_TransDate` | Last transaction date | Generated | Date the PNR was last updated in the system. |
| `/PNR/@PNR_CreationDate` | Date the PNR was created | Generated | First date the reservation was entered. |
| `/PNR/@LastTktDate` | Ticketing date | Generated | Date the booking was ticketed / paid. |
| `/PNR/BookingRefID/@ID` | Booking reference | Generated | **Booking code for each booking** – the record‑locator unique to this reservation. |
| `/BookingRefID/CompanyName/@TravelSector` | Travel sector code | **Default** | “1” signifies airline sector per IATA spec. |
| `/BookingRefID/CompanyName/@Code` | Travel‑agency identifier | Generated | Code of the agency or channel that sold the ticket. |
| `/PNR/SSR/@SSR_Code` | Special‑service request code | **Default** | “OTHS” denotes a miscellaneous SSR. |
| `/PNR/SSR/@ServiceQuantity` | Quantity for the SSR | **Default** | Always `1` for generic placeholder service. |
| `/PNR/SSR/@Status` | SSR status | **Default** | “HN” – holding need, i.e., service requested. |
| `/PNR/SSR/@BoardPoint` | Boarding airport for SSR | Generated | Origin airport where the SSR applies. |
| `/PNR/SSR/@OffPoint` | Off‑point airport for SSR | Generated | Destination airport for the SSR. |
| `/PNR/SSR/@RPH` | Reference passenger handle | Generated | Links the SSR to the lead passenger. |
| `/PNR/POS/Source/@AgentSine` | Agent sign‑on / airline code | Generated | Identifies the airline office issuing the booking. |
| `/PNR/POS/Source/@PseudoCityCode` | Pseudo‑city code | Generated | Office identifier for the booking agent/agency. |
| `/PNR/POS/Source/@ISOCountry` | Country of sale | Generated | Two‑letter ISO country where booking took place. |
| `/PNR/POS/Source/@ISOCurrency` | Sale currency | Generated | Three‑letter ISO currency code used for payment. |
| `/PNR/POS/Source/@AgentDutyCode` | Agent duty code | **Default** | Describes agent’s functional role (placeholder). |
| `/PNR/POS/Source/@AirlineVendorID` | Airline vendor ID | Generated | Repeats operating airline code owning the PNR. |
| `/PNR/POS/Source/@AirportCode` | Airport location of POS | Generated | Airport associated with the selling office. |
| `/PNR/ContactInfo/@PhoneNumber` | Contact phone number | Generated | Primary telephone recorded for the booking. |
| `/PNR/ContactInfo/@PhoneType` | Phone type code | **Default** | `4` = mobile phone per IATA tables. |
| `/PNR/ContactInfo/@EmailAddress` | Contact e‑mail address | Generated | Synthetic e‑mail constructed from passenger name. |
| `/PNR/ContactInfo/StreetNmbr/@StreetNmbrSuffix` | Street number suffix | **Default** | Placeholder value `123` in synthetic data. |
| `/PNR/ContactInfo/StreetNmbr/@StreetDirection` | Street direction | **Default** | Placeholder value `N/`. |
| `/PNR/PrepaidBag/@UnitOfMeasureQuantity` | Number of prepaid checked bags | Generated | How many bags were prepaid online. |
| `/PNR/PrepaidBag/@UnitOfMeasureCode` | Unit code for baggage | **Default** | “700” corresponds to bags per IATA. |
| `/PNR/PrepaidBag/@Amount` | Amount paid for prepaid bags | Generated | Total fee charged for the bags. |
| `/PNR/PrepaidBag/@CurrencyCode` | Currency of the bag fee | Generated | Same currency as used for the ticket. |
| `/PNR/PrepaidBag/@BagDestination` | Baggage destination airport | Generated | Final destination where bags are tagged to. |
| `/PNR/Flight/@DepartureDateTime` | Segment departure time | Generated | Scheduled departure of this itinerary leg. |
| `/PNR/Flight/@ArrivalDateTime` | Segment arrival time | Generated | Scheduled arrival of the leg. |
| `/PNR/Flight/@ResBookDesigCode` | Booking class | **Default** | “S” economy class in synthetic data. |
| `/PNR/Flight/@NumberInParty` | Passengers booked in this segment | Generated | Mirrors party size. |
| `/PNR/Flight/@Status` | Segment booking status | **Default** | “HK” – holding confirmed. |
| `/PNR/Flight/@FlightNumber` | Sequential leg number inside PNR | Generated | `1`, `2`, … for multi‑leg journeys. |

---

## Passenger‑Level Fields

| XML Path / Attribute | Description | Default / Generated | Comment (business meaning) |
| --- | --- | --- | --- |
| `/Passenger/@RPH` | Passenger reference | Generated | Sequential identifier of passenger within the booking. |
| `/Passenger/@SurnameRefNumber` | Surname reference number | Generated | Groups passengers sharing the same family name. |
| `/Passenger/@BoardingStatus` | Boarding status code | **Default** | “63” indicates the passenger has boarded / is checked‑in. |
| `/Passenger/GivenName` | First/given name | Generated | Passenger’s given name (capitalised). |
| `/Passenger/MiddleName` | Middle name(s) | **Default (blank)** | Unused in the current synthetic data set. |
| `/Passenger/SurnamePrefix` | Name prefix | **Default (blank)** | e.g., “de”, “van”; blank here. |
| `/Passenger/Surname` | Family name | Generated | Passenger’s surname (capitalised). |
| `/Passenger/NameTitle` | Title (Mr/Ms/Dr) | **Default (blank)** | Not populated in synthetic data. |
| `/Passenger/CustLoyalty/@ProgramID` | FFP program ID | Generated/Default | “PROGRAM_ID” if frequent flier, otherwise “‑”. |
| `/Passenger/CustLoyalty/@MembershipID` | FFP membership number | Generated/Default | Booking id when enrolled; “‑” when not. |
| `/Passenger/ExcessBaggage/*` | Excess baggage coupon | **Default (mostly blank)** | Placeholder structure for airport‑purchased excess bags. |
| `/Passenger/FareInfo/@CountryCode` | Country of sale | Generated | Country where ticket was sold to passenger. |
| `/Passenger/FareInfo/…` | Fare basis, discounts, cabin class | **Default** | Constant placeholders (`PTC_CODE`, `SFLY`, etc.). |
| `/Passenger/SSR/*` | Passenger‑level SSR | Mixed | E.g., accessibility or service requirements. |
| `/Passenger/TicketDocument/@TicketDocumentNbr` | Ticket coupon number | Generated | `{flight}/{booking}/{passenger}` unique number. |
| `/Passenger/TicketDocument/@Type` | Document type | **Default** | “1” = electronic ticket. |
| `/Passenger/TicketDocument/@DateOfIssue` | Ticket issue date | Generated | Matches flight day for synthetic data. |
| `/Passenger/TicketDocument/@TicketLocation` | Ticketing location | Generated | Airport where the ticket was issued (origin). |
| `/Passenger/TicketDocument/@PrimaryDocInd` | Primary ticket flag | **Default** | Always “TRUE” in synthetic data. |
| `/Passenger/TicketDocument/TotalFare/@Amount` | Fare amount for this passenger | Generated | Price per passenger. |
| `/Passenger/TicketDocument/TotalFare/@CurrencyCode` | Currency of fare | Generated | Currency used for payment. |
| `/Passenger/TicketDocument/PricingInfo/*` | Pricing metadata | Generated | Shows sale date/time, country, refundability flags. |
| `/Passenger/TicketDocument/Taxes/Tax/*` | Ticket tax lines | Generated | 10 % of fare; TaxType “BK”. |
| `/Passenger/TicketDocument/PaymentInfo/*` | Payment method details | Generated / Default | Synthetic payment info; `PaymentUse` fixed “NEW”. |
| `/Passenger/DOC_SSR (DOCA)` | Residence address SSR | Generated | Address where passenger resides. |
| `/Passenger/DOC_SSR (DOCS)` | Passport details SSR | Generated | Passport number, nationality, DOB, expiry, etc. |
| `/Passenger/DOC_SSR (DOCO)` | Visa / permit SSR | Generated | Travel document or visa issuance information. |

**Legend**

* **Default** — hard‑coded constant used for every record.  
* **Generated** — value derived from flight, booking, or passenger inputs, or randomly synthesised during generation.


