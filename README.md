# Qiskit IonQ Provider

<img src="https://ionq.com/images/ionq-logo-dark.png" alt="IonQ Logo" width="350px"/>

[![License](https://img.shields.io/github/license/qiskit-community/qiskit-aqt-provider.svg?style=popout-square)](https://opensource.org/licenses/Apache-2.0)

**Qiskit** is an open-source SDK for working with quantum computers at the level of circuits, algorithms, and application modules.

This project contains a provider that allows access to **[IonQ]** ion trap quantum
systems.

The example python notebook (in `/example`) should help you understand basic usage.

## API Access

The IonQ Provider uses IonQ's REST API, and using the provider requires an API access token from IonQ. If you would like to use IonQ as a Qiskit provider, please contact sales@ionq.co to request more information about gaining access to the IonQ API.

## Installation

> :warning: **The Qiskit IonQ Provider is not compatible `qiskit<=0.24.0`**:
> To ensure you are on the latest version, run:
>
> ```bash
> pip install -U "qiskit>=0.25.0"
> ```

You can install the provider using pip:

```bash
pip install qiskit-ionq
```

## Provider Setup

To instantiate the provider, make sure you have an access token then create a provider:

```python
from qiskit_ionq import IonQProvider

provider = IonQProvider("token")
```

### Credential Environment Variables

Alternatively, the IonQ Provider can discover your access token from environment variables:

```bash
export QISKIT_IONQ_API_TOKEN="token"
```

Then invoke instantiate the provider without any arguments:

```python
from qiskit_ionq import IonQProvider

provider = IonQProvider()
```

Once the provider has been instantiated, it may be used to access supported backends:

```python
# Show all current supported backends:
print(provider.backends())

# Get IonQ's simulator backend:
simulator_backend = provider.get_backend("ionq_simulator")
```

### Submitting a Circuit

Once a a backend has been specified, it may be used to submit circuits.
For example, running a Bell State:

```python
from qiskit import QuantumCircuit

# Create a basic Bell State circuit:
qc = QuantumCircuit(2, 2)
qc.h(0)
qc.cx(0, 1)
qc.measure([0, 1], [0, 1])

# Run the circuit on IonQ's platform:
job = simulator_backend.run(qc)

# Print the results.
print(job.get_counts())

# The simulator specifically provides the the ideal probabilities and creates
# counts by sampling from these probabilities. The raw probabilities are also accessible:
print(job.get_probabilities())
```

### Basis gates and transpilation

The IonQ provider provides access to the full IonQ Cloud backend, which includes its own transpilation and compilation pipeline. As such, IonQ provider backends have a broad set of "basis gates" that they will accept — effectively anything the IonQ API will accept. They are `ccx, ch, cnot, cp, crx, cry, crz, csx, cx, cy, cz, h, i, id, mcp, mct, mcx, measure, p, rx, rxx, ry, ryy, rz, rzz, s, sdg, swap, sx, sxdg, t, tdg, toffoli, x, y` and `z`.

If you have circuits that you'd like to run on IonQ backends that use other gates than this (`u` or `iswap` for example), you will either need to manually rewrite the circuit to only use the above list, or use the Qiskit transpiler, per the example below. Please note that not all circuits can be automatically transpiled.

If you'd like lower-level access—the ability to program in native gates and skip our compilation/transpilation pipeline—please reach out to your IonQ contact for further information.

```python
from qiskit import QuantumCircuit, transpile
from math import pi

qc2 = QuantumCircuit(1, 1)
qc2.u(pi, pi/2, pi/4, 0)
qc2.measure(0,0)
transpiled_circuit = transpile(qc2, simulator_backend)
```

## Contributing

If you'd like to contribute to the IonQ Provider, please take a look at the [contribution guidelines](CONTRIBUTING.md). This project adheres the Qiskit Community code of conduct. By participating, you are are agreeing to uphold this code.

If you have an enhancement request or bug report, we encourage you to open an issue in [this repo's issues tracker](https://github.com/qiskit-partners/qiskit-ionq/issues). If you have a support question or general discussion topic, we recommend instead asking on the [Qiskit community slack](https://qiskit.slack.com/) (you can join using [this link](https://ibm.co/joinqiskitslack)) or the [Quantum Computing StackExchange](https://quantumcomputing.stackexchange.com/questions/tagged/qiskit).

## Running Tests

This package uses the [pytest](https://docs.pytest.org/en/stable/) test runner.

To use pytest directly, just run:

```bash
pytest [pytest-args]
```

Alternatively, you may use the setuptools integration by running tests through `setup.py`, e.g.:

```bash
python setup.py test --addopts="[pytest-args]"
```

### Fixtures

Global pytest fixtures for the test suite can be found in the top-level [test/conftest.py](./test/conftest.py) file.

## Documentation

To build the API reference and quickstart docs, run:

```bash
pip install -r requirements-docs.txt
make html
open build/html/index.html
```

## License

[Apache License 2.0].

The IonQ logo and Q mark are copyright IonQ, Inc. All rights reserved.

[ionq]: https://www.ionq.com/
[apache license 2.0]: https://github.com/qiskit-partners/qiskit-ionq/blob/master/LICENSE.txt
