# EFT Spec
This document contains a proposal for modifications to the EFT format. It is intended to be backwards compatible, at least as functional as the current spec, and to be parsed by the EVE parser with no code changes by CCP. Comments , suggestions, and questions are welcome, as are example implementations.

Notes about the current format, the EVE parser, and the reasoning behind this all follow the proposal.

## The Proposal
I am proposing a few small changes to the format, all backwards compatible with EVE, and _nearly_ so with Pyfa's output. They disambiguate and formalize things and improve compatability with the EVE parser.
1. Section headers, surrounded by `%`, as in `%High slots%`. This removes ambiguity about items in the various bays, and makes parsing odd edge cases like freighters easier.
2. New sections `%Implants%` and `%Boosters%` to follow the cargo bay, for items that apply to the character flying the ship.
3. Abyssal modules to follow charges, if any, with a comma and a space and then the index number.
   1. The details should follow after all other sections and a format header, with all details on a single line, format below.
4. Offline modules to be marked as `/offline`
   1. This follows charges and abyssal mutation notation, if any.
   2. Marking modules as `/overheated` would not be accepted by the EVE parser, but is a reasonable extension that third-party devs could support.
5. Fitting tools should preserve slot order, including empty slots.


A blank fit with every possible section would look something like this:
```EFT
[Ship Type, Fit Name]

%Low slots%
[Empty Low slot]
[Empty Low slot]
[Empty Low slot]
[Empty Low slot]
[Empty Low slot]
[Empty Low slot]
[Empty Low slot]
[Empty Low slot]

%Med slots%
[Empty Med slot]
[Empty Med slot]
[Empty Med slot]
[Empty Med slot]
[Empty Med slot]
[Empty Med slot]
[Empty Med slot]
[Empty Med slot]

%High slots%
[Empty High slot]
[Empty High slot]
[Empty High slot]
[Empty High slot]
[Empty High slot]
[Empty High slot]
[Empty High slot]
[Empty High slot]

%Rig slots%
[Empty Rig slot]
[Empty Rig slot]
[Empty Rig slot]

%Subsystem slots%
[Empty Subsystem slot]
[Empty Subsystem slot]
[Empty Subsystem slot]
[Empty Subsystem slot]

%Service slots%
[Empty Service slot]
[Empty Service slot]
[Empty Service slot]
[Empty Service slot]
[Empty Service slot]

%Drone bay%

%Fighter bay%

%Cargo bay%

%Implants%

%Boosters%

%Mutated Modules%
```
Examples:

```EFT
[Loki, New EFT Example]

%Low slots%
Layered Energized Membrane II
Layered Energized Membrane II
Expanded Cargohold II
Expanded Cargohold II
Kinetic Energized Membrane II
Kinetic Energized Membrane II
Kinetic Energized Membrane II

%Med slots%
Corelum B-Type 10MN Afterburner
Medium Shield Extender II, 1
[Empty Med slot]

%High slots%
Heavy Assault Missile Launcher II
Heavy Assault Missile Launcher II /offline
Heavy Assault Missile Launcher II, Scourge Rage Heavy Assault Missile
Heavy Assault Missile Launcher II, Scourge Rage Heavy Assault Missile
Heavy Assault Missile Launcher II, Scourge Rage Heavy Assault Missile
True Sansha Medium Energy Nosferatu
[Empty High slot]

%Rig slots%
[Empty Rig slot]
[Empty Rig slot]
[Empty Rig slot]

%Subsystem slots%
Loki Core - Augmented Nuclear Reactor
Loki Defensive - Adaptive Defense Node
Loki Offensive - Launcher Efficiency Configuration
Loki Propulsion - Intercalated Nanofibers

%Drone bay%
Warrior II x2
Hammerhead II x3

%Cargo bay%
Navy Cap Booster 400 x5

%Implants%
High-grade Amulet Alpha
High-grade Amulet Beta
High-grade Amulet Gamma

%Boosters%
Standard Blue Pill Booster
Agency 'Pyrolancea' DB9 Dose IV

%Mutated modules%
1; Glorified Decayed Medium Shield Extender Mutaplasmid; capacityBonus 1060.0, cpu 37.0, power 28.0, signatureRadiusAdd 6.8
```

```EFT
[Fortizar, EFT example]

%Low slots%
[Empty Low slot]
[Empty Low slot]
[Empty Low slot]
[Empty Low slot]

%Med slots%
[Empty Med slot]
[Empty Med slot]
[Empty Med slot]
[Empty Med slot]
[Empty Med slot]

%High slots%
Standup Point Defense Battery I
Standup Multirole Missile Launcher I
Standup Multirole Missile Launcher I, Standup Cruise Missile
Standup Multirole Missile Launcher I, Standup Heavy Missile
[Empty High slot]
[Empty High slot]

%Rig slots%
Standup L-Set Point Defense Battery Control II
[Empty Rig slot]
[Empty Rig slot]

%Service slots%
Standup Cloning Center I
Standup Market Hub I
[Empty Service slot]
[Empty Service slot]
[Empty Service slot]

%Fighter bay%
Standup Einherji II x9
Standup Einherji II x9

%Cargo bay%
Standup XL Cruise Missile x1
Standup Cruise Missile x2
```

## Current spec
The current spec of EFT has, as noted, no central source of truth. As a practical matter, EVE and Pyfa define it. EVE's importer is incredibly permissive, as is Pyfa's.

### General format
The general format is as follows:
1. Header line with ship type and name, separated by comma and surrounded by square brackets.
2. Sections of slotted items
   1. Items in slots have no quantity by MAY have charges.
   2. Charges are separated from the item name by a comma, and also do not have a quantity.
   3. Each slotted section is separated by a single blank line, and they are in the following order, if present: Low, Med, High, Rig, Subsystem, Service.
   4. If a slot is not filled, it is replaced with the text `[Empty SECTION slot]`, where `SECTION` is the name of the section as given in 3.
3. Sections of items in bays
   1. Items in bays always have a quantity.
   2. Each bay section is separated by 2 blank lines, and they are in the following order, if present: Drone, Fighter, Cargo.

Examples:
```EFT
[Loki, EFT Example]

Layered Energized Membrane II
Layered Energized Membrane II
Expanded Cargohold II
Expanded Cargohold II
Kinetic Energized Membrane II
Kinetic Energized Membrane II
Kinetic Energized Membrane II

Corelum B-Type 10MN Afterburner
Medium Shield Extender II
[Empty Med slot]

Heavy Assault Missile Launcher II
Heavy Assault Missile Launcher II
Heavy Assault Missile Launcher II, Scourge Rage Heavy Assault Missile
Heavy Assault Missile Launcher II, Scourge Rage Heavy Assault Missile
Heavy Assault Missile Launcher II, Scourge Rage Heavy Assault Missile
True Sansha Medium Energy Nosferatu
[Empty High slot]

[Empty Rig slot]
[Empty Rig slot]
[Empty Rig slot]

Loki Core - Augmented Nuclear Reactor
Loki Defensive - Adaptive Defense Node
Loki Offensive - Launcher Efficiency Configuration
Loki Propulsion - Intercalated Nanofibers


Warrior II x2
Hammerhead II x3


Navy Cap Booster 400 x5
```

```EFT
[Fortizar, EFT example]

[Empty Low slot]
[Empty Low slot]
[Empty Low slot]
[Empty Low slot]

[Empty Med slot]
[Empty Med slot]
[Empty Med slot]
[Empty Med slot]
[Empty Med slot]

Standup Point Defense Battery I
Standup Multirole Missile Launcher I
Standup Multirole Missile Launcher I, Standup Cruise Missile
Standup Multirole Missile Launcher I, Standup Heavy Missile
[Empty High slot]
[Empty High slot]

Standup L-Set Point Defense Battery Control II
[Empty Rig slot]
[Empty Rig slot]

Standup Cloning Center I
Standup Market Hub I
[Empty Service slot]
[Empty Service slot]
[Empty Service slot]


Standup Einherji II x9
Standup Einherji II x9


Standup XL Cruise Missile x1
Standup Cruise Missile x2
```

### Pyfa extensions
Pyfa adds the following extensions to the format:
1. A module in a slot may be marked as offline by putting `/OFFLINE` after the module name, separated by a space.
2. Mutated modules retain their base module name, but have a `[N]` after them where `N` is a number, starting at 1 and continuing as needed for all mutated modules. A corresponding `[N]` is put at the bottom of the fit with the details of mutation.
3. Implants and boosters that are being applied to the fit are put between the Fighter and Cargo sections, separated from each other by 1 blank line if needed, and from the surrounding sections by 2 blank lines.

Examples:
```EFT
Heavy Assault Missile Launcher II /OFFLINE
```

```EFT
Medium Shield Extender II [1]

[1] Medium Shield Extender II
  Glorified Decayed Medium Shield Extender Mutaplasmid
  capacityBonus 1060.0, cpu 37.0, power 28.0, signatureRadiusAdd 6.8
```

```EFT
Warrior II x2
Hammerhead II x3


High-grade Amulet Alpha
High-grade Amulet Beta
High-grade Amulet Gamma

Standard Blue Pill Booster
Agency 'Pyrolancea' DB9 Dose IV


Navy Cap Booster 400 x5

```

### EVE parsing
EVE's parser is incredibly permissive. A few notes about it:
1. The header line with the ship type and fit name is required
2. The string /offline is ignored in all places, to include in the middle of module or fit names. (`Med/offlineium Shield Extender II` will parse and become a `Medium Shield Extender II`)
3. `<br>` is a valid linebreak, as is CRLF (`\r\n`) or just LF (`\n`).
4. Excess whitesapce (tabs or spaces) are ignored at the end of lines.
5. No order matters. Items are parsed into their appropriate slots, even if those slots don't exist on the ship (ie, a `Torpedo Launcher II` on a freighter fit will be parsed as going into a high slot and will simply disappear).
   1. The only exception to this is that modules with a quantity are parsed as being part of the cargo.
   2. Note that this does not apply to drones or fighters, which are always parsed as being in their appropriate bay (even if it dosn't exist on that ship).
6. Charges can be listed after a comma, but go to the cargohold.
   1. They are also assumed to fill the module's capacity, as they do not have a quantity. `Expanded Cargohold II, Nova Rage Rocket` will parse fine and give you `0x Nova Rage Rocket` in the fit.
7. The `[Empty X slot]` lines are not required, and if present will simply be ignored.

## Reasoning/FAQs
### Why change the EFT format?
The EFT format is a haphazard format with no source of truth and unsupported extensions. It is difficult to parse and ambiguous at best. It does not support a number of things, like states for modules or modules modified by Abyssal Mutaplasmids.

### Okay, why not a new format then?
It is also the almost universal format for EVE fits, supported by any tool that supports fits in any way. EVE supports it with import and export via clipboard, and it's both human readable and human writable. If you are not parsing it into a fit (as a shopping list, for example) it is trivial to parse.
