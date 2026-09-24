## v1.0.0 - 2026-08-29  (deploy #33238709694)

### Change Scope
- Added:   Automated artifact packaging pipeline generating release-package.zip, build-output, and test-results (#3bdef9d)
- Fixed:   Artifact upload path corrected from `./dist/` to `./build/` with strict validation (#5553127)
- Fixed:   Race condition between build and test jobs by adding sequential `needs: [build]` dependency (#5553127)
- Fixed:   Test report upload skipped on failure by adding `if: always()` condition (#5553127)
- Changed: Workflow pipeline restructured into strict 3-stage dependency chain (build -> test -> package) in `.github/workflows/ci.yml` (#5553127)

### Validation
- CI build #33238709694 PASS (5 tests: /health 200, /health status ok, / 200, /version 200, /version field)
- Staging / smoke check: PASS (Verified build manifest `./build/manifest.json` and release archive `./release-package.zip`)
- Manual QA: Verified JUnit XML test report (5/5 passed, 0 failures, 0 errors in 0.5s); confirmed `/health` and `/version` endpoints respond with expected JSON payload

### Risks
- GitHub Actions runner deprecation warning: Actions (`actions/checkout@v4`, `actions/setup-node@v4`, `actions/upload-artifact@v4`) target Node 20 but run on Node 24; monitor for breaking runner updates.
- Pipeline execution duration: Sequential dependency (`needs: [build]`) increased pipeline runtime to 54s; monitor queue times under concurrent builds.

### Rollback
- Target: v0.9.0 (commit 3bdef9d)
- Steps:
  1. Check out previous known-good release tag: `git checkout v0.9.0` (or `git revert 5553127`)
  2. Redeploy previous release package or re-run workflow on v0.9.0 base
  3. Verify service health via `curl -f http://localhost:3000/health || npm test`
