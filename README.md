# nema17-planter

A 3D-printable seed metering unit (planter box) driven directly by a **NEMA 17** stepper motor.


<img width="666" height="574" alt="Assembled metering unit with SORGO-0 plate" src="https://github.com/user-attachments/assets/c87f3eb6-a007-49f3-b50d-76e5d03d978d" />

The plate, housing and lid are printable on any 200×200 bed. The plate mounts straight onto the motor shaft, so the seed rate is set entirely in software — no chains, no sprockets, no ground-drive wheel. Swapping crops means swapping a plate.

<img width="653" height="533" alt="Housing and internals" src="https://github.com/user-attachments/assets/c267a579-cbdd-48a2-90ae-fe2345f315ee" />

---

## Why

Main Reason: to develope a cheap planter for sorghum and maybe corn.

Driving the plate with a stepper decouples plate speed from ground speed. Feed it a speed signal (GPS, encoder, or a fixed assumption for a walk-behind) and the controller computes the plate RPM. That also opens the door to variable rate and to plot-by-plot population treatments without touching the machine.

Designed to plug into an ESP32 / AgOpenGPS-style stack, but the mechanics don't care what drives them.

---

## Status

⚠️ **Work in progress.** STEP files are being uploaded. Nothing here has been through a full field season yet — treat plate hole sizes as starting points, not gospel. Calibrate on a bench before you trust it in a plot.

---

## Repository layout

```
step/          STEP files (housing, lid, plates, motor mount)
stl/           Ready-to-slice meshes
plates/        One file per crop/rate combination
docs/          Photos, assembly notes
```

*(TODO: adjust once the files are pushed.)*

---

## Seed plates

Plates are engraved on the face with crop, hole diameter, and cell count so you can identify them by eye once they're covered in graphite and dust.

| Plate | Crop | Hole ⌀ | Cells / rev | Notes |
|---|---|---|---|---|
| `SORGO-0` | Sorghum | 5 mm | 100 | First iteration, shown above |
| — | — | — | — | *more to come* |

Naming convention: `CROP-N`, where `N` is the spoon model. If a plate works, it keeps its number.

---

## Rate calculation

The plate turns one cell per seed. So:

```
steps_per_seed = (steps in on rev) / cells_per_plate
```

and for a target population at a given ground speed:

```
plate_rpm = (speed_m_s × seeds_per_m × 60) / cells_per_plate
```

Example — `SORGO-0` (100 cells), 20 seeds/m target, 5 km/h (1.39 m/s):

```
plate_rpm = (1.39 × 20 × 60) / 100 = 16.7 rpm
```

At 1/16 microstepping and direct drive that's 3200 steps/rev → ~890 steps/s. Comfortably inside what a NEMA 17 will do without stalling, with a lot of headroom left.

**Sanity check the rate on a bench** — run a known number of revolutions over a tray and count what falls out. Actual cells filled per revolution is rarely exactly the hole count: doubles and skips depend on seed size distribution, plate speed, and how well the cutoff is set.

---

## Bill of materials

| Item | Qty | Notes |
|---|---|---|
| NEMA 17 stepper | 1 | TODO: specify holding torque used |
| Stepper driver | 1 | TODO: TMC2209 / A4988 / DRV8825? |
| M3 screws + heat-set inserts | TODO | For lid and motor mount |
| Printed housing | 1 | |
| Printed lid | 1 | |
| Printed seed plate | 1 per crop | |

**Print settings (starting point):** PETG or ABS for anything that sees sun. PLA will creep and warp in a field box in January. 0.2 mm layers, 4 perimeters on the housing. Plates print flat, face up, no supports — and the sealing face wants to be smooth, so print it against the bed.

---

## Roadmap

- [ ] Publish STEP + STL files
- [ ] Plates for canola, wheat, soybean
- [ ] Seed level sensor
- [ ] Seed-drop optical counter for closed-loop verification (skip / double detection)
- [ ] ESP32 firmware + AgOpenGPS section-control integration
- [ ] Field validation vs. a mechanical meter

---

## License

TODO — pick one. Consider CERN-OHL-S or a permissive one (MIT / CC-BY-SA) depending on whether you want derivatives shared back.

## Author

Joaquín Peraza

Contributions and plate designs welcome. Open an issue if you build one — especially if it doesn't work.
