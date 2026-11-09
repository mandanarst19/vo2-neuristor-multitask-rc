# VO₂ neuristor model (`model.py`)

All simulation scripts import `model.py`, the VO₂ thermal-neuristor model (classes `VO2`, `Circuit`, `Circuit2D`) of

> Y.-H. Zhang *et al.*, *Nat. Commun.* **15**, 6986 (2024).

`model.py` is not redistributed here. Download it from the code repository given in the Code availability statement of that paper, and add it to the Kaggle notebook inputs (or to this folder when running locally).

The scripts use `model.py` unchanged. Every script checks that `model.VO2.reversal` is the original method. Changes made for this work are applied from outside, inside each script:

- **F1:** the hysteresis state is reset before every simulation (`reset_vo2`).
- **F2:** the capacitance scaling sets `C0` and `R0C0` together.
- **Coupling:** in the coupling control and sweep, only the neighbour term `S_couple` is set by hand. The substrate term `S_env = 0.92 S_th` is kept fixed.
