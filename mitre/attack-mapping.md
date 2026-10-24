# MITRE ATT&CK Mapping

## T1110 — Brute Force

The repeated Windows failed-logon detection is mapped to **MITRE ATT&CK T1110 — Brute Force** when the observed pattern is consistent with credential-guessing behavior.

The technique has four sub-techniques:

- T1110.001 — Password Guessing
- T1110.002 — Password Cracking
- T1110.003 — Password Spraying
- T1110.004 — Credential Stuffing

The Home SOC Lab does **not** automatically assign a sub-technique from Event ID 4625 alone. Additional evidence is required to distinguish the behavior.

## Evidence-to-Mapping Logic

| Evidence | Interpretation |
|---|---|
| Repeated 4625 failures | Authentication failure pattern |
| Same account repeatedly targeted | Could support password guessing against one account |
| Many accounts targeted from one source | Could support password spraying |
| Known reused credential set | Could support credential stuffing |
| Offline hash cracking evidence | Could support password cracking |

## Analyst Principle

MITRE ATT&CK mapping is an analytical classification, not proof of attacker intent. The investigation should document the observed evidence and the confidence of the mapping.
