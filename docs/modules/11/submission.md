# Submission 11

- Repository: https://github.com/PenielOkoli/tasklane-flow
- Live app: https://tasklane-flow.vercel.app
- Report: [performance-report.md](performance-report.md)
- Isolated workload: https://tasklane-flow.vercel.app/evaluation?perf=1

## Walkthrough
1. Open the live home page, then “Explore sample history”. This does not change saved data or call an AI model.
2. Observe ten of 100 answers. Navigate to page 10; change answer 100 to Reviewed, leave and return to confirm the edit.
3. Expand a response. Full JSON appears only while that response is open.
4. Capture measurements after five sample-review edits. Compare with committed before/after evidence and read the INP caveat.
5. Open Knowledge for a real grounded answer, save feedback, then review it in Evaluations. The ordinary workspace is separate from the sample session.

Do not represent the React commit measurement as INP, the fixture as actual model generation, or this small lab sample as field p75. INP remains an unresolved performance gate, explicitly recorded in the report.
