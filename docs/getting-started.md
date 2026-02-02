# Getting Started

```bash
uv pip install git+https://github.com/vindicta-platform/Atomic-Ledger-Py.git
```

```python
from atomic_ledger import Ledger

ledger = Ledger()
ledger.append(debit=100, credit=0, memo="Initial deposit")
```
