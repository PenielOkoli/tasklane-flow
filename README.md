# Tasklane Flow

FlexiSAF Deliverable 11: performance optimization of Tasklane’s AI review history.

Continues Tasklane Guard f39733f713fac1bc0bdf0948a2f5263c5ec2116d in its own repository and Vercel project. Baseline measurements and optimization work are in progress. See [the plan](docs/modules/11/plan.md).

## Run

`npm ci`, then `npm run dev`. Use Node 22. Configure server-only GROQ_API_KEY and SESSION_SECRET (at least 32 random characters). The isolated synthetic performance scenario at `/evaluation?perf=1` needs neither secret and never calls an AI provider.

Inherited security evidence in docs/modules/10 applies to the original Guard deployment, not this new project.
