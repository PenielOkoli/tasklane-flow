# Performance report — Tasklane Flow

## Outcome

Tasklane's AI feedback review page previously mounted every saved answer and serialized full structured model responses inside closed details. At the supported 100-record limit, this created 100 hidden JSON blocks (1,575,400 characters) and repeated that work on review edits.

The candidate mounts ten records per page, creates a response's JSON only when opened, and loads the grading/schema code only when a comparison is requested. All records remain reachable, editable and exportable. This targets long **AI answer review histories**, not a token-streaming chat. Model latency, context-window cost and network streaming have not been optimized or claimed.

## Measured results

| Metric | Before | After | Interpretation |
|---|---:|---:|---|
| State update → React layout effect, median of 15 review edits | 10.4 ms | 2.3 ms | 77.9% less measured rendering time; not next-paint latency |
| Mounted answer-history rows | 100 | 10 | 90% fewer, all 100 still reachable |
| Closed-response JSON characters in DOM | 1,575,400 | 0 | Full payload retained in memory; formatting deferred |
| Initial review-route JavaScript, uncompressed | 704,634 B | 600,655 B | 14.8% reduction, including framework/runtime |
| Same scripts, gzip | 215,540 B | 186,207 B | 13.6% reduction |
| Lab LCP, median of three navigations | 544 ms | 360 ms | 33.8% lower in this small sample; first runs 1,176 / 1,200 ms |
| Lab CLS, median of three navigations | 0.01076 | 0.01076 | Unchanged |
| Observed provisional INP | 1,016 ms in 2/3 captures | Missing in all three immediate captures; later 4,192 ms | **No improvement established; responsiveness gate unresolved** |

INP is not replaced by the React timing above. A later candidate capture reported 4,192 ms after additional interactions; it is preserved in `late-inp-observation.json`. The browser reported visible documents, but event reporting/paint timing did not line up with the short React work. The cause is not proven. These readings are concerning and require a controlled foreground-device investigation; they do not justify a good-INP claim. This submission demonstrates measured rendering and bundle gains, not that all production performance gates pass.

## Method and source versions

- Product foundation: [Tasklane Guard f39733f](https://github.com/PenielOkoli/tasklane-guard/commit/f39733f713fac1bc0bdf0948a2f5263c5ec2116d).
- Comparable baseline: [2871177](https://github.com/PenielOkoli/tasklane-flow/commit/2871177407d65fe496b106acf588e334e9db666f), before optimizations. Earlier measurement pilots are retained, not pooled with final results.
- Final candidate: this repository's optimized source; `browser-candidate.json`, `bundle-candidate.json` and deployment evidence identify the tested results.
- Local **production builds**, Next.js 16.4.2, React 19.3.1, Node 22.14.0, Windows, Chrome 157.0.0.0, viewport 1024 × 577. No CPU/network throttling; first run then two warm reloads, same machine and viewport. Results are small-sample local lab readings, not 28-day field p75 or a mobile network benchmark.
- Explicit `/evaluation?perf=1` mode uses 100 deterministic model-shaped saved responses, about 15.8 KB of formatted JSON each, under the existing 100-feedback-record limit. No model calls, storage writes, or replacement of user feedback. A separate live provider smoke test checks real answers.
- Five “Review next sample” clicks per navigation. `performance.now()` starts in the handler and ends in `useLayoutEffect` after the updated history commits. Browser Web Vitals are collected by the standard `web-vitals` library. Raw readings include missing values rather than treating missing INP as zero.
- Early double-animation-frame timing was contaminated by ~1-second frame waits. `discarded-raf-pilot.json` is excluded. Early measurement controls inserted on hydration caused CLS around 0.19. The same server-reserved measurement layout was applied to both versions; `pilot-*-before-layout-fix.json` is excluded. This harness correction is not counted as a product optimization.
- Script sizes come from scripts referenced by the production `/evaluation` response, resolved to built files. Gzip uses Node's default compression and is an estimate of compression, not Vercel transfer size. Dynamically requested chunks are excluded from *initial* size; loading the grader later still costs bytes.

## Each optimization and its evidence

1. **Lazy grading/schema dependency.** Split small catalog/types/release rules from Zod validation. `import('@/lib/evaluation')` happens inside the guarded comparison action. The intermediate build measured 704,689 → 596,390 raw bytes and 215,563 → 184,495 gzip bytes (`bundle-lazy-grader.json`, prior to the identical harness correction). Tests still exercise the exact grading function. Validation is deferred, not removed.
2. **Bounded history and lazy review UI.** Ten rows per page, accessible page selection and previous/next controls. Before/after mounted rows: 100 → 10; total DOM elements: see raw captures. This works on real saved feedback as well as the fixture; edits and exports operate on the complete list. The feedback component loads when its tab is opened.
3. **Deferred structured-response rendering.** Before: 100 JSON blocks / 1,575,400 characters, even closed. After: zero closed blocks; opening one response creates one 15,754-character block and closing removes it. The browser check and a serialization-spy test verify the boundary. Review update timing measures the combined benefit of 2 and 3; no isolated timing contribution is invented.

No raster images are used by this interface, so no image-size saving is claimed. Existing local fonts and CSS remain; the meaningful heavy assets here were JavaScript and structured AI response payloads. Persistence still loads the bounded history into memory; this is not server pagination or constant-memory data storage.

## Reproduce

Use Node 22, `npm ci`, `npm run build`, then `node node_modules/next/dist/bin/next start -p 3011`. Open `/evaluation?perf=1`, wait for 100 sample answers, click “Review next sample” five times and capture measurements. Repeat three navigations. `node scripts/measure-bundle.mjs candidate` records the initial production script list from port 3011. For baseline, check out 2871177 in a separate directory, rebuild and use the same procedure with the `baseline` argument. Never compare development mode against production.

For a stable INP investigation, use a foreground browser on representative mobile/desktop hardware, wait for Web Vitals reporting to settle, inspect Event Timing input/processing/presentation delays, and collect enough real sessions to assess p75. No telemetry is sent to a third party by the measurement panel. Measurements remain in memory until manually copied.

## Performance gates

- Pass: history mounts at most ten rows; unopened response JSON is absent; later-page data remains reachable and editable; grader stays out of initial review scripts; all regression tests pass; provider/security smoke checks pass when configured.
- Target (not a demonstrated field pass): p75 LCP ≤2.5 s, INP ≤200 ms and CLS ≤0.1 on representative users. Current INP evidence does **not** pass this gate. No field audience/CrUX dataset exists for this new deployment.
- Inherited limitation: abuse counters are process-local, not distributed production quotas. This milestone does not remove that boundary.

## References

[Web Vitals and field/lab interpretation](https://web.dev/articles/vitals), [Next.js lazy loading](https://nextjs.org/docs/app/guides/lazy-loading), [React rendering memoization](https://react.dev/reference/react/memo). Installed Next.js documentation was consulted for client boundaries and dynamic imports.
