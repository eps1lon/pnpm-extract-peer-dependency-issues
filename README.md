#

`pnpm.peerDependencyRules` is only considered in the default reporter.

`--reporter ndjson` ignores them.

The idea was to start tracking peer dependency issues via `pnpm.peerDependencyRules` and then fail CI if new warnings are introduced.

We'd have to parse the default output for ` WARN  Issues with peer dependencies found` which is considerable more brittle.

`pnpm --strict-peer-dependencies true` is not suitable since it ignores `pnpm.peerDependencyRules`.

## Actual behavior

```bash
$ pnpm install --resolution-only
 WARN  Issues with peer dependencies found
.
├─┬ react-dom 18.3.1
│ └── ✕ missing peer react@^18.3.1
└─┬ ts-node 10.9.2
  └── ✕ missing peer @types/node@"*"

$ pnpm install --resolution-only  --reporter ndjson
...
{
  "time": 1720535261322,
  "hostname": "Sebastians-MBP.fritz.box",
  "pid": 30195,
  "level": "debug",
  "name": "pnpm:peer-dependency-issues",
  "issuesByProjects": {
    ".": {
      "bad": {},
      "missing": {
        "react": [
          {
            "parents": [{ "name": "react-dom", "version": "18.3.1" }],
            "optional": false,
            "wantedRange": "^18.3.1"
          }
        ],
        "@swc/core": [
          {
            "parents": [{ "name": "ts-node", "version": "10.9.2" }],
            "optional": true,
            "wantedRange": ">=1.2.50"
          }
        ],
        "@swc/wasm": [
          {
            "parents": [{ "name": "ts-node", "version": "10.9.2" }],
            "optional": true,
            "wantedRange": ">=1.2.50"
          }
        ],
        "@types/node": [
          {
            "parents": [{ "name": "ts-node", "version": "10.9.2" }],
            "optional": false,
            "wantedRange": "*"
          }
        ],
        "typescript": [
          {
            "parents": [{ "name": "ts-node", "version": "10.9.2" }],
            "optional": false,
            "wantedRange": ">=2.7"
          }
        ]
      },
      "conflicts": [],
      "intersections": {
        "react": "^18.3.1",
        "@types/node": "*",
        "typescript": ">=2.7"
      }
    }
  }
}
...

```

## Expected

`--reporter ndjson` considers `pnpm.peerDependencyRules`
