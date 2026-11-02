# Quantum Basics

A collection of Qiskit notebooks exploring reversible quantum arithmetic, modular exponentiation, and circuit-resource trade-offs. The emphasis is on **constructing circuits, checking their outputs, and understanding their logical resource costs**.

The current repository contains two complementary implementations of arithmetic used in quantum order finding and related algorithms. It does not implement the full Shor factoring workflow.

## Notebooks

| Notebook | Focus | Included work |
|---|---|---|
| [`modexp.ipynb`](modexp.ipynb) | Ripple-carry modular exponentiation | SUM/CARRY and MAJ/UMA adders; Qiskit VBE/CDKM adders; modular addition; controlled modular multiplication; modular exponentiation; resource formulas and a 2,048-bit-width extrapolation |
| [`qft.ipynb`](qft.ipynb) | QFT-based modular arithmetic | Phase-based constant and modular addition; modular MAC; modular multiplication; modular exponentiation; `DraperQFTAdder` variant; comparison with ripple-carry circuits |

### Suggested reading order

Start with `modexp.ipynb` to understand arithmetic through reversible carry propagation. Continue with `qft.ipynb` to see an alternative based on Fourier-basis phase operations and to compare the resulting circuits.

## Methods

Both notebooks build circuits with Qiskit and check selected arithmetic examples. Resource analyses decompose circuits to the `u` and `cx` gate basis using `optimization_level=0`, and report gate count, depth, and logical qubit count.

The two notebooks use related but not identical measurement procedures:

- **Ripple-carry:** `modexp.ipynb` contains a full-circuit CDKM benchmark and component-wise resource calculations. The VBE modular-exponentiation depth is component-based and should not be treated as an independently verified full-circuit depth bound.
- **QFT:** `qft.ipynb` measures fully decomposed QFT arithmetic circuits for selected small moduli and compares the results with recorded values from the ripple-carry notebook. The QFT analysis also includes structural upper bounds, which are not themselves measured primitive-gate counts.

### Example benchmark

The notebooks include the following recorded example at $N=15$, base $a=2$, and $n=4$:

| Implementation | `u`/`cx` gates | Depth | Depth method | Logical qubits |
|---|---:|---:|---|---:|
| QFT arithmetic | 19,312 | 12,092 | Fully transpiled circuit | 14 |
| CDKM ripple-carry | 24,576 | 16,244 | Fully transpiled circuit | 23 |
| VBE ripple-carry | 33,536 | 16,444 | Sum of component depths | 25 |

The figures are **recorded notebook outputs**, not results independently reproduced for this repository preparation. The VBE depth is not directly comparable to the full-circuit depth measurements. Gate counts and depths are sensitive to circuit construction, transpiler version, and gate synthesis. These small examples do not establish a hardware advantage or an asymptotic lower bound.

## Run locally

The notebooks require Python, Jupyter, Qiskit, Qiskit Aer, NumPy, and notebook visualization dependencies. Install the packages in a Python environment appropriate for your machine:

```bash
python3 -m pip install jupyter qiskit qiskit-aer numpy matplotlib pylatexenc
```

Then launch Jupyter from the repository directory:

```bash
jupyter lab
```

Open `modexp.ipynb` or `qft.ipynb` and run cells **in order**. Some drawing and transpilation cells can be substantially slower than the arithmetic checks, especially as the register width grows. Library APIs and primitive gate decompositions can differ between Qiskit versions.

## Scope and limitations

- **Educational and computational study:** These notebooks demonstrate and compare known quantum arithmetic constructions. They are not original research publications or claims of new algorithms.
- **Limited verification:** Assertions and simulations cover selected small inputs. They do not establish formal correctness for all parameters.
- **Logical resources only:** The analyses exclude physical-qubit encoding, quantum error correction, connectivity, scheduling, noise, and device-specific gate durations.
- **RSA-2048-width extrapolation:** The numbers in `modexp.ipynb` come from substituting $n=2048$ into arithmetic resource expressions. They are not full Shor-algorithm or hardware-attack estimates.
- **Implementation-dependent comparisons:** Different gate bases, compiler settings, optimization passes, and architectures can change comparative results.

## References

- [IBM Quantum: `CDKMRippleCarryAdder`](https://quantum.cloud.ibm.com/docs/en/api/qiskit/1.0/qiskit.circuit.library.CDKMRippleCarryAdder)
- [IBM Quantum: `VBERippleCarryAdder`](https://quantum.cloud.ibm.com/docs/en/api/qiskit/1.1/qiskit.circuit.library.VBERippleCarryAdder)
- [Häner, Roetteler, and Svore, *Factoring using 2n+2 qubits with Toffoli based modular multiplication*](https://www.microsoft.com/en-us/research/wp-content/uploads/2017/05/1611.07995.pdf)
- [Pham and Svore, *A 2D Nearest-Neighbor Quantum Architecture for Factoring*](https://www.microsoft.com/en-us/research/publication/a-2d-nearest-neighbor-quantum-architecture-for-factoring-2/)

For additional construction details and assumptions, see the Markdown explanations inside each notebook.
