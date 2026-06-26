# Track-2 Gap Analysis — OPI `dpu-operator` vs. full primary-datapath OVN offload + KubeVirt

## Method
Read `opiproject/dpu-operator` @ main: the CRD surface (`config/crd/bases/`),
the example CRs (`examples/`), the core reconciler
(`internal/daemon/sfc-reconciler/sfc.go`), and the vendor-plugin layout
(`internal/daemon/vendor-specific-plugins/`). Also ran the test suite to a clean
pass (`evidence/make-test-PASS.log`).

## What the operator does today
Its entire API is four CRDs under `config.openshift.io/v1`:
- **DpuOperatorConfig** — turns the operator on; example spec is just `logLevel: 0` (no datapath/network config).
- **DataProcessingUnit / DataProcessingUnitConfig** — represent and configure the DPU devices (discovery/lifecycle).
- **ServiceFunctionChain** — the heart of it: spec is a `nodeSelector` targeting DPU-side nodes
  (`dpu.config.openshift.io/dpuside: "dpu"`) plus a list of `networkFunctions`, each a container
  image. The operator schedules and chains these network-function containers onto the DPU.

Core reconcile logic is a single SFC reconciler (`internal/daemon/sfc-reconciler/sfc.go`); vendor
integration is a plugin layer (`intel-netsec`, `marvell`, `mock-vsp`, `common`) that wires each
vendor's DPU into this network-function/SFC model.

**In one line: the operator offloads network functions and chains them (SFC) onto the DPU.**

## What it does NOT do (the gap)
There is no CRD, spec field, or reconcile path for:
1. **Provisioning the full primary OVN-Kubernetes datapath on the DPU** — moving the cluster's main
   pod/VM networking datapath (OVN/OVS) onto the DPU to bypass the host CPU. The operator chains
   *additional* NF containers; it does not own or offload the *primary* datapath.
2. **KubeVirt enablement** — nothing attaches KubeVirt VMs to a DPU-offloaded fabric.

## Reference bar: NVIDIA DPF on BF3 (Track 1)
DPF offloads the **entire OVN-Kubernetes primary datapath** into BlueField-3 and lets **KubeVirt VMs
ride that offloaded fabric**. That is the capability this operator does not yet reach on other vendors.

## Track-2 deliverable (where I would contribute)
Close the gap on a second vendor (Intel/Marvell) through this operator: extend it from
network-function/SFC offload toward **full primary-datapath OVN-Kubernetes offload + KubeVirt
enablement**, contributing the missing CRDs / reconcile paths / vendor-plugin work upstream.

## Evidence
- CRDs: `config/crd/bases/` → DpuOperatorConfig, DataProcessingUnit(Config), ServiceFunctionChain
- SFC spec: `examples/sfc.yaml` (nodeSelector + networkFunctions)
- Core logic: `internal/daemon/sfc-reconciler/sfc.go`
- Vendor plugins: `internal/daemon/vendor-specific-plugins/{intel-netsec,marvell,mock-vsp,common}`
- Test suite passes: `evidence/make-test-PASS.log`
