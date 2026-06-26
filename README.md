# OPI DPU/OVS Offload — Hands-on Lab

Hands-on validation done to prepare for the LFX **OPI DPU-Accelerated OVS Offload** mentorship, focused on **Track 2: the DPU Operator**.

## What's here
- **[`gap-analysis.md`](gap-analysis.md):**  Track-2 gap analysis of `opiproject/dpu-operator`: it offloads network functions / service-function chains (`ServiceFunctionChain` CRD, `sfc-reconciler`), but does **not** provision the full primary OVN-Kubernetes datapath on the DPU or enable KubeVirt the way NVIDIA DPF does on BF3. That gap is the proposed work.
- **[`evidence/make-test-PASS.log`](evidence/make-test-PASS.log):** full log of the operator's test suite (ginkgo + envtest, 5 suites) passing on Kind (exit 0).
- **[`evidence/make-test-result.md`](evidence/make-test-result.md):** short notes: the result, and how I diagnosed/resolved a rootless-podman teardown permission issue (`kindcluster.go:110`) by running rootful.

## Summary
- Built, ran, and troubleshot the OPI `dpu-operator` test suite to a clean pass on Kind.
- Read its CRDs / reconciler / vendor-plugins to pinpoint the Track-2 gap (full primary OVN datapath + KubeVirt enablement on a second vendor).


## Scope
This lab covers the **software / control-plane side**: building and running the operator's
test suite, and analyzing the Track-2 gap from its source. The **hardware datapath offload**
(BF3 / Intel / Marvell) is the mentorship's hands-on work, to be done on real hardware during
the internship.
