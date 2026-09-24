# Release Notes

## [v1.0.0](https://github.com/Anant3008/ci-artifacts-lab/releases/tag/v1.0.0) - 2026-08-29 (deploy [#33238709694](https://github.com/Anant3008/ci-artifacts-lab/actions/runs/33238709694))

### Change Scope
- **Added**: Automated artifact packaging pipeline generating `release-package.zip`, `build-output`, and `test-results` ([commit 3bdef9d](https://github.com/Anant3008/ci-artifacts-lab/commit/3bdef9d885ee94954004ad7381a8ccad64de5ffa))
- **Fixed**: Artifact upload path in `build` job corrected from `./dist/` to `./build/` with strict validation `if-no-files-found: error` ([commit 5553127](https://github.com/Anant3008/ci-artifacts-lab/commit/5553127c4b7ee554a2613633f89f893fbb21068f))
- **Fixed**: Race condition between `build` and `test` jobs by adding sequential `needs: [build]` dependency ([commit 5553127](https://github.com/Anant3008/ci-artifacts-lab/commit/5553127c4b7ee554a2613633f89f893fbb21068f))
- **Fixed**: Test report upload silently skipped on test failures by adding `if: always()` condition to `test` job ([commit 5553127](https://github.com/Anant3008/ci-artifacts-lab/commit/5553127c4b7ee554a2613633f89f893fbb21068f))
- **Changed**: CI configuration in `.github/workflows/ci.yml` updated with strict artifact validation across Build, Test, and Package jobs ([commit 5553127](https://github.com/Anant3008/ci-artifacts-lab/commit/5553127c4b7ee554a2613633f89f893fbb21068f))

### Validation
- **CI Build**: [Run #33238709694](https://github.com/Anant3008/ci-artifacts-lab/actions/runs/33238709694) **PASS** (5 tests: `/health` 200, `/health` status ok, `/` 200, `/version` 200, `/version` field)
  - Build Job: [99064191919](https://github.com/Anant3008/ci-artifacts-lab/actions/runs/33238709694/job/99064191919) (15s) - Uploaded `build-output` (app.js, manifest.json)
  - Test Job: [99064221826](https://github.com/Anant3008/ci-artifacts-lab/actions/runs/33238709694/job/99064221826) (18s) - Uploaded `test-results` (junit.xml)
  - Package Job: [99064259028](https://github.com/Anant3008/ci-artifacts-lab/actions/runs/33238709694/job/99064259028) (13s) - Uploaded `release-package` (release-package.zip)
- **Staging / Smoke Check**: **PASS** (Verified `./build/manifest.json` build timestamp `2026-08-29T06:32:41.853Z` and unzipped `release-package.zip`)
- **Manual QA**: Inspected `junit.xml` test report (5/5 passed, 0 failures, 0 errors in 0.5s); verified local test execution passes 100%

### Risks
- **Runner Runtime Compatibility**: Actions target Node 20 runtime (`actions/checkout@v4`, `actions/setup-node@v4`, `actions/upload-artifact@v4`) and were forced to Node 24 by GitHub runners. Monitor for future runner deprecation policies.
- **Pipeline Execution Latency**: Adding `needs: [build]` sequential dependency increased total pipeline runtime to 54s. Monitor queue times under higher concurrent load.

### Rollback
- **Target**: [v0.9.0](https://github.com/Anant3008/ci-artifacts-lab/tree/v0.9.0) (commit [3bdef9d](https://github.com/Anant3008/ci-artifacts-lab/commit/3bdef9d885ee94954004ad7381a8ccad64de5ffa))
- **Steps**:
  1. Revert deployment to previous tag:
     ```bash
     git checkout v0.9.0
     # or git revert 5553127 -m "Rollback v1.0.0"
     ```
  2. Redeploy previous release package or trigger workflow on v0.9.0 base:
     ```bash
     npm ci && npm run build && npm test
     ```
  3. Verify service recovery:
     ```bash
     curl -f http://localhost:3000/health
     ```
