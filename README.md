# econ3916-lab10-causal-logic

# Correlation & Causality: Confounders, Colliders and the Limits of Controls

## Objective
I used simulated data with known truths, plus the LaLonde job-training data, to show that adding controls can remove bias or create it, depending on where the control sits in the causal graph.

## Methodology
- Simulated three worlds (true causation, pure confounding and pure selection) and compared the correlation between D and Y in each.
- Drew a confounder, a collider and a mediator as directed graphs with networkx.
- Simulated a confounder (U drives both D and Y, true effect 1.5) and ran OLS on Y with and without U as a control.
- Simulated a collider (D and Y independent, both cause C) and ran OLS on Y with and without C as a control. I also looked at D and Y inside a slice where C is held roughly fixed.
- Built an M-bias structure (u1 drives D, u2 drives Y, both feed into W, true effect 0) and ran OLS with and without W, which is measured before treatment.
- Wrote a function, `coef_on_d()`, that regresses an outcome on D plus any list of controls and returns the coefficient on D. I used it to add u1 alongside W in the M-bias world.
- Loaded the LaLonde NSW experiment and a CPS comparison group, and compared the experimental benchmark with the naive and covariate-adjusted observational estimates.
- Built the full causal graph in networkx, listed every path between D and Y, classified each as causal, backdoor or non-causal, and searched for the smallest valid adjustment set.

## Key Findings
- The three worlds had correlations of 0.654, 0.664 and 0.632 between D and Y, but the true effects were 2.0, 0.0 and 0.0. The correlation alone could not tell them apart.
- Controlling for the confounder moved the estimate from 2.476 to 1.500, which matches the true effect of 1.5.
- Controlling for the collider gave -0.592 (p printed as 0.000) where the truth is 0. Without it, the estimate was -0.017. Inside the slice where C was held roughly fixed, the correlation between D and Y was -0.596, against -0.017 for everyone.
- In the M-bias world, the estimate was 0.009 without W and -0.191 with W, where the truth is 0. A control measured before treatment still introduced bias. When I also controlled for u1, the estimate went back to 0.006.
- In the LaLonde data, the experimental benchmark was $1,794. The naive comparison with the CPS group gave $-8,498, and adding eight covariates gave $699. The controls closed 89% of the gap, but the adjusted estimate still did not match the benchmark.
- In the graph search, the smallest valid adjustment set was {U}. Adding W alone or with U was not valid, and adding W, U and u1 together was valid.
- Whether a control helps or hurts depends on the causal structure, not on when the variable was measured. The structure has to be argued from how the data were generated, because the data alone cannot show it.
