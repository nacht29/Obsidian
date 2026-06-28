# Session: API Caching & Auth Token Issue
**Date:** 2026-06-28  
**Duration:** ~2 hours  
**Status:** Partial resolution with workaround

## Problems Found

1. **Caching Issue in API**
   - API caching layer was returning stale or inconsistent responses
   - Root cause investigation needed more time than available

2. **Session Token Storage Inconsistency**
   - Tokens being stored in multiple ways across the system
   - Middleware and auth handlers not aligned on storage format/location
   - Causes unpredictable token validation failures

## Solutions Implemented

- **Middleware Workaround:** Added token normalization layer in middleware to handle multiple token formats during request processing
- Allows system to function despite underlying inconsistency
- Buys time for proper fix without breaking current deployments

## Key Insights

- Caching issue is separate from auth token problem but both stress-test the middleware
- Quick workaround is stable but masking the real problem
- Token storage inconsistency is more critical than initially thought—affects multiple systems

## Deferred Work

- **Full auth system rewrite** — scope too large for this session, needs dedicated time
- Better token storage standardization
- Audit all places tokens are created/stored/validated
- Remove middleware workaround once tokens are properly normalized at source

## Next Session

1. Start with auth system audit (map all token flows)
2. Design consistent token storage contract
3. Plan rewrite with minimal downtime
4. Consider feature gate for new token handling during rollout

## Notes

- Current workaround is safe for production but should not be permanent
- Caching issue may need separate investigation path
- Token inconsistency likely explains several "random" auth failures in production
