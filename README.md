# jmeter-learning-example
Repo to be used to learn basics of Jmeter


# ToDo List 
Composed with assistance from AI based on the course taken - probably not a very good list but will suffice for now.

⸻

0) Project Bootstrapping
	•	Create repo with folders: /plans, /jmx, /data, /correlation, /results, /reports, /bin (helpers)
	•	Add a minimal README.md and a .gitignore for *.jtl, /reports, /results, /bin/ artifacts
	•	Pin JMeter version (e.g., 5.x) in README and store plugin list (use [JMeter Plugins Manager])
	•	Define target SUT (base URL, environments, credentials, rate limits)

⸻

1) Introduction & Ground Rules (§1)
	•	Set clear test goals (what to learn/measure: throughput, response time, error rate)
	•	Define SLIs/SLOs (e.g., p95 < 800 ms, error rate < 1%)
	•	Choose test types: smoke, baseline, load, stress, soak; map to thread groups later
	•	Safety: never load prod without approval; respect robots/ToS; throttle if needed

⸻

2) Recording the Script (§2)
	•	Use HTTP(S) Test Script Recorder with a clean browser profile
	•	Record only necessary flows; disable images/fonts/analytics (Recorder → URL Includes/Excludes)
	•	Name transactions with Transaction Controller names reflecting business steps
	•	Immediately clean up: remove duplicates, static resources, and irrelevant calls

⸻

3) Put Load & Analyze Performance Metrics (§3)
	•	Add Thread Group (or better: Concurrency Thread Group, see §4) with initial low load
	•	Add Listeners only for debugging (View Results Tree locally); for runs, prefer JTL + CLI reports
	•	Define KPIs (latency, connect time, response time, throughput, error %) to collect in report
	•	Store baseline results in /results/baseline-YYYYMMDD-hhmm/

⸻

4) Advanced Thread Group Methods (§4)
	•	Prefer Concurrency/Stepping/Ultimate Thread Groups for realistic ramps
	•	Model Arrival Rate or Concurrent Users aligned to real traffic patterns
	•	Include Warm-up and Cool-down phases
	•	For soak/sojourn tests, schedule long steady states

⸻

5) Sessions & Cookies (§5)
	•	Add HTTP Cookie Manager; set “Clear cookies each iteration” appropriately
	•	If load balancer needs stickiness, document it and configure accordingly
	•	Add HTTP Cache Manager when simulating browsers; otherwise omit

⸻

6) Assertions (§6)
	•	Use Response Assertion for functional correctness (status=200, text/json fields)
	•	Add JSON/XML Assertion where applicable; avoid brittle string-only checks
	•	Keep assertions fast and minimal to reduce overhead

⸻

7) Controllers for Modular Tests (§7)
	•	Structure with Module/Include Controller: /modules/login.jmx, /modules/search.jmx, /modules/checkout.jmx
	•	Use Transaction Controller (Generate parent sample) for each business step
	•	Use Throughput/If/While/Once Only controllers to reflect user logic and probabilities
	•	Keep scenarios data-driven and reusable

⸻

8) Timers (§8)
	•	Add Think Time with Uniform Random / Gaussian Random Timer
	•	Use Constant Throughput Timer only when you must cap RPS; document why
	•	Avoid global timers that unintentionally throttle unrelated samplers

⸻

9) Regular Expressions & Correlation (§9, §12)
	•	Identify dynamic values (tokens, ids) → capture with Regex Extractor or JSON Extractor
	•	Store extractor examples in /correlation/ (input, extractor, output)
	•	Replace hard-coded values with variables; verify with Debug Sampler
	•	Robustness: add Boundary change tolerance (e.g., JSONPath > Regex when possible)

⸻

10) Data-Driven Testing (§10)
	•	Add CSV Data Set Config (UTF-8, “Recycle on EOF” as needed) under Test Plan
	•	Keep data files in /data/; document column meanings in a header comment
	•	Create representative datasets (valid, edge, negative where safe)

⸻

11) Scripting (Beanshell Intro) → Prefer JSR223 Groovy (§11)
	•	Avoid Beanshell in new scripts (slow). Use JSR223 + Groovy with “Cache compiled script”
	•	Keep logic minimal: custom headers, dynamic payloads, conditional branching
	•	Externalize complex logic to helper Groovy files under /bin/ and load via classpath

⸻

12) Handling Dynamic Responses – Advanced (§12)
	•	Chain requests using extracted vars; fail fast if a required var is missing
	•	Validate correlation with assertions; add fallback logs to identify breakpoints
	•	Parameterize headers (auth tokens), cookies, anti-CSRF flows properly

⸻

13) Validations in Non-GUI Mode (§13)
	•	Run tests with CLI:

jmeter -n -t jmx/checkout.jmx -l results/run.jtl -e -o reports/run-report


	•	Keep GUI for design only; automate all runs via CLI
	•	Store jmeter.properties / user.properties overrides in repo (/bin/properties/)

⸻

14) Distributed / Remote Testing (§14)
	•	Decide if you truly need it; single host often suffices with proper tuning
	•	Align JDK, JMeter, plugins, time sync (NTP) across master/workers
	•	Ensure network & bandwidth can sustain target RPS without saturation
	•	Aggregate results centrally; avoid heavy listeners on workers

⸻

15) Monitoring Server Performance (§15)
	•	Coordinate with APM/infra: CPU, memory, GC, DB, caches, queue depths, 95/99p lat
	•	Tag test runs so app metrics correlate with JMeter timestamps
	•	Capture OS/network counters on load generators (avoid generators as bottleneck)

⸻

16) HTTP Configuration & Hygiene (cross-cutting)
	•	Use HTTP Request Defaults for base URL, protocol, ports
	•	Set Connection Reuse, Keep-Alive, and HTTP Implementation as required
	•	Timeouts: connect and response MUST be set (e.g., 10s/30s) and documented

⸻

17) Scenarios & Workloads
	•	Define user mix per scenario (e.g., Browse 60%, Search 30%, Checkout 10%)
	•	Create smoke (quick, low VU), baseline, peak, soak profiles
	•	Document expected concurrency and arrival rates for each

⸻

18) Test Data & Environment
	•	Ensure idempotent data or resettable environments
	•	Seed or mock dependencies (emails, payments) to avoid external side-effects
	•	Clear test accounts/fixtures post-run if needed

⸻

19) Reporting & Analysis
	•	Generate HTML report (-e -o) and archive under /reports/<run-id>/
	•	Record run metadata: commit SHA, JMeter version, scenario, env, SLOs
	•	Analyze: p50/p90/p95/p99, errors, timeouts, saturation points, scalability curve
	•	Write a brief run summary in /reports/<run-id>/SUMMARY.md with pass/fail vs SLOs

⸻

20) CI/CD Integration
	•	Add a GitHub Actions/GitLab CI job to run smoke/baseline on PRs or nightly
	•	Upload artifacts (JTL, report) and expose key KPIs as job outputs
	•	Fail pipeline when SLO regression exceeds threshold

⸻

21) Security & Compliance
	•	Store secrets via env vars/CI secrets; never commit creds
	•	Mask sensitive data in logs/JTL
	•	Coordinate with security for auth flows and rate limits

⸻

22) Housekeeping & Quality
	•	Consistent naming convention for samplers and variables (${userId}, ${csrfToken})
	•	Limit Listeners in test plans; remove debug elements before committing
	•	Keep test plans small and modular; prefer Include Controller for reuse
	•	Document known caveats (e.g., anti-bot, WAF behavior, IP allowlists)

⸻

23) Learning Milestones (tie back to course)
	•	§1 Understand JMeter basics & goals
	•	§2 Record a clean flow and refactor
	•	§3–4 Apply proper thread groups & ramps
	•	§5–6 Handle sessions and add assertions
	•	§7–8 Modularize with controllers & realistic timers
	•	§9–12 Correlate dynamic data (Regex/JSON), advanced handling
	•	§10 Make it data-driven
	•	§11 Replace Beanshell with JSR223 Groovy
	•	§13 Run non-GUI + generate reports
	•	§14 (Optional) Distributed mode
	•	§15 Correlate with server-side monitoring
	•	Wrap with CI, reporting, and documentation

⸻

Quick “Do/Don’t”
	•	✅ Do: non-GUI runs; correlation; assertions; realistic think time; modular design; version control
	•	✅ Do: fixed timeouts; Groovy JSR223; property files; HTML reports; CI gating
	•	❌ Don’t: heavy listeners in load runs; Beanshell; hard-coded tokens; testing prod without approval; ignoring server metrics

⸻

Run Command Templates

# Smoke
jmeter -n -t jmx/smoke.jmx -l results/smoke.jtl -e -o reports/smoke

# Baseline
jmeter -n -t jmx/baseline.jmx -q bin/properties/baseline.properties \
  -l results/baseline.jtl -e -o reports/baseline


