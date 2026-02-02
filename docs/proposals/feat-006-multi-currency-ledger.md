# Feature Proposal: Multi-Currency Ledger Support

**Proposal ID**: FEAT-006  
**Author**: Unified Product Architect (Autonomous)  
**Created**: 2026-02-01  
**Status**: Draft  
**Priority**: High  
**Target Repository**: Atomic-Ledger-Py  

---

## Part A: Software Design Document (SDD)

### 1. Executive Summary

Extend Atomic-Ledger-Py to support multiple currency types (Gas Credits, Victory Points, Tournament Tokens) with cross-currency exchange and consolidated balance reporting.

### 2. System Architecture

#### 2.1 Current State
- Single-currency ledger
- Basic debit/credit operations
- No currency conversion

#### 2.2 Proposed Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                 Multi-Currency Ledger                           │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                   Currency Registry                     │    │
│  │   gas_credits | victory_points | tournament_tokens      │    │
│  └─────────────────────────────────────────────────────────┘    │
│                              │                                  │
│              ┌───────────────┼───────────────┐                  │
│              ▼               ▼               ▼                  │
│  ┌────────────────┐ ┌────────────────┐ ┌────────────────┐       │
│  │   Gas Ledger   │ │   VP Ledger    │ │  Token Ledger  │       │
│  │  (Consumable)  │ │  (Earneable)   │ │ (Transferable) │       │
│  └────────────────┘ └────────────────┘ └────────────────┘       │
│                              │                                  │
│                              ▼                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │                   Exchange Engine                       │    │
│  │   Define rates: 10 VP = 1 Gas Credit                    │    │
│  └─────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
```

#### 2.3 File Changes

```
Atomic-Ledger-Py/
├── src/
│   └── atomic_ledger/
│       ├── currency.py          [NEW] Currency definition
│       ├── multi_ledger.py      [NEW] Multi-currency ledger
│       ├── exchange.py          [NEW] Currency exchange
│       ├── ledger.py            [MODIFY] Currency field support
│       └── transaction.py       [MODIFY] Currency in entries
├── tests/
│   ├── test_multi_currency.py   [NEW]
│   └── test_exchange.py         [NEW]
└── docs/
    └── multi-currency.md        [NEW]
```

### 3. Data Model

```python
@dataclass
class Currency:
    code: str              # "GAS", "VP", "TKN"
    name: str              # "Gas Credits"
    decimals: int          # Precision (2 for cents)
    is_transferable: bool  # Can users send to each other?
    is_purchasable: bool   # Can be bought with real money?
    
class MultiLedger:
    """Ledger supporting multiple currencies."""
    
    def balance(self, account: str, currency: str) -> Decimal:
        """Get balance for specific currency."""
        
    def balances(self, account: str) -> dict[str, Decimal]:
        """Get all currency balances."""
        
    def exchange(
        self,
        account: str,
        from_currency: str,
        to_currency: str,
        amount: Decimal
    ) -> Transaction:
        """Convert between currencies at current rate."""
```

### 4. Exchange Rate Management

```python
class ExchangeEngine:
    """Manage currency exchange rates."""
    
    rates: dict[tuple[str, str], Decimal]
    
    def set_rate(self, from_curr: str, to_curr: str, rate: Decimal):
        """Set exchange rate (admin only)."""
        
    def get_rate(self, from_curr: str, to_curr: str) -> Decimal:
        """Get current exchange rate."""
        
    def convert(self, amount: Decimal, from_curr: str, to_curr: str) -> Decimal:
        """Calculate converted amount."""
```

### 5. Security Considerations

- Exchange rates admin-only
- Rate change audit logged
- Atomic exchange transactions
- Overdraft prevention per currency

---

## Part B: Behavior Driven Development (BDD)

### User Stories

#### US-001: Earn Victory Points
**As a** competitive player  
**I want to** earn Victory Points from battles  
**So that** I can track my competitive progress

#### US-002: Convert VP to Gas
**As a** platform user  
**I want to** exchange Victory Points for Gas Credits  
**So that** I can use earned rewards for platform features

#### US-003: View All Balances
**As a** member  
**I want to** see all my currency balances  
**So that** I understand my total platform wealth

### Acceptance Criteria

```gherkin
Feature: Multi-Currency Ledger

  Scenario: Credit victory points after battle
    Given user "alice" has 0 Victory Points
    When the system credits 100 VP for a tournament win
    Then alice's VP balance should be 100
    And the transaction should be recorded in the VP ledger

  Scenario: Exchange currencies
    Given user "bob" has 500 VP and 0 Gas Credits
    And the exchange rate is 10 VP = 1 Gas Credit
    When bob exchanges 100 VP for Gas Credits
    Then bob should have 400 VP
    And bob should have 10 Gas Credits
    And both ledgers should record the transaction

  Scenario: View consolidated balances
    Given user "carol" has balances across multiple currencies
    When she queries her balances
    Then she should see all currencies with amounts
```

---

## Implementation Estimate

| Phase | Effort | Dependencies |
|-------|--------|--------------|
| Currency Registry | 3 hours | None |
| Multi-Ledger | 6 hours | Existing ledger |
| Exchange Engine | 4 hours | None |
| Testing | 4 hours | None |
| **Total** | **17 hours** | |
