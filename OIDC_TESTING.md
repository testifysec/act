# OIDC Testing Guide

## Running OIDC Tests Locally

This guide explains how to test the OIDC implementation without Docker Hub authentication.

### Prerequisites

- Build the act binary: `go build -o act`
- Ensure Docker is running

### Running the Example Workflow

Use the `node:20-slim` image to avoid Docker Hub authentication issues:

```bash
./act -W .github/workflows/example-oidc.yml -j test-oidc \
  -P ubuntu-latest=node:20-slim \
  --artifact-server-path /tmp/act-artifacts
```

### What Gets Tested

The OIDC test workflow verifies:

1. **Environment Variables**: Confirms `ACTIONS_ID_TOKEN_REQUEST_URL` and `ACTIONS_ID_TOKEN_REQUEST_TOKEN` are set
2. **Token Request**: Obtains an OIDC ID token from the local act server
3. **Token Claims**: Validates all 31 GitHub-compliant claims are present

### Expected Output

Successful test output shows:

```
✅ OIDC token endpoints enabled
✅ Successfully obtained OIDC token
✅ Token claims decoded and displayed
✅ OIDC integration test passed!
```

### Alternative Images

If `node:20-slim` isn't suitable, you can use:

- `node:20-alpine`
- `debian:bookworm-slim`
- `ubuntu:22.04`

All of these are freely available without Docker Hub authentication.

### Troubleshooting

**Issue**: Docker authentication error with `catthehacker/ubuntu:act-latest`

**Solution**: Use the `-P` flag with a freely available image as shown above.

**Issue**: M-series Mac architecture warning

**Solution**: Add `--container-architecture linux/amd64` to the command.

### OIDC Token Claims

The test validates all 31 claims match GitHub's official OIDC schema:

- Standard JWT: `iss`, `sub`, `aud`, `exp`, `nbf`, `iat`, `jti`
- Workflow: `workflow`, `workflow_ref`, `workflow_sha`, `job_workflow_ref`, `job_workflow_sha`
- Repository: `repository`, `repository_id`, `repository_owner`, `repository_owner_id`, `repository_visibility`
- Execution: `run_id`, `run_number`, `run_attempt`, `event_name`, `ref`, `ref_type`, `ref_protected`
- Actor: `actor`, `actor_id`
- Branches: `head_ref`, `base_ref`
- Infrastructure: `runner_environment`, `sha`
