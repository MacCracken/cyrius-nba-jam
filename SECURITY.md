# Security Policy

**Email**: security@agnos.io

## Considerations

- Asset loading (sprites, sound) must validate file headers and sizes — no unbounded allocations from untrusted data
- Input handling must not crash on malformed gamepad events
- Save data loading must be bounds-checked

**Last Updated**: 2026-04-14
