# The-Indu-Goblin

## Goblinize your Industry!

A comprehensive multi-purpose industry tool for managing and optimizing your Moon Mining, Planetary Interaction (PI), and future industry operations in EVE Online.

## Features

### 🔧 General
- **Market Integration**: Automatic price updates from EVE Tycoon API (Jita/The Forge region)
- **Optimization Tools**: Identify opportunities to improve your operations
- **EVE ESI Integration**: Secure authentication and data retrieval through EVE Online's ESI API
- **Multi-Module Architecture**: Expandable for future industry features

### 🌍 Planetary Interaction
- **Multi-character Support**: Add multiple characters to a single account using EVE universe IDs
- **Colony Overview**: View all your planetary colonies, their layouts, and outputs in one place
- **Production Flow Analysis**: Calculate current ISK values and potential yields at ~87.6% processing efficiency
- **Smart Facility Monitoring**: Know which facilities are REALLY producing.
- **Storage Monitoring**: Track facility contents and capacity
- **Extractor Management**: Monitor extractor cycles and depletion times

### 🌙 Moon Mining
- **Moon Survey Parser**: Paste moon survey data directly from EVE
- **Ore Refinement Calculator**: Automatic conversion of moon ores to refined materials
- **Profitability Analysis**: Calculate profit/loss with fuel costs and current market prices
- **Dual Efficiency Modes**: Support for Metenox Moon Drill (40%) and Athanor/Tatara (100%) extraction
- **Secondary Outputs**: Optional inclusion of mineral byproducts (Pyerite, Mexallon)
- **Market Price Updates**: Recalculate profitability with latest Jita prices anytime

### 🚀 Industry
- **Industry Profitability Check**: Check what is currently profitable to produce ISK/Hour or Revenue
- **Industry Calculator**: Instant profitability calculation for any industry job.
- **Industry Job Checker**: Import and check the status of your industry jobs across all of your characters
- **Visualisation**: Visualize your blueprint, production cycles and industry input/output item flow

### 🤖 Discord Notifications
- **Direct Chat Notification**: Sends your updates when your PI or Industry will stop or run into bottlenecks

## ESI API Integration & Data Flow

The application integrates with the following EVE Online ESI API endpoints:

### 1. Character Planets Endpoint (PI)

**URL:** `https://esi.evetech.net/latest/characters/{character_id}/planets/`

**Data Parsed:**
- Pins (extractors, factories, storage facilities, command centers)
- Links between pins
- Routes for material transport
- Pin contents (inventory)
- Extractor details (heads, cycle times, products)
- Factory details (schematics, cycle times)

**PI schematic:** 
Returns information about a PI schematic (production blueprint)

**Data Parsed:**
- Schematic name
- Cycle time (production time in seconds, e.g., 3600s for Mechanical Parts)
- Input materials
- Output products


### 2. Market Data (External API)

**Source:** EVE Tycoon API & ESI API

**Description:** Retrieves current market prices for materials and ores

**Data Parsed:**
- Type IDs
- Buy/sell prices (Jita - The Forge region 10000002)
- Market volumes
- Price history

### 2. Moon Mining Data

**Data Source:** User-submitted moon survey data from EVE Online

**Description:** Parses moon survey data and calculates mining profitability

**Input Format:** Tab-separated moon survey data copied from EVE Online client:
```
Moon    Moon Product    Quantity    Ore    TypeID    SolarSystemID    PlanetID    MoonID
Hemouner V - Moon 3            Coesite    0.19814376533    45493    30003902    40246856    40246859
Hemouner V - Moon 3            Sylvite    0.249667495489    45491    30003902    40246856    40246859
```

**Data Parsed:**
- Moon name (e.g., "Hemouner V - Moon 3")
- Ore type IDs (e.g., 45493)
- Ore names (e.g., Coesite, Sylvite, Zeolites)
- Ore percentages/composition (0.0 to 1.0)
- System, planet, and moon IDs

**Processing Steps:**
1. **Ore Identification**: Maps ore type IDs to moon ore names
2. **Volume Calculation**: Converts percentages to ore units based on chunk size (default: 1,000,000 m³)
3. **Refinement**: Applies reprocessing efficiency (default: 87.6%) to calculate refined materials
4. **Material Classification**: Categorizes materials by tier (R64, R32, R16, R8, R4, Secondary)
5. **Market Pricing**: Fetches current Jita buy/sell prices for all refined materials
6. **Fuel Cost Calculation**: 
   - Magmatic Gas: 200 units/hour (typeID: 81143)
   - Fuel Blocks: 5 units/hour (typeID: 4247/4246/4051/4312)
7. **Profitability Analysis**: Revenue from ores minus fuel costs over specified time period

**Saved To:**
- `moon_mining_operations` table (operation records)
- `moon_ores` table (refined materials per operation)
- Links to `price_data` table for current market prices

**Calculation Modes:**
- **Metenox Moon Drill Mode** (`use_metenox=true`): 40% extraction efficiency + fuel costs and calcualted refinement of 1.0
- **Athanor/Tatara Mode** (`use_metenox=false`): 100% efficiency, Athanor Fuel Costs and calcualted refinement of 0.876

**Optional Settings:**
- `include_secondary`: Include/exclude low-value minerals (Pyerite, Mexallon) - default: false
- `hours`: Mining duration (0.1 to 8760 hours) - default: user-specified
- `fuel_block_type_id`: Type of fuel blocks used (Helium, Hydrogen, Oxygen, Nitrogen)

