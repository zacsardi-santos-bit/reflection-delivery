# Expand Insurance Red Team Plugins to Cover Broader Risk Scenarios

## Description

The current insurance-related red team plugins cover a limited set of scenarios that don't fully reflect the breadth of harms an AI system deployed in insurance contexts could cause. Specifically:

- The coverage discrimination plugin does not detect newer or subtler forms of bias such as geographic redlining, using credit scores as a proxy for protected characteristics, or retaliating against policyholders who file legitimate claims.
- The network provider misinformation plugin is scoped to healthcare networks only, but many insurance products involve vendor networks (auto repair shops, preferred contractors, rental car partners) where misinformation can cause real financial harm.
- There is no plugin to detect unauthorized disclosure of policyholder data — a significant privacy and regulatory risk, since AI systems may be manipulated into sharing sensitive insurance records, claims history, or personal information without proper authorization.

## Expected Behavior

- The coverage discrimination plugin should detect a wider set of discriminatory behaviors including geographic, credit-based, occupational, and claims-retaliation practices, and reference the full set of applicable laws including fair housing and equal credit laws.
- The network misinformation plugin should cover auto insurance vendor networks (body shops, preferred contractors, rental car partners) in addition to healthcare provider networks, and flag guidance that could lead to voided warranties or unexpected costs.
- A new data disclosure plugin should evaluate whether an AI system inappropriately reveals policyholder information, and should reference applicable privacy regulations governing insurance data.
- The insurance suite should be updated with expanded compliance framework coverage and the new plugin should be included in the list of domain-specific plugins.

## Why This Matters

Insurance AI systems face regulatory scrutiny across multiple dimensions — fair lending, privacy, and network adequacy. Without comprehensive red team coverage, bias and privacy violations can go undetected, leading to regulatory penalties, consumer harm, and class action exposure.
