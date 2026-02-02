# Atomic-Ledger-Py

An immutable transaction ledger with double-entry accounting for Python applications.

## Overview

Atomic-Ledger-Py provides a robust, immutable ledger implementation following double-entry accounting principles. It ensures financial data integrity through cryptographic verification and atomic transactions.

## Features

- **Double-Entry Accounting**: Every transaction has balanced debits and credits
- **Immutability**: Append-only ledger with hash chain verification
- **Atomic Transactions**: All-or-nothing commit semantics
- **Audit Trail**: Complete transaction history with timestamps

## Installation

Install from source using uv:

```bash
uv pip install git+https://github.com/vindicta-platform/Atomic-Ledger-Py.git
```

Or clone and install locally:

```bash
git clone https://github.com/vindicta-platform/Atomic-Ledger-Py.git
cd Atomic-Ledger-Py
uv pip install -e .
```

## Quick Start

```python
from atomic_ledger import Ledger, Transaction

ledger = Ledger()

# Create a transfer
tx = Transaction()
tx.debit("user:alice", 100.00)
tx.credit("user:bob", 100.00)
ledger.commit(tx)

# Query balances
print(ledger.balance("user:alice"))  # -100.00
print(ledger.balance("user:bob"))    # +100.00
```

## Use Cases

- **Gas Tank Economy**: Track platform credit consumption
- **In-Game Currency**: Manage virtual economies
- **Audit Compliance**: Financial record keeping

## Related Repositories

| Repository | Relationship |
|------------|-------------|
| [platform-core](https://github.com/vindicta-platform/platform-core) | Parent platform |
| [Metered-SaaS-Logic](https://github.com/vindicta-platform/Metered-SaaS-Logic) | Usage billing |

## License

MIT License - See [LICENSE](./LICENSE) for details.
