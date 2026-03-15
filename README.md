# Inpop

Compute high-precision planetary positions, lunar librations, and relativistic time transformations using the INPOP ephemerides.  

**Install via pip:**  
```bash
pip3 install Inpop
```

---

## Project description

**Inpop** is a pure Python library for **computing high-precision planetary positions, lunar librations, and relativistic time transformations** using the "Institut de mécanique céleste et de calcul des éphémérides"] (IMCCE) INPOP ephemerides.  

INPOP ephemerides are distributed as binary `.dat` files containing Chebyshev coefficients, which parameterize the barycentric motion of solar system bodies over fixed time intervals. Inpop reads these coefficients, evaluates the Chebyshev series, and computes positions, velocities, libration angles, and time transformations.  

Inpop is primarily designed for **astrometry, celestial mechanics, and spacecraft navigation software**, providing direct access to the underlying ephemeris data rather than high-level astronomical reductions.

---

## Downloading INPOP files

INPOP `.dat` files can be downloaded from the IMCCE FTP server:  
[https://ftp.imcce.fr/pub/ephem/planets/](https://ftp.imcce.fr/pub/ephem/planets/)

If the file is not found locally, the Inpop library will attempt to download it automatically to a writable directory.

---

## Requirements

- Python 3.8+  
- [NumPy](https://numpy.org/)  
- [Numba](https://numba.pydata.org/) (optional, for just-in-time acceleration when loading data into memory)  

---

## Provides

The package provides a single class, `Inpop`. It can compute:

- Planetary positions (AU)  
- Velocities (AU/day)  
- Lunar libration angles (rad)  
- Time transformations (TT–TDB, TCG–TCB)  
- First-order derivatives of all above quantities  

---

## Quickstart

```python
from inpop import Inpop

# Open a small INPOP file (auto-download if not present)
inpop = Inpop("inpop21a_TDB_m100_p100_tt.dat")

# Inspect available time range and timescale
print(inpop.jd_beg, inpop.jd_end)  # Julian dates
print(inpop.timescale)             # TDB or TCB

# Compute state vector: moon relative to earth at J2000.0
pv = inpop.PV(2451545.0, 'moon', 'earth')
print(pv)
```

---

## How Inpop works

INPOP files contain Chebyshev coefficients for each body over fixed intervals. Inpop evaluates these polynomials to compute positions, velocities, and time corrections.

```
INPOP .dat file
       │
       │  Chebyshev coefficients
       ▼
+-----------------------+
|    Inpop library      |
|                       |
|  • file reader        |
|  • Chebyshev eval     |
|  • time transforms    |
+-----------------------+
       │
       ▼
Computed quantities

  • planetary position (AU)
  • velocity (AU/day)
  • lunar libration angles (rad)
  • TT–TDB or TCG–TCB time offsets
```

Workflow:

```
Julian date
     │
     ▼
select Chebyshev segment
     │
     ▼
evaluate polynomial series
     │
     ▼
state vector (position, velocity)
```

---

## Accuracy and verification

- Double-precision arithmetic  
- Numerical errors: ~2e-14 AU for positions, sub-microsecond for time over 200 years  
- For sub-millisecond timing, pass Julian dates as `[day, fraction]` for maximum precision  

---

## Core methods

### `PV(jd, target, center, rate=True, ts=None)`

Compute the state vector of a solar system body (`target`) relative to another (`center`) at time `jd`.  

- Returns `[P, V]` if `rate=True`, or position vector `P` if `rate=False`.  
- `target` and `center` can be integers 0–12 or strings:  

```
 0 mercury
 1 venus
 2 earth
 3 mars
 4 jupiter
 5 saturn
 6 uranus
 7 neptune
 8 pluto
 9 moon
10 sun
11 ssb
12 emb
```

- Optional `ts` keyword: `"TDB"` or `"TCB"` to force timescale conversion.

### `LBR(jd, rate=True, ts=None)`

Compute the **physical libration angles** of the moon. Returns angles in radians and optionally angular velocities.

### `TTmTDB(tt_jd, rate=False)`

Time difference between **TT and TDB** in seconds, evaluated at `tt_jd`.

### `TCGmTCB(tcg_jd, rate=False)`

Time difference between **TCB and TCG** in seconds.

### `close()`, `__del__()`

Close the INPOP file safely.

---

## Constants

Access key constants directly:

```python
print(inpop.AU, inpop.EMRAT)
print(inpop.constants)  # dictionary of up to 400 ephemeris constants
```

Units are automatically converted to AU, AU/day, seconds, and radians.

---

## Citation

Please cite INPOP if using it in scientific work:

Fienga, A., et al. (2019). INPOP planetary ephemerides. *Celestial Mechanics and Dynamical Astronomy*, 130(12). https://doi.org/10.1007/s10569-019-09921-2  

INPOP ephemerides are produced by the "Institut de mécanique céleste et de calcul des éphémérides" (IMCCE).

---

## Author

Marcel Hesselberth

- GitHub: [https://github.com/hesselberth/Inpop](https://github.com/hesselberth/Inpop)
- Pypi: [https://pypi.org/project/Inpop/]
- Bugs / issues: [GitHub issues](https://github.com/hesselberth/Inpop/issues)  

---

## License

- GPLv3: [https://www.gnu.org/licenses/gpl-3.0.txt](https://www.gnu.org/licenses/gpl-3.0.txt)  
- Free software: no warranty.

