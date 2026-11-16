# Verification record

## Source and deployment

- Foundation: Tasklane Guard `f39733f713fac1bc0bdf0948a2f5263c5ec2116d`.
- Comparable baseline: `2871177407d65fe496b106acf588e334e9db666f`.
- Measured optimized implementation: `a32a8da12fb7e68a3d095333c39427a36a816a93`.
- Final application commit: `b72c494a27a998a057daa0cf2117f69b2843789a` (mobile wordmark CSS adjustment only).
- GitHub-connected Production deployment verified Ready: https://tasklane-flow-f1jbhwsjr-penielokolis-projects.vercel.app
- Public alias: https://tasklane-flow.vercel.app
- Subsequent evidence commit adds documentation, screenshot and a working verification script; no application behavior changes.

## Results

On 15 November 2026, production builds and TypeScript checks passed. All **65 tests passed** (61 inherited, four new history/serialization tests). Three before/after browser runs are retained. Exact measurements, failed/omitted metrics and excluded pilots are explained in [performance-report.md](performance-report.md).

The live HTTP checks on 15 November passed four groups: eight published routes/security headers; protected session/provider configuration; one real grounded Groq answer; and injection/sanitization boundaries. See `live-verification.json`. This smoke test is distinct from the synthetic performance workload.

On 16 November, the deployed normal evaluation workflow loaded the deferred grading code and completed all 16 retrieval checks: inherited baseline 5/8, candidate 8/8. This confirms functional continuity, not new answer-quality gains. AI consent was off for that browser comparison; release status correctly remains Hold pending AI evidence and human review. See `live-evaluation-browser.json`.

The live isolated performance page mounted exactly ten answer rows and zero closed JSON blocks. Local browser checks reached answers 91–100, changed answer 100 to Reviewed, navigated away/back, and retained the status. Opening its response mounted one 15,754-character JSON block; closing removed it. Mobile home check at 390 × 844 had document width 375 (no horizontal overflow); the final CSS also prevents the wordmark wrapping. `live-workspace.png` shows the deployed home page.

## Limits and release judgment

Rendering and byte reductions are established. **INP is unresolved and the field performance gate is not passed.** A delayed candidate INP observation was 4,192 ms; it is retained, not dropped. No claim is made of field p75, representative mobile throttling, faster model generation, or streaming improvements. A real-device Event Timing investigation remains necessary before claiming consistently responsive production interactions.

Secrets were configured separately for this Vercel project. Secret values are not included in evidence. Inherited process-local abuse counters, browser-local history and external provider/platform retention limits remain.
