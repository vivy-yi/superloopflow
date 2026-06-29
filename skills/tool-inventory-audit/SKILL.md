---
name: tool-inventory-audit
description: How to audit and maintain .tools-inventory.yaml. Use when adding/upgrading agentic tools or detecting tool version drift.
---

# Tool Inventory Audit

## When to Use
- Adding a new agentic tool
- Upgrading an existing tool
- Detecting tool version drift
- Onboarding a new contributor

## Process
1. Run `/omc:omc-doctor` and `/ecc:harness-audit`
2. Compare detected versions to `.tools-inventory.yaml`
3. Update if drift detected
4. Run `make preflight` to verify
5. Commit inventory update

## Self-Evaluation
- [ ] All installed tools in inventory?
- [ ] Versions match reality?
- [ ] Fallback strategies documented?
